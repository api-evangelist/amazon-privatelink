---
title: "Establish private connectivity between Salesforce and your on-premises network using AWS Direct Connect"
url: "https://aws.amazon.com/blogs/networking-and-content-delivery/establish-private-connectivity-between-salesforce-and-your-on-premises-network-using-aws-direct-connect/"
date: "Mon, 05 Aug 2024 13:15:48 +0000"
author: "Alan Peaty"
feed_url: "https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/feed/"
---
<p><a href="https://www.salesforce.com/">Salesforce</a> is an <a href="https://partners.amazonaws.com/partners/001E000001A16lmIAB/Salesforce">AWS Partner</a> and a trusted global leader in customer relationship management (CRM). <a href="https://www.salesforce.com/products/platform/hyperforce/">Hyperforce</a> is the next-generation Salesforce architecture, built on <a href="https://aws.amazon.com/">Amazon Web Services (AWS)</a>.</p> 
<p>When business applications developed on Hyperforce are integrated with on-premises systems, traffic in both directions will flow over the internet. For customers in heavily regulated industries such as the public sector and financial services, programmatic access of the Salesforce APIs hosted on Hyperforce from on-premises systems is required to traverse a private connection. Conversely, accessing on-premises systems from business applications running in Hyperforce is required to use a private connection.</p> 
<p>In this post, we describe how <a href="https://aws.amazon.com/directconnect/">AWS Direct Connect</a> and <a href="https://aws.amazon.com/transit-gateway/">AWS Transit Gateway</a> can be used in conjunction with <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_overview.htm&amp;type=5">Salesforce Private Connect</a> to facilitate the private, bidirectional exchange of organizational data.</p> 
<h2>Architectural overview</h2> 
<p>In a <a href="https://aws.amazon.com/blogs/apn/how-to-access-salesforce-hyperforce-securely-and-reliably-with-aws-direct-connect/">previous post</a>, you learned how to use AWS Direct Connect to establish a dedicated, managed, and reliable connection to Hyperforce. The approach used a <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/WorkingWithVirtualInterfaces.html">public virtual interface</a> to facilitate connectivity to public Hyperforce endpoints.</p> 
<p>The approach in this post demonstrates the use of a private or transit virtual interface to establish a dedicated, private connection to Hyperforce using Salesforce Private Connect.</p> 
<h3>Approach</h3> 
<p>AWS Direct Connect is set up between the on-premises network and a virtual private cloud (VPC) residing inside a customer’s AWS account to provide connectivity from the on-premises network to AWS.</p> 
<p>The exchange of data between the customer VPC and Salesforce’s transit VPC is facilitated through the Salesforce Private Connect feature, based on <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html">AWS PrivateLink</a> technology. AWS PrivateLink allows consumers to securely access a service located in a service provider’s VPC as if it were located in the consumer’s VPC. Using Salesforce Private Connect, traffic is routed through a fully managed network connection between your Salesforce organization and your VPC instead of over the internet.</p> 
<p>The following table shows the definitions of inbound and outbound connections in the context of Salesforce Private Connect:</p> 
<table style="height: 282px; border: 1px solid #d5dbdb; border-collapse: collapse; padding: 15px;" width="696"> 
 <tbody> 
  <tr> 
   <th style="border: 1px solid #d5dbdb; padding: 15px;">Direction</th> 
   <th style="border: 1px solid #d5dbdb; padding: 15px;">Inbound</th> 
   <th style="border: 1px solid #d5dbdb; padding: 15px;">Outbound</th> 
  </tr> 
  <tr> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">Description</td> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">Traffic that flows into Salesforce</td> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">Traffic that flows out of Salesforce</td> 
  </tr> 
  <tr> 
   <td rowspan="2" style="border: 1px solid #d5dbdb; padding: 15px;">Use cases</td> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">AWS to Salesforce</td> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">Salesforce to AWS</td> 
  </tr> 
  <tr> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">On-premises network to Salesforce</td> 
   <td style="border: 1px solid #d5dbdb; padding: 15px;">Salesforce to on-premises network</td> 
  </tr> 
 </tbody> 
</table> 
<p>This pattern can only be adopted for Salesforce services supported by Salesforce Private Connect, such as <a href="https://www.salesforce.com/products/experience-cloud/">Experience Cloud</a>, <a href="https://www.salesforce.com/financial-services/cloud/">Financial Services Cloud</a>, <a href="https://www.salesforce.com/products/health-cloud/">Health Cloud</a>, <a href="https://www.salesforce.com/products/salesforce-platform/">Platform Cloud</a>, <a href="https://www.salesforce.com/sales/cloud/">Sales Cloud</a>, and <a href="https://www.salesforce.com/service/cloud/">Service Cloud</a>. Check the latest <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_considerations.htm&amp;type=5">Salesforce documentation</a> for the specific Salesforce services that are supported. Furthermore, this architecture is only applicable to the inbound and outbound exchange of data and does not pertain to the access of the Salesforce UI.</p> 
<p>The following diagram shows the end-to-end solution of how private connectivity is facilitated bidirectionally. In this example, on-premises servers located on the 10.0.1.0/26 network are required to privately exchange data with applications running on the Hyperforce platform.</p> 
<div class="wp-caption aligncenter" id="attachment_22999" style="width: 1034px;">
 <img alt="Diagram shows how AWS Direct Connect and Salesforce Private Connect is used to establish private, bidirectional connectivity between on-premises systems and Salesforce." class="wp-image-22999 size-full" height="477" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2024/08/06/salesforce_private_link_overview_v0.6_figure_1.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-22999">Figure 1: Using AWS Direct Connect and Salesforce Private Connect to establish private, bidirectional connectivity</p>
</div> 
<h2>Prerequisites</h2> 
<p>In order to implement this solution, the following prerequisites are required on both the Salesforce and AWS side.</p> 
<h3>Salesforce</h3> 
<ul> 
 <li>Use of a <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_considerations.htm&amp;type=5">supported Salesforce service</a> (for inbound connectivity to Salesforce API).</li> 
 <li><a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_considerations.htm&amp;language=en_US&amp;type=5">Salesforce Private Connect license</a>. Each Salesforce Private Connect license allows for one provisioned connection in each direction, inbound and outbound.</li> 
 <li>Salesforce organization configured with <a href="https://help.salesforce.com/s/articleView?id=sf.domain_name_overview.htm&amp;language=en_US&amp;type=5">My Domain</a>.</li> 
</ul> 
<p>Refer to Salesforce documentation for detailed requirements on <a href="https://help.salesforce.com/s/articleView?id=000386897&amp;type=1">migrating your Salesforce organization to Hyperforce</a>.</p> 
<h4>AWS</h4> 
<ul> 
 <li>AWS account</li> 
 <li><a href="https://aws.amazon.com/directconnect/">AWS Direct Connect</a> using a private or transit virtual interface (VIF)</li> 
 <li><a href="https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html">Transit Gateway</a></li> 
</ul> 
<h2>Network flow between on-premises data center and Salesforce API</h2> 
<p>The following figure shows how both inbound and outbound traffic flows through the architecture.</p> 
<div class="wp-caption aligncenter" id="attachment_22875" style="width: 1034px;">
 <img alt="The diagram shows how network traffic flows between an on-premises data center and Salesforce." class="wp-image-22875 size-full" height="477" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2024/07/31/salesforce_private_link_overview_v0.6_figure_2.png" width="1024" />
 <p class="wp-caption-text" id="caption-attachment-22875">Figure 2: Network flow between on-premises data center and Salesforce</p>
</div> 
<h3>Inbound</h3> 
<ol> 
 <li>An application running on premises needs to reach the Salesforce API privately. Traffic from on-premises systems to AWS traverse Direct Connect using either a transit or private VIF.</li> 
 <li>The traffic is targeted at the VPC endpoint residing in the customer’s VPC using a custom DNS record. This record resolves to the IP addresses of the VPC endpoint created for you.</li> 
 <li>The inbound VPC endpoint is associated with the PrivateLink of Salesforce’s inbound Private Connect configuration, which provides private access to supported Salesforce APIs. The private endpoint also supports authentication flows by facilitating access to the Auth2 token endpoint (<code>/services/oauth2/token</code>) required to programmatically retrieve a bearer token. The token is used in subsequent interactions with the Salesforce REST API (for example, <code>/services/data/v60.0/sobjects/Case</code> for case objects). Refer to Salesforce documentation <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_inbound_aws.htm&amp;language=en_US&amp;type=5">Establish an Inbound Connection with AWS</a> for the steps required to set up the Salesforce configuration.</li> 
</ol> 
<h3>Outbound</h3> 
<ol> 
 <li>An application running on Salesforce needs to reach an on-premises resource privately. Traffic from Salesforce leaves through the Salesforce transit VPC and reaches the customer’s internal Network Load Balancer, which is part of the Salesforce outbound Private Connect configuration. Refer to Salesforce documentation <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_outbound_aws.htm&amp;type=5">Establish an Outbound Connection with AWS</a> for the steps required to set up the Salesforce configuration.</li> 
 <li>The Network Load Balancer is configured with a target group consisting of one or more IP addresses of on-premises resources. Traffic is forwarded to these IP addresses through the target group.</li> 
 <li>The Transit Gateway routes traffic back to the on-premises resources over the AWS Direct Connect connection.</li> 
</ol> 
<h2>Considerations</h2> 
<p>Before you set up the private, bidirectional exchange of organizational data with AWS Direct Connect, AWS Transit Gateway, and Salesforce Private Connect, review these considerations.</p> 
<h3>Resiliency</h3> 
<p>We recommend that you set up <a href="https://aws.amazon.com/directconnect/resiliency-recommendation/">multiple AWS Direct Connect connections</a> to provide resilient communication paths to the AWS Region, especially if the traffic between your on-premises resources and Hyperforce is business-critical. Refer to the AWS documentation on how to achieve <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/high_resiliency.html">high</a> and <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/maximum_resiliency.html">maximum</a> resiliency for your AWS Direct Connect deployments.</p> 
<p>For inbound traffic flow, we recommend that the <a href="https://docs.aws.amazon.com/whitepapers/latest/aws-privatelink/creating-highly-available-endpoint-services.html">VPC endpoint</a> is configured across Availability Zones for high availability. Configure customer DNS records for the Salesforce API with IP addresses associated with the VPC endpoint and implement the DNS failover or load-balancing mechanism on the customer side.</p> 
<p>For outbound traffic flow, we recommend that you configure your Network Load Balancer with two or more Availability Zones for high availability.</p> 
<h3>Security</h3> 
<p>For inbound traffic flow, <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_inbound_aws.htm&amp;type=5">source IP addresses</a> used by the incoming connection are displayed in the Salesforce Private Connect inbound configuration. We recommend that these IP ranges be used in Salesforce configurations that permit the enforcement of source IP. Refer to the Salesforce documentation <a href="https://help.salesforce.com/s/articleView?id=sf.connected_app_edit_ip_ranges.htm&amp;type=5">Restrict Access to Trusted IP Ranges for a Connected App</a> to learn how you can use these IP ranges can to control access to the Salesforce APIs.</p> 
<p>You access Salesforce APIs using an encrypted TLS connection. AWS Direct Connect also offers a number of <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/encryption-in-transit.html">additional data in transit encryption options</a>, including support for <a href="https://docs.aws.amazon.com/vpn/latest/s2svpn/private-ip-dx.html">private IP VPNs over AWS Direct Connect</a> and <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/MACsec.html">MAC security</a>. An IP virtual private network (VPN) encrypts end-to-end traffic using an IPsec VPN tunnel, while MAC Security (MACsec) provides point-to-point encryption between devices.</p> 
<p>For outbound traffic flow, we recommend that you configure <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-tls-listener.html">TLS listeners</a> on your Network Load Balancers to ensure that traffic to the Network Load Balancer is encrypted.</p> 
<h3>Cost optimization</h3> 
<p>If your use case is to solely facilitate access to Salesforce, you can use a virtual private gateway and a private VIF instead to optimize deployment costs. However, if you plan to implement a hub-spoke network transit hub interconnecting multiple VPCs, we recommend the use of a transit gateway and a transit VIF for a more scalable approach. Refer to the <a href="https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/aws-direct-connect.html">Amazon Virtual Private Cloud Connectivity Options whitepaper</a> and <a href="https://docs.aws.amazon.com/directconnect/latest/UserGuide/limits.html">AWS Direct Connect Quotas</a> for the pros and cons of each approach.</p> 
<h2>Conclusion</h2> 
<p>Salesforce and AWS continue to innovate together to provide multiple connectivity approaches to meet customer requirements. This post demonstrated how AWS Direct Connect can be used in conjunction with <a href="https://help.salesforce.com/s/articleView?id=sf.private_connect_overview.htm&amp;type=5">Salesforce Private Connect</a> to secure end-to-end exchanges of data in industries where the use of the internet is not an option.</p> 
<p>Reach out to your Salesforce or AWS representative or an <a href="https://aws.amazon.com/directconnect/partners/">AWS Direct Connect Partner</a> to learn more.</p> 
<p><em>A correction was made on August 6, 2024: An earlier version of this post included an incorrect diagram for Figure 1. This diagram has been updated.</em></p> 
<h3>About the Authors</h3> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="NetCDNBlog-1224_appeaty.jpeg"><img alt="Alan Peaty" class="alignleft wp-image-1288 size-thumbnail" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2024/07/31/NetCDNBlog-1224_appeaty.jpeg" width="120" /></p> 
 <h3 class="lb-h4">Alan Peaty</h3> 
 <p style="color: #879196; font-size: 1rem;">Alan is a Senior Partner Solutions Architect helping Global Systems Integrators (GSIs), Global Independent Software Vendors (GISVs) and their customers adopt AWS services. Prior to joining AWS, Alan worked as an architect at Systems Integrators such as IBM, Capita, and CGI. Outside of work, Alan is a keen runner who loves to hit the muddy trails of the English countryside, and an IoT enthusiast.</p> 
</div> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="NetCDNBlog-1224_rminchr.jpg"><img alt="Christian Ramirez" class="alignleft wp-image-1288 size-thumbnail" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2024/07/31/NetCDNBlog-1224_rminchr.jpg" width="120" /></p> 
 <h3 class="lb-h4">Christian Ramirez</h3> 
 <p style="color: #879196; font-size: 1rem;">Christian is an AWS Partner Solutions Architect working with Salesforce in the Public Sector. Beyond work, Christian finds solace in hiking and cycling, connecting with nature and maintaining a balanced lifestyle.</p> 
</div> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="NetCDNBlog-1224_AbhiramGajjala.jpeg"><img alt="Abhiram Gajjala" class="alignleft wp-image-1288 size-thumbnail" height="120" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2024/07/31/NetCDNBlog-1224_AbhiramGajjala.jpeg" width="120" /></p> 
 <h3 class="lb-h4">Abhiram Gajjala (Guest)</h3> 
 <p style="color: #879196; font-size: 1rem;">Abhiram is the Director of Engineering at Salesforce, where he plays a key role in the Hyperforce Networking Group. He leads the development of innovative software connectivity products, including Salesforce Private Connect. With expertise in cloud networking and enterprise solutions, Abhiram drives initiatives that enhance secure connectivity and optimize performance for Salesforce customers.</p> 
</div>
