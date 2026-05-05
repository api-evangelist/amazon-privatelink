---
title: "Providing controlled internet access through centralised proxy servers using AWS Fargate and PrivateLink"
url: "https://aws.amazon.com/blogs/networking-and-content-delivery/providing-controlled-internet-access-through-centralised-proxy-servers-using-aws-fargate-and-privatelink/"
date: "Mon, 29 Aug 2022 18:08:03 +0000"
author: "Sanjay Dandeker"
feed_url: "https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/feed/"
---
<p>In this post we provide a regional solution for controlling outbound internet access to 1000s of <a href="https://aws.amazon.com/vpc/" rel="noopener noreferrer" target="_blank">Amazon Virtual Private Clouds (VPCs)</a> using <a href="https://aws.amazon.com/fargate/" rel="noopener noreferrer" target="_blank">AWS Fargate</a> and <a href="https://aws.amazon.com/privatelink/" rel="noopener noreferrer" target="_blank">AWS PrivateLink</a>. It removes the need to manage any proxy servers or to provide Layer 3 connectivity between your VPCs. It also provides an end-to-end deployment pipeline with a simple, scalable approach to managing your list of permitted URLs and proxy server configuration.</p> 
<p>Allowing access to internet URLs from your Amazon VPC is a common requirement. You may need to access software repositories, communicate with partner APIs, or download packages from trusted vendors. This is relatively simple when your resources reside in public subnets, but private subnets require the use of a Network Address Translation (NAT) Gateway or a proxy server to provide outbound internet access.</p> 
<p>Providing internet access to resources in your private subnets can be achieved through several approaches. One common approach is an Internet Gateway with a public subnet and NAT Gateway for every Amazon VPC. In highly regulated environments, this approach can cause challenges because as the AWS footprint grows, so does the number of ingress/egress points to the internet.</p> 
<p>Customers choosing to adopt a centralised internet model to overcome this problem can use <a href="https://aws.amazon.com/network-firewall/" rel="noopener noreferrer" target="_blank">AWS Network Firewall</a>, which can be deployed using a <a href="https://aws.amazon.com/blogs/networking-and-content-delivery/deployment-models-for-aws-network-firewall/" rel="noopener noreferrer" target="_blank">centralised or decentralised model</a> to control internet traffic through an inspection VPC. Although AWS Network Firewall provides a fully managed service to control access to approved domains, it acts as a ‘transparent’ proxy to route traffic to the internet. This means that the instances accessing the internet are not aware that they are behind a proxy.</p> 
<p>An explicit proxy requires ‘awareness’ of a proxy server and instances need to be configured to use a proxy server to connect to the internet. Proxy authentication can also be added to provide more granular control and restrict access to specific instances or resources in a VPC. The solution in this blog uses a fleet of <a href="http://www.squid-cache.org/Intro/" rel="noopener noreferrer" target="_blank">Squid proxies</a> running in explicit mode, requiring instances to be configured to use your proxy to connect to the internet.</p> 
<h2>The Solution Architecture</h2> 
<p>This solution is based on a fleet of opensource Squid proxies running on&nbsp;<a href="https://aws.amazon.com/ecs/" rel="noopener noreferrer" target="_blank">Amazon Elastic Container Service (ECS)</a> with AWS Fargate. Internet access is provided centrally via a NAT Gateway and an Internet Gateway deployed in the central VPC. Amazon ECS uses a <a href="https://aws.amazon.com/elasticloadbalancing/network-load-balancer/" rel="noopener noreferrer" target="_blank">Network Load Balancer (NLB)</a> configured as an endpoint service to make the solution available to ‘spoke’ VPCs. Interface endpoints are deployed into the ‘spoke’ (application) VPCs to enable resources inside these VPCs to use the deployed endpoint as its proxy server. Some key points to note about this solution are:</p> 
<ul> 
 <li>L3 network connectivity is NOT required between the central hub and ‘spoke’ account VPCs, as it leverages AWS PrivateLink.</li> 
 <li>The allowlist is managed centrally and can be version controlled in your git-based repository.</li> 
 <li>The solution only uses AWS managed services, so there are no Amazon Elastic Compute Cloud (EC2) instances or cluster nodes to manage.</li> 
 <li>The solution can scale to 1000s of VPCs and ‘proxy endpoints’.</li> 
 <li>The ECS service automatically scales the required number of tasks based on the CPU load of the proxy service.</li> 
 <li>Squid proxy is configured to act as an explicit proxy, so instances must be configured to be ‘proxy aware’ to access the internet.</li> 
</ul> 
<div class="wp-caption alignnone" id="attachment_13033" style="width: 1651px;">
 <img alt="Figure 1: Solution Overview" class="wp-image-13033 size-full" height="764" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-1.png" width="1641" />
 <p class="wp-caption-text" id="caption-attachment-13033">Figure 1: Solution Overview</p>
</div> 
<p>The endpoint service can be configured to permit individual AWS accounts to use the central proxy service. An optional private DNS name can also be configured for the endpoint service to allow the ‘spoke’ accounts to use a common hostname as their proxy. This allows hosts or applications across different accounts and ‘spoke’ VPCs to be configured to use a common proxy hostname, which resolves to the unique local endpoint within each VPC.</p> 
<h3>ECS on Fargate running the Squid Proxy Service</h3> 
<p>The Squid proxy service uses AWS Fargate with Amazon ECS to run a scalable fleet of proxy servers behind a Network Load Balancer. The container image running the squid proxy contains the squid.conf configuration file and the associated allowlist.txt file, which maintains the list of the domains permitted by the proxy service. Both of these files can be updated in the git-based repo to trigger the deployment pipeline described below.</p> 
<p>The Network Load Balancer used by ECS is configured as an <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-share-your-services.html" rel="noopener noreferrer" target="_blank">Endpoint Service powered by AWS PrivateLink</a>. With AWS PrivateLink, service consumers create interface VPC endpoints to connect to endpoint services that are hosted by service providers. The hub account for our solution is the ‘Service Provider’ and the AWS account where the proxy endpoint is deployed is the ‘Service Consumer’.</p> 
<h3>The Deployment Pipeline</h3> 
<p>On initial deployment of the solution, <a href="https://aws.amazon.com/codepipeline/" rel="noopener noreferrer" target="_blank">AWS CodePipeline</a> uses <a href="https://aws.amazon.com/codebuild/" rel="noopener noreferrer" target="_blank">AWS CodeBuild</a> to build the Squid proxy container using the Dockerfile and configuration files from the <a href="https://aws.amazon.com/codecommit/" rel="noopener noreferrer" target="_blank">AWS CodeCommit</a> repo. The container image is then pushed to the <a href="https://aws.amazon.com/ecr/" rel="noopener noreferrer" target="_blank">Amazon Elastic Container Registry (ECR)</a> repo, and <a href="https://aws.amazon.com/codedeploy/" rel="noopener noreferrer" target="_blank">AWS CodeDeploy</a> is used to deploy the container image to ECS. Any subsequent updates to the squid.conf or allowlist.txt files in CodeCommit will trigger the deployment pipeline to rebuild a new container image and deploy it to ECS using a rolling update to replace the running containers one at a time. The pipeline will complete once all of the running containers have been replaced with a new image.</p> 
<h3>The Service Consumer (Spoke Accounts)</h3> 
<p>AWS PrivateLink enables private connectivity between VPCs. The service consumer (the application ‘spoke’ accounts) for this solution will have a ‘proxy endpoint’ deployed inside the VPC which permits internet access to the list of domains permitted in the central account.</p> 
<p>The proxy endpoint can be deployed to 1000s of VPCs (within the same region) across any AWS account that has been permitted to use the endpoint service in the list of principals. The permitted principals can be a specific user or role, but for our solution we will leverage the account Amazon Resource Name (ARN) to permit any principal in our consumer account to create an interface endpoint. For more information, review the <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/configure-endpoint-service.html" rel="noopener noreferrer" target="_blank">Configure an endpoint service</a> document.</p> 
<h2>Deploying the Solution</h2> 
<p>The solution can be deployed in 4 steps to get you up and running:</p> 
<ul> 
 <li><strong>Step 1:</strong> Create a CodeCommit repo and stage the Dockerfile and associated configuration files.</li> 
 <li><strong>Step 2:</strong> Create a <a href="https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using-service-linked-roles.html" rel="noopener noreferrer" target="_blank">Service Linked Role for ECS</a>.</li> 
 <li><strong>Step 3:</strong> Deploy the solution via the Cloudformation template provided. This solution deploys everything required for the central hub account in the solution overview</li> 
 <li><strong>Step 4:</strong> Create your VPC endpoint in your spoke account along with an EC2 instance for testing.</li> 
</ul> 
<h3>Step 1: Create your CodeCommit repo</h3> 
<p>Create your CodeCommit repo, and stage the configuration files required to build the solution. The configuration files can be found in the following <strong><a href="https://github.com/aws-samples/centralised-egress-proxy" rel="noopener noreferrer" target="_blank">Github Repository</a></strong>.</p> 
<ol> 
 <li>Navigate to the <a href="https://console.aws.amazon.com/codesuite/codecommit/repositories" rel="noopener noreferrer" target="_blank">AWS CodeCommit console</a>, then choose Create repository.</li> 
 <li>Enter a name, optional description, and then choose Create. You will be taken to your repository after creation.</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13039" style="width: 1034px;">
 <img alt="Figure 2: AWS CodeCommit Repository Settings" class="wp-image-13039 size-large" height="715" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-2-1024x715.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13039">Figure 2: AWS CodeCommit Repository Settings</p>
</div> 
<ol start="3"> 
 <li>Add the 4 configuration files from the <a href="https://github.com/aws-samples/centralised-egress-proxy" rel="noopener noreferrer" target="_blank">Github Repository</a> directly from the console or by cloning the repository to your local computer, creating commits, and uploading the content to the new repository.</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13040" style="width: 1034px;">
 <img alt="Figure 3: AWS CodeCommit Repository" class="wp-image-13040 size-large" height="392" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-3-1024x392.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13040">Figure 3: AWS CodeCommit Repository</p>
</div> 
<h3>Step 2: Create the ECS Service Linked Role</h3> 
<p>If your AWS account does not have the ECS Service link role: <code>AWSServiceRoleForECS</code>, you will need to create the role using the following CLI command:</p> 
<div class="hide-language"> 
 <pre><code class="lang-bash">aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com</code></pre> 
</div> 
<p>For additional information, see the <a href="https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using-service-linked-roles.html" rel="noopener noreferrer" target="_blank">Service-linked roles for Amazon ECS</a> guide.</p> 
<h3>Step 3: Deploy the Proxy Solution using CloudFormation</h3> 
<p>Use <a href="https://aws.amazon.com/cloudformation/" rel="noopener noreferrer" target="_blank">AWS CloudFormation</a> to provision the required resources for the hub account. Select the Launch Stack button below to open the CloudFormation console and create a stack from the template. Then, follow the on-screen instructions.</p> 
<p><a href="https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/quickcreate?templateURL=https://awsiammedia.s3.amazonaws.com/public/sample/522-centralising-internet-access/centralised_egress_proxy.yaml" rel="noopener noreferrer" target="_blank"><img alt="" class="aligncenter wp-image-7716 size-medium" height="56" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2021/06/04/launch-stack-300x56.png" width="300" /></a></p> 
<p>CloudFormation will create the following resources in your AWS account:</p> 
<ol> 
 <li>A Virtual Private Cloud (VPC) with an Internet Gateway</li> 
 <li>Public and Private subnets in the VPC</li> 
 <li>Two route tables, one for the private subnets and another for the public subnets</li> 
 <li>An ECS service using AWS Fargate</li> 
 <li>A deployment pipeline consisting of: 
  <ul> 
   <li>CodeBuild for building the docker container</li> 
   <li>CodeDeploy for deploying the container image to ECS</li> 
   <li>CodePipeline for orchestrating the end to end build and deployment</li> 
  </ul> </li> 
 <li>An ECR Repository for the container image</li> 
 <li>The required IAM roles and policies</li> 
 <li>Cloudwatch Events and Log Groups</li> 
</ol> 
<p>The following parameters are required&nbsp;to deploy the CloudFormation stack:</p> 
<ol> 
 <li>CodeCommit Repo &amp; Branch name from Step 1</li> 
 <li>The Number of AZs to deploy the solution</li> 
 <li>Allowed Principles for the Endpoint Service (this will be the AWS account ARN for your service consumer account (ie: where you will deploy the proxy endpoint), eg: arn:aws:iam::&lt;aws-account-id&gt;:root</li> 
</ol> 
<h4>Using Private DNS (Optional Step)</h4> 
<p>When you create a VPC endpoint, AWS generates an endpoint-specific DNS hostname that you can use to communicate with the endpoint service. For example: vpce-xxxxxxxxxxxxxxxx.vpce-svc-yyyyyyyyyyyyyyy.eu-west-2.vpce.amazonaws.com.</p> 
<p>By default, this is the hostname that your applications can use to proxy internet traffic. As this hostname is unique for every VPC endpoint deployed, using the AWS generated hostname may not be preferable, as the proxy server you direct applications to will differ for every VPC.</p> 
<p>An alternative option is to enable Private DNS for your endpoint service and use this private DNS name as the proxy server hostname in every ‘spoke’ account. Enabling private DNS creates a managed AWS Route 53 record for your VPC that resolves the private DNS name to the IPs of the endpoint inside your VPC.</p> 
<p>To use the private DNS feature, you must have a registered domain name to verify domain ownership before you can permit your ‘spoke’ accounts to use this hostname. After the domain ownership verification completes, consumers can access the endpoint by using the private DNS name.</p> 
<p>As private DNS can be optionally enabled for new or existing endpoint services, the following instructions provide a guide for adding private DNS to the endpoint service after it has been deployed via the CloudFormation template in the previous section:</p> 
<ol> 
 <li>Select your Endpoint Service in the VPC console</li> 
 <li>Select Actions → Modify private DNS name</li> 
 <li>Enable “Associate a private DNS name with the service”</li> 
 <li>Enter your private DNS name eg: aws.ssproxy.co.uk</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13043" style="width: 1034px;">
 <img alt="Figure 4: VPC endpoint details -pending verification" class="wp-image-13043 size-large" height="272" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-4-1024x272.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13043">Figure 4: VPC endpoint details -pending verification</p>
</div> 
<p>The domain verification status will show as ‘pending verification’ and you will be required to use the domain verification name and value shown in the screenshot above to create a TXT record with your DNS provider to verify ownership of the record. To learn more, view the <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html" rel="noopener noreferrer" target="_blank">AWS PrivateLink documentation</a>.</p> 
<p>After creating the verification TXT record, you will need to wait up to 10 minutes before performing the following actions:</p> 
<ol> 
 <li>Select your Endpoint Service in the VPC console</li> 
 <li>Select Actions → Verify domain ownership for private DNS name</li> 
 <li>Confirm verification</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13044" style="width: 1012px;">
 <img alt="Figure 5: VPC endpoint domain ownership verification" class="wp-image-13044 size-full" height="375" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-5.png" width="1002" />
 <p class="wp-caption-text" id="caption-attachment-13044">Figure 5: VPC endpoint domain ownership verification</p>
</div> 
<p>Once verification has been completed, your Domain verification status should change to ‘Verified’.</p> 
<div class="wp-caption alignnone" id="attachment_13045" style="width: 1034px;">
 <img alt="Figure 6: VPC endpoint details – verified domain" class="wp-image-13045 size-large" height="272" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-6-1024x272.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13045">Figure 6: VPC endpoint details – verified domain</p>
</div> 
<h3>Step 4: Deploy the proxy endpoint in your application (spoke) account</h3> 
<p>The proxy endpoint is deployed into every private VPC where you want to use the proxy service. You will need an existing VPC with an EC2 instance for testing. To deploy the endpoint, follow these steps:</p> 
<ol> 
 <li>Log in to the consumer AWS account where you will deploy your proxy endpoint. The account ARN should have been permitted when you ran the CloudFormation stack. If it wasn’t, you can re-deploy the stack, adding the account ARN in the AllowedPrincipalsList parameter.</li> 
 <li>In the VPC console, go to endpoints and ‘Create Endpoint’.</li> 
 <li>Select ‘Other endpoint services’ and enter the Service Name for the endpoint service provided in the CloudFormation outputs e.g. com.amazonaws.vpce.eu-west-2.vpce-svc-xxxxxxxxxxxxxxxx and click verify.</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13046" style="width: 1034px;">
 <img alt="Figure 7: VPC endpoint creation – Service Name verification" class="wp-image-13046 size-large" height="573" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-7-1024x573.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13046">Figure 7: VPC endpoint creation – Service Name verification</p>
</div> 
<ol start="4"> 
 <li>Select your VPC and subnets where the endpoint will be deployed. See the Additional Considerations section for more information about AZ alignment requirements between the provider and consumer accounts.</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13048" style="width: 1034px;">
 <img alt="Figure 8: VPC endpoint creation – Subnet selection" class="wp-image-13048 size-large" height="264" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-8-1024x264.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13048">Figure 8: VPC endpoint creation – Subnet selection</p>
</div> 
<ol start="5"> 
 <li>If you enabled private DNS for your endpoint service in the provider account, you can enable the DNS name in the Additional settings section.</li> 
</ol> 
<div class="wp-caption alignnone" id="attachment_13049" style="width: 1034px;">
 <img alt="Figure 9: VPC endpoint creation – Enable DNS name" class="wp-image-13049 size-large" height="421" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Centralised-proxy-blog-figure-9-1024x421.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-13049">Figure 9: VPC endpoint creation – Enable DNS name</p>
</div> 
<ol start="6"> 
 <li>Add a security group to the endpoint that permits your VPC CIDR or test EC2 instance to communicate with the endpoint via the TCP protocol on port 3128.</li> 
 <li>Click Create Endpoint.</li> 
</ol> 
<h2>Testing the proxy</h2> 
<p>Deploy a test EC2 instance (Linux) to the VPC where your proxy endpoint was deployed in Step 4. You need to use Session Manager to connect to the instance, as the consumer VPC is private and therefore has no ingress route for SSH. Follow the user guide <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html" rel="noopener noreferrer" target="_blank">here</a> to use Session Manager.</p> 
<p>From the EC2 terminal, configure the instance to use your proxy using the following export commands:</p> 
<p><code>$ export http_proxy=http://&lt;Proxy-DOMAIN&gt;:&lt;Proxy-Port&gt;</code></p> 
<p><code>$ export https_proxy=http://&lt;Proxy-DOMAIN&gt;:&lt;Proxy-Port&gt;</code></p> 
<p>eg without private DNS:</p> 
<p><code>$ export http_proxy=http://vpce-0e92264eb3c2d222d-166ar2fp.vpce-svc-0407aa3b85114a062.eu-west-2.vpce.amazonaws.com:3128</code></p> 
<p><code>$ export&nbsp;https_proxy=http://vpce-0e92264eb3c2d222d-166ar2fp.vpce-svc-0407aa3b85114a062.eu-west-2.vpce.amazonaws.com:3128</code></p> 
<p>eg with Private DNS:</p> 
<p><code>$ export http_proxy=http://aws.ssproxy.uk:3128</code></p> 
<p><code>$ export&nbsp;https_proxy=http://aws.ssproxy.uk:3128</code></p> 
<p>You can use curl to test that you are able to connect to URLs permitted by your allow list but not to any other URLs. For example:</p> 
<p><code>curl https://aws.amazon.com&nbsp; loads page.</code></p> 
<p><code>curl https://www.microsoft.com&nbsp;returns 403 error or&nbsp;&lt;!-- ERR_ACCESS_DENIED</code></p> 
<h2>Clean up</h2> 
<p>Don’t forget to clean up the resources to avoid unwanted charges. To delete the resources deployed in this blog, you can follow these steps:</p> 
<ol> 
 <li>Navigate to the VPC endpoints section in the ‘spoke’ account and ‘delete’ the proxy endpoint.</li> 
 <li>Delete the CloudFormation stack in the hub account.</li> 
 <li>Delete the files and CodeCommit repo in the hub account.</li> 
</ol> 
<h2>Conclusion</h2> 
<p>In this post we have shown you one of the options available in AWS to build a centralised internet egress solution across a multi-account environment. The solution is based on a fully ‘serverless’ and managed infrastructure solution using AWS Fargate and a deployment pipeline to manage your URL allow list. The solution is scalable across 1000s of AWS accounts and provides granular control and additional security controls, such as proxy-based authentication.</p> 
<h2>About the Authors</h2> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="Sanjay-Dandeker.jpeg"><img alt="Sanjay Dandeker" class="alignleft wp-image-5363 size-full" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Sanjay-Dandeker.jpeg" width="95" /></p> 
 <h3 class="lb-h4">Sanjay Dandeker</h3> 
 <p>Sanjay is a Principal Partner Solutions Architect at AWS. He works with customers and partners in the Financial Services industry to build solutions and capabilities that help highly regulated customers as they move to the cloud.</p> 
</div> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="Saurabh-Kothari.jpg"><img alt="Saurabh Kothari" class="alignleft size-full wp-image-5363" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/08/10/Saurabh-Kothari.jpg" width="95" /></p> 
 <h3 class="lb-h4">Saurabh Kothari</h3> 
 <p>Saurabh is Senior Cloud Infrastructure and Application Architect at AWS. He works closely with customer teams in the Financial Services industry to help them build scalable and secure solutions in the AWS cloud.</p> 
</div>
