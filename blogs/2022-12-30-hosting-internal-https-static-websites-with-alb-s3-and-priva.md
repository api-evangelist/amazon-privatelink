---
title: "Hosting Internal HTTPS Static Websites with ALB, S3, and PrivateLink"
url: "https://aws.amazon.com/blogs/networking-and-content-delivery/hosting-internal-https-static-websites-with-alb-s3-and-privatelink/"
date: "Fri, 30 Dec 2022 19:02:56 +0000"
author: "Schuyler Jager"
feed_url: "https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/feed/"
---
<p><a href="https://aws.amazon.com/s3/">Amazon Simple Storage Service (Amazon S3</a>) is a powerful platform that enables you to do various tasks. One notable feature is the ability to create a bucket with an FQDN, <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/website-hosting-custom-domain-walkthrough.html#root-domain-walkthrough-add-record-to-hostedzone">point an alias record to the bucket website endpoint</a>, and immediately get up-and-running with an HTTP static website. If you want to serve HTTPS traffic for your static website, then you can use <a href="https://aws.amazon.com/cloudfront/">Amazon CloudFront</a> to provide both caching and HTTPS certificates to your public-facing users.</p> 
<p>If your users are located inside of an Intranet or private network, then you will want to provide access to your S3 buckets using interface endpoints on <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/privatelink-interface-endpoints.html">AWS PrivateLink for S3</a>. Your users will also want to access your static website using a friendly domain name like <code>portal.example.com</code>. Custom domains will require an additional internal proxy to serve your TLS certificate. This can be achieved with an internal <a href="https://aws.amazon.com/elasticloadbalancing/application-load-balancer/">Application Load Balancer</a> (ALB).</p> 
<h2>Solution overview</h2> 
<p>This solution leverages your existing private connection to the VPC and an Internal ALB to present the TLS certificate of the custom S3 bucket domain to the end-user. The ALB leverages <a href="https://aws.amazon.com/certificate-manager/">AWS Certificate Manager</a> (ACM) to present a valid certificate for the end-user, while maintaining a secure TLS connection to the trusted Amazon S3 VPC Endpoint. This enables the use of custom domain names for your static website.</p> 
<p><img alt="AWS architectural diagram showing a group called Private VPC. Direct Connect, Site-to-Site VPN, and Client VPN provide a connection into the Private VPC which then routes to the Internal ALB. The Internal ALB uses TLS Certificate and AWS Certificate Manager to present a TLS certificate, then routes to the S3 VPC Endpoint IPs. Finally, the VPC Endpoint IPs route to the S3 Static Website on portal.example.com" class="alignnone wp-image-14623 size-full" height="962" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2023/01/06/Networking_NetCDNBlog-430-v2.jpg" width="1430" /></p> 
<h2>Walkthrough</h2> 
<p>In this walkthrough, you’ll be creating an Amazon S3 VPC Endpoint, and an Internal ALB which can be leveraged with your existing AWS connection.</p> 
<h3>Prerequisites</h3> 
<p>For this walkthrough, you should have the following prerequisites:</p> 
<ul> 
 <li>An <a href="https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fportal.aws.amazon.com%2Fbilling%2Fsignup%2Fresume&amp;client_id=signup">AWS account</a></li> 
 <li>A <a href="https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-private.html">private</a> or <a href="https://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html">imported certificate</a> for the domain imported in ACM in the Region of your choice</li> 
 <li>An <a href="https://aws.amazon.com/s3/">Amazon S3 bucket</a> named with the domain that matches your ACM certificate (such as “<code>portal.example.com</code>”). You can <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/GetStartedWithS3.html">read here to get started with Amazon S3</a>, and <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-buckets-s3.html">read about working with Amazon S3 buckets here</a>. 
  <ul> 
   <li>You <strong>do not </strong>need to enable static website hosting on the bucket, as this is only for public website endpoints. Requests to the bucket will be going through a private REST API instead.</li> 
  </ul> </li> 
 <li>A VPC with at least two private subnets</li> 
 <li>An existing Direct Connect, Site-to-Site VPN, or Client VPN connection with correct routing into your VPC. This will be referred to as your private inbound connection.</li> 
 <li>An <a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/AboutHZWorkingWith.html">private hosted zone</a> for your custom domain name</li> 
 <li>A resource that can access your VPC network, such as an Amazon Elastic Compute Cloud (Amazon EC2) instance running inside of the VPC</li> 
 <li>A static website containing an index.html entry page populated into your S3 bucket</li> 
</ul> 
<h3>Step 1: Create your Amazon S3 VPC Endpoint</h3> 
<p>To securely and privately connect the ALB to your S3 bucket, you must start by<a href="https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html"> creating an Amazon S3 VPC Endpoint.</a></p> 
<ol> 
 <li>Log in to your VPC Dashboard.</li> 
 <li>On the left-hand menu, navigate to the “Endpoints” page.</li> 
 <li>Select “Create Endpoint”.</li> 
 <li>Search for “s3” in the Service List, and select the Amazon S3 Interface Endpoint service.<img alt="Screenshot of the VPC Create Endpoints page, with S3 Interface endpoint selected" class="alignnone size-full wp-image-14308" height="291" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/12/13/Networking_NetCDNBlog-430-Screenshot1.jpg" width="977" /></li> 
 <li>Select the VPC which contains your private inbound connection, and at least two subnets to which the endpoint will belong. It’s recommended that you select subnets belonging to two or more different Availability Zones (AZs) to <a href="https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/use-fault-isolation-to-protect-your-workload.html">leverage fault isolation</a> on your interface endpoint.</li> 
 <li>Select the security group that you would like to protect the VPC Endpoint. This security group must allow access on ports 80 and 443 from the security group for your ALB at minimum. You can read more about <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html">working with security groups here</a>.</li> 
 <li>Select “Full Access” for the <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html#vpc-endpoint-policies">VPC Endpoint policy</a>. This policy makes sure that all AWS principals working in the VPC can access the VPC Endpoint for any S3 bucket. This doesn’t bypass the security policy we’ll define on the S3 bucket. However, you can choose to limit this policy to only allow access to the ALB later after you’ve created it.</li> 
 <li>Select “Create endpoint”.</li> 
 <li>Select your new VPC Endpoint ID to navigate to the new VPC Endpoint.</li> 
 <li>On the bottom tabs, go to “Subnets”.</li> 
 <li>Note the IPv4 Addresses of your VPC Endpoint, as you’ll need them later!<img alt="Screenshot of the IPv4 Addresses of the VPC Endpoint" class="alignnone size-full wp-image-14309" height="504" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/12/13/Networking_NetCDNBlog-430-Screenshot2.jpg" width="977" /></li> 
</ol> 
<h3>Step 2: Allow the VPC Endpoint to your S3 bucket</h3> 
<p>You can <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies-vpc-endpoint.html">read more about S3 bucket policies for VPC Endpoints here</a>.</p> 
<ol> 
 <li>Navigate to your S3 bucket, and go to the “Permissions” tab.</li> 
 <li>Scroll down to “Bucket policy”, and Select “Edit”.</li> 
 <li>Add your policy based on the provided documentation. For convenience, you can also use this provided policy to make sure that only your VPC Endpoint is explicitly allowed:</li> 
</ol> 
<pre><code class="lang-json">{
   "Version": "2012-10-17",
   "Id": "Policy1415115909152",
   "Statement": [
     {
       "Sid": "Access-to-specific-VPCE-only",
       "Principal": "*",
       "Action": "s3:GetObject",
       "Effect": "Allow",
       "Resource": ["arn:aws:s3:::yourbucketname",
                    "arn:aws:s3:::yourbucketname/*"],
       "Condition": {
         "StringEquals": {
           "aws:SourceVpce": "vpce-1a2b3c4d"
         }
       }
     }
   ]
}
</code></pre> 
<h3>Step 3: Set up your Internal ALB</h3> 
<p>The Internal ALB will terminate your client-facing TLS connection</p> 
<ol> 
 <li>Navigate to your AWS Console EC2 Dashboard.</li> 
 <li>On the left-hand menu, select “Load Balancers”.</li> 
 <li>Select “Create load balancer”.</li> 
 <li>Select “Create” under the “Application Load Balancer” box.</li> 
 <li>Name your ALB and pick the “internal” scheme.</li> 
 <li>Switch the listener protocol to “HTTPS”.</li> 
 <li>Select your private subnets that the ALB will serve. Select “Next”.</li> 
 <li>Select the ACM certificate that will be served to your clients. Note that it must match your static S3 bucket domain/name. Select “Next”.</li> 
 <li>Select or create a security group that will allow your existing private connection to connect to port 443 on the load balancer. Select “Next”. 
  <ol> 
   <li>If you haven’t done so already, then make sure that you update the security group for your VPC Endpoint to allow access to the ALB security group that you’ve defined here.</li> 
  </ol> </li> 
 <li>Create a new target group that will target IPs using the HTTPS protocol. Use HTTP protocol under the health checks. Under “Advanced Health Check settings”, ensure that the Port Override is set to 80 to match the HTTP protocol. <img alt="Screenshot of the Target Group page with options entered" class="aligncenter size-full wp-image-16672" height="872" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2023/05/31/Target-Group-1.png" width="1430" /></li> 
 <li>ALB <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-health-checks.html">health checks Host headers will not contain a domain name</a>, so S3 will return a non-200 HTTP response code. Add “307,405” to the health check success codes. Select “Next”.</li> 
 <li>Register the VPC Endpoint IP addresses that you noted from Step 1 into the target group. Select “Next”.<img alt="Screenshot of the VPC Endpoint IP addresses populated into the ALB target group page" class="alignnone size-full wp-image-14311" height="477" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/12/13/Networking_NetCDNBlog-430-Screenshot4.jpg" width="977" /></li> 
</ol> 
<h3>Step 4: Configure extra listener rules</h3> 
<p>The Amazon S3 PrivateLink Endpoint is a <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteEndpoints.html#WebsiteRestEndpointDiff">REST API Endpoint</a>, which means that trailing slash requests will return XML directory listings by default. To work around this, you’ll create a redirect rule to point all requests ending in a trailing slash to index.html.</p> 
<ol> 
 <li>Navigate to your Internal ALB. Select it, and open the “Listeners” tab.</li> 
 <li>On the right side of the table for the HTTPS listener, select “View/edit rules”.</li> 
 <li>Select the “+” icon near the top to enable the insertion of a new rule.</li> 
 <li>Under “IF”, select “Add Condition”, then select “Path…”.</li> 
 <li>Enter “<code>*/</code>” under the Path Value.</li> 
 <li>Under “THEN”, select “Add Action”, then select “Redirect to…”.</li> 
 <li>Enter “<code>#{port}</code>” under “Enter port”.</li> 
 <li>Pick “Custom host, path, query” under the dropdown.</li> 
 <li>Modify “Path” to “<code>/#{path}index.html</code>”.</li> 
 <li>Select “Save” in the top-right corner.<img alt="Screenshot of the ALB Listener rules with options populated as described above" class="alignnone size-full wp-image-14312" height="537" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/12/13/Networking_NetCDNBlog-430-Screenshot5.jpg" width="977" /></li> 
</ol> 
<h3>Step 5: Configure your DNS and test your ALB</h3> 
<p>Configure your on-premises or private DNS entries to point to the Internal ALB. You can use <a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html">Route53 private hosted zones</a> (PHZs) to set up a private alias record, and associate the PHZ with your VPC. You can also <a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-forwarding-inbound-queries.html">forward inbound DNS queries from on-premises to your VPC</a>.</p> 
<h3>Step 6: Test your ALB</h3> 
<p>You must use a resource with private access to your VPC to reach the Internal ALB. Connect to your resource and try navigating to your new private DNS entry. If you only have console access to your resource, then you can also use a cURL command to validate the private static website.</p> 
<h2>Cleanup</h2> 
<p>To clean up, you can delete or revert the resources created in this guide in the following order:</p> 
<ul> 
 <li>Route53 PHZ DNS entries</li> 
 <li>ALB</li> 
 <li>Load Balancer Target Group</li> 
 <li>The Amazon S3 VPC Endpoint</li> 
 <li>Any related Security Groups that you created</li> 
 <li>Your S3 bucket policy</li> 
</ul> 
<h2>Conclusion</h2> 
<p>In this post, you learned how to create a private static <a href="https://aws.amazon.com/s3/">Amazon S3</a> website with a custom domain, without having to provision any proxies on <a href="https://aws.amazon.com/ec2/">Amazon EC2</a>. This can be useful when scaling your static website to large internal user bases, while allowing them to access a friendly domain name. Finally, you will benefit from not having to manage the undifferentiated heavy lifting of upgrading, securing, or scaling Amazon EC2 proxy instances.</p> 
<p>If you want to add authentication mechanisms to your static website, then you can leverage authentication using your ALB using one of the use-cases provided in the documentation, or use <a href="https://aws.amazon.com/verified-access/">AWS Verified Access</a>.</p> 
<p><em><strong>An update was made on January 23, 2025:</strong> The post has been updated to reflect that <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteEndpoints.html">Amazon S3 Website endpoints no longer support HTTPS</a>.</em></p> 
<h2>About the Author</h2> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="schjager-wp-2.jpg"><img alt="Headshot of Schuyler Jager" class="alignleft wp-image-14301 size-full" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/12/13/schjager-wp.jpg" width="125" /></p> 
 <h3 class="lb-h4">Schuyler Jager</h3> 
 <p>Schuyler Jager is a Sr. Solutions Architect on the Canadian Central FSI Team. He brings over five years of AWS Networking experience in the start-up industry, and has modernized monolithic applications into containerized environments. In his spare time, you can find him building (and eventually flying) his experimental airplane!</p> 
</div>
