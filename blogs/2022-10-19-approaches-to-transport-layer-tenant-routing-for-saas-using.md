---
title: "Approaches to Transport Layer Tenant Routing for SaaS using AWS PrivateLink"
url: "https://aws.amazon.com/blogs/networking-and-content-delivery/transport-layer-tenant-routing-for-saas-using-aws-privatelink/"
date: "Wed, 19 Oct 2022 18:05:51 +0000"
author: "Mark Promnitz"
feed_url: "https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/feed/"
---
<p>In today’s ecosystem, Software as a Service (SaaS) offerings are primarily delivered in a low friction, service-centric approach over the Internet. These services are often mobile applications or websites delivered via a Content Delivery Network (CDN), such as <a href="https://aws.amazon.com/cloudfront/">Amazon CloudFront</a>, that in turn issues requests to the backend SaaS platform. As a SaaS provider, your SaaS solution is the main customer touch point for their core SaaS offering (excluding your Marketing, Sales, and Customer Success organisations for the point of this post) and we can address tenant identification, isolation, and routing using our usual Layer 7 (Application Layer) suspects (such as JSON Web Tokens (JWT) and HTTP Headers).</p> 
<p>However, not all SaaS offerings are the same, and increasingly SaaS businesses are finding the need to meet their customers “where they are”, by enabling private integration between the SaaS offering and your customer’s IT environment. This provides great security and integration results for end customers. However, it also presents challenges for SaaS businesses, including addressing security and integration challenges between the SaaS offering’s AWS environment and your customer’s IT environment. A prominent example of this is Classless Inter-Domain Routing (CIDR) collisions between (RFC 1918) private addresses. Your SaaS application, deployed into a Virtual Private Cloud (VPC) is built on a CIDR range (e.g., 10.0.0.0/16). The odds are that one or more of your customers will clash with either yourself or another customer and a network routing challenge will ensue. If you’re having this exact challenge, then check out our post on <a href="https://aws.amazon.com/blogs/networking-and-content-delivery/connecting-networks-with-overlapping-ip-ranges/">Connecting Networks with Overlapping IP Ranges</a>.</p> 
<p>Now imagine that you are providing a Database as a Service offering that has a functional requirement to securely accept private Transport Layer (Layer 4) connections to your platform while still providing the isolation and tenancy routing capabilities that your customers expect. When working with Layer 4 connections, we’re often constrained by the communication protocols in use. Furthermore, we don’t immediately have the <a href="https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/identity-and-access-management.html">SaaS Tenant Identifiers</a> that SaaS businesses routinely use to route tenants to the correct back-end infrastructure (e.g., commonly supplied using HTTP Headers or JWT).</p> 
<p>To address this, we launched <a href="https://aws.amazon.com/privatelink/">AWS PrivateLink</a> in 2017. PrivateLink is a highly available, scalable technology that enables you to privately connect your VPC to services as if they’re located within your VPC.</p> 
<p>When designing SaaS offerings, you must evaluate and decide whether resources should be <a href="https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/pool-isolation.html">pooled</a> (shared) or <a href="https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/silo-isolation.html">siloed</a> (not shared) between tenants based on the requirements of your SaaS offering. In this post, I’ll discuss a few approaches and what you should consider when architecting your SaaS offering for tenant routing and the delivery of Layer 4 solutions via PrivateLink.</p> 
<p><strong>PrivateLink recap</strong><br /> Before diving in, this blog will quickly recap the architecture for PrivateLink. For a detailed look, see the documentation on how to <a href="https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-share-your-services.html">share your services through PrivateLink</a>.</p> 
<p>In short, PrivateLink is powered by the <a href="https://aws.amazon.com/elasticloadbalancing/network-load-balancer/" rel="noopener noreferrer" target="_blank">Network Load Balancer&nbsp;(NLB)</a> service:</p> 
<ul> 
 <li>You need an NLB deployed in front of the services that you want to expose from your VPC.</li> 
 <li>Your NLB must be configured to listen on your required TCP port(s). 
  <ul> 
   <li>Note that PrivateLink doesn’t support UDP connections at the time of writing this post.</li> 
  </ul> </li> 
 <li>You will need one or more Target Groups and your NLB listener(s) configured to route connections to them.</li> 
</ul> 
<p>Then, we create a VPC endpoint service configuration, based on that NLB, to set up your PrivateLink. From here, we grant permission to specific AWS principals (AWS accounts, <a href="https://aws.amazon.com/iam/">AWS Identity and Access Manager (IAM)</a> users, or IAM roles) so that they can connect to your service from the comfort of their own VPC.</p> 
<p><strong>Option 1 – Pooled NLB with Ports as Tenant Routing Identifier</strong><br /> Pooling (or sharing) your NLB between multiple tenants is a commonly considered solution when looking to implement multi-tenanted SaaS solutions via PrivateLink, as it provides benefits in <em>simplicity</em> (fewer NLBs to deploy and configure) and <em>cost efficiency</em> (as you’re charged for each hour or partial hour that an NLB is running). This pooled approach works extremely well in scenarios where you can perform application-level tenant routing using Layer 7 (Application Layer) constructs, such as HTTP Headers and JWT within your platform. Without these constructs (e.g., dealing with Transport layer connections for our hypothetical Database as a Service offering) we must rely on other techniques for tenant routing.</p> 
<p>To identify and route Transport Layer traffic for SaaS tenants under this pooled NLB model, we can implement a port (or NLB Listener) per tenant to act as the Tenant Identifier. In turn, this enables you to route the traffic to the correct Target Group and through to the backing services. Remember that the Target Group lets you direct all of the traffic to a certain backend port, thereby removing the need to flow custom tenant ports throughout your SaaS platform. Under this model, you can cater for siloed or pooled backend resources as outlined in diagram 1.</p> 
<p><img alt="Using TCP Ports as Tenant Identifiers in AWS Network Load Balancer." class="alignnone size-full wp-image-13650" height="297" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/07/portPerTenant.png" width="574" /></p> 
<p><strong>Diagram 1 – Pooled NLB with Siloed Listeners as Tenant Routing Identifier</strong></p> 
<p>There are important considerations for this tenant routing approach that you should evaluate and consider, including:</p> 
<ul> 
 <li>Each tenant will likely be allocated <em>non-standard ports</em> that can introduce complexity and friction during the tenant on-boarding experience, as your customers may need to raise and explain the need for unusual ports in their firewalls. 
  <ul> 
   <li>Theoretically you could pool a listener if tenants share a Target Group. However, this significantly limits your flexibility if you must transparently adjust the routing for a tenant (e.g., a tenant moved SaaS plans that require different backend resources) and you’ll want to maintain a mapping of which tenants are using which port.</li> 
  </ul> </li> 
 <li>Consider your <em>Service Quotas</em> associated with NLBs – you can have a maximum of 50 listeners (tenants in this case) per NLB. If you outgrow this Service Quota, then you would must add additional NLBs and manage tenant mapping/allocation between them.</li> 
 <li>Network traffic bandwidth metrics are recorded per NLB in CloudWatch. By pooling this resource, it becomes challenging to understand and attribute network bandwidth usage at a per-tenant level.</li> 
 <li>When you present a PrivateLink endpoint to your tenant, all of the ports present on the NLB are presented to the customer. This should be a significant consideration for the <em><a href="https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/tenant-isolation.html">Tenant Isolation</a> and Security</em> of your platform, as it means Tenant B could attempt to access your platform via other ports. Under this pooled routing model this would mean that the tenant is routed to another tenant’s backend infrastructure. 
  <ul> 
   <li>This cross-tenancy risk can be mitigated if the deployed PrivateLink endpoint is owned and controlled in an <a href="https://docs.aws.amazon.com/whitepapers/latest/aws-privatelink/use-case-examples.html#private-access-to-saas-applications">SaaS vendor-provided integration VPC</a>, where you control the security group associated with the endpoint and can restrict access to specific ports. This is detailed in diagram 2.</li> 
   <li>You may additionally mitigate some of the risk that this poses if your backend resources require authentication, but this isn’t always the case.</li> 
  </ul> </li> 
</ul> 
<p><img alt="Considerations when deploying a pooled Network Load Balancer" class="alignnone size-large wp-image-13706" height="545" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/17/PooledNLBConsiderations-1-1024x545.png" width="1024" /></p> 
<p><strong>Diagram 2 – Pooled NLB deployment scenarios</strong></p> 
<p><strong>Option 2 – Siloed NLB as Tenant Routing Identifier</strong></p> 
<p>Depending on your specific scenario, one or more of the above considerations for a pooled NLB may not be feasible or acceptable. Therefore, we look at implementing your NLB and PrivateLink in a <a href="https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/silo-isolation.html">siloed model</a>.</p> 
<p>Under this model, we essentially have a 1:1 mapping for NLBs to your SaaS tenants that supports both Silo and Pooled models’ backend resources via Target Groups. The clear benefit here is <em>simplicity</em> (each NLB and PrivateLink is dedicated per tenant), you can expose the industry standard port(s) that your customers will expect, and you have the most <em>flexibility </em>for configuration, tenant routing, and ongoing updates.</p> 
<p>As you can see in the diagram 3, this siloed NLB model can readily cater for most backend routing configurations, including both siloed and pooled models for target groups and backend resources.</p> 
<p><strong><img alt="Siloed NLBs for Tenant Identification" class="alignnone size-large wp-image-13700" height="677" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/14/SiloedNLBs-1024x677.png" width="1024" /></strong></p> 
<p><strong>Diagram 3 – Siloed NLB as Tenant Routing Identifier </strong></p> 
<p>However, as with Option 1 (pooled), there are some additional considerations that you’ll want to consider if implementing this tenant routing approach:</p> 
<ul> 
 <li>As you’re implementing an NLB per tenant, you should keep an eye on your <em>Service Quotas</em> for NLBs and Target Groups. Both of these resources have a default quota (number of NLBs per region, number of Target Groups per region) that you may need to adjust to cater for your SaaS platform’s requirements.</li> 
 <li>Network bandwidth monitoring and attribution to specific tenants is relatively simple under this model. We can readily understand and attribute network bandwidth to specific tenants.</li> 
 <li>This model will impact your <em>AWS spend </em>(more than the pooled model) as you’re charged for each hour or partial hour that an NLB is running. In a 1:1 model, this pricing dimension can be expected to increase with each tenant on-boarded to this solution. 
  <ul> 
   <li>Note that the pricing dimension for this component is readily known and consistent. Therefore, it can readily be incorporated into your per-tenant cost modelling.</li> 
  </ul> </li> 
</ul> 
<p><strong>Conclusion</strong><br /> This post has shown several approaches to implementing Transport Layer Tenant Routing using <a href="https://aws.amazon.com/privatelink/" rel="noopener noreferrer" target="_blank">PrivateLink</a>. I recommend implementing Option 2 (Siloed NLBs) for most Transport Layer routing scenarios via PrivateLink.</p> 
<p>The following table shows a comparison between the options:</p> 
<table border="1" style="height: 216px;" width="773"> 
 <tbody> 
  <tr> 
   <td style="width: 100px;" width="63"></td> 
   <td width="98"><strong>Tenant Routing Method</strong></td> 
   <td width="87"><strong>Complexity</strong></td> 
   <td width="122"><strong>Security Considerations</strong></td> 
   <td width="80"><strong>Flexibility</strong></td> 
   <td width="71"><strong>Cost Profile</strong></td> 
   <td width="80"><strong>Tenant Bandwidth Monitoring</strong></td> 
  </tr> 
  <tr> 
   <td width="63"><strong>Pooled NLB</strong></td> 
   <td width="98">Per Port</td> 
   <td width="87">Moderate</td> 
   <td width="122">Moderate</td> 
   <td width="80">Moderate</td> 
   <td width="71">Low</td> 
   <td width="80">Hard</td> 
  </tr> 
  <tr> 
   <td width="63"><strong>Siloed NLB</strong></td> 
   <td width="98">Per NLB</td> 
   <td width="87">Low</td> 
   <td width="122">Low</td> 
   <td width="80">High</td> 
   <td width="71">Moderate</td> 
   <td width="80">Easy</td> 
  </tr> 
 </tbody> 
</table> 
<p><strong>Further Reading</strong><br /> I strongly recommend reviewing our other articles and whitepapers on this topic:</p> 
<ul> 
 <li><a href="https://aws.amazon.com/blogs/architecture/building-saas-services-for-aws-customers-with-privatelink/">https://aws.amazon.com/blogs/architecture/building-saas-services-for-aws-customers-with-privatelink/</a></li> 
 <li><a href="https://aws.amazon.com/blogs/apn/enabling-new-saas-strategies-with-aws-privatelink/">https://aws.amazon.com/blogs/apn/enabling-new-saas-strategies-with-aws-privatelink/</a></li> 
 <li>PrivateLink Whitepaper: <a href="https://d1.awsstatic.com/whitepapers/aws-privatelink.pdf">https://d1.awsstatic.com/whitepapers/aws-privatelink.pdf</a></li> 
</ul> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="mpro_headshot.jpg"><img alt="Mark Promnitz" class="alignleft size-full wp-image-5363" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/19/mpro_headshot.jpg" width="114" /></p> 
 <h3 class="lb-h4">Mark Promnitz</h3> 
 <p>Mark Promnitz is a Senior Solutions Architect, based in Australia. In addition to helping his enterprise customers leverage the capabilities of AWS, he can often be found talking about Software as a Service (SaaS), data and cloud-native architectures on AWS.</p> 
</div>
