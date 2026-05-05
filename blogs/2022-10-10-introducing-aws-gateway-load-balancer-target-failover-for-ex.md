---
title: "Introducing AWS Gateway Load Balancer Target Failover for Existing Flows"
url: "https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-aws-gateway-load-balancer-target-failover-for-existing-flows/"
date: "Mon, 10 Oct 2022 19:49:05 +0000"
author: "Pratik R. Mankad"
feed_url: "https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/feed/"
---
<h2>Introduction:</h2> 
<p><a href="https://aws.amazon.com/elasticloadbalancing/gateway-load-balancer/">AWS Gateway Load Balancer (GWLB)</a> is an <a href="https://aws.amazon.com/elasticloadbalancing/">Elastic Load Balancing (ELB)</a> service that allows customers to insert third-party virtual appliances such as firewall, intrusion detection and prevention systems (IDS/IPS), network observability and others, transparently into the traffic path. <a href="https://aws.amazon.com/elasticloadbalancing/application-load-balancer/">Application Load Balancer (ALB)</a> and <a href="https://aws.amazon.com/elasticloadbalancing/network-load-balancer/">Network Load Balancer (NLB)</a> are reverse proxies and traffic is routed to them. On the other hand, GWLB is a transparent network appliance (i.e., not a proxy) and traffic is routed through GWLB. The ease with which customers can deploy, scale and manage third-party or their custom appliances has made GWLB as one of the preferred services for designing network architectures to inspect and protect various types of workloads in <a href="https://aws.amazon.com/vpc/">Amazon Virtual Private Cloud (VPC)</a>.</p> 
<p>GWLB opens up new possibilities for inserting third-party functions (such as security, analytics, telecom, Internet of Things) and custom logic into any traffic path. This capability, along with offloading the problems of scale, availability, service delivery and stickiness of flows, enables AWS partners to focus on their core expertise and innovate faster.</p> 
<p>Customers have quickly adopted GWLB based architectures to inspect and protect their traffic. GWLB uses Gateway Load Balancer endpoints (GWLBE) to exchange traffic across VPC boundaries securely. A GWLBE is a VPC endpoint that provides private connectivity between virtual appliances in the service provider VPC and application servers in the service consumer VPC through <a href="https://aws.amazon.com/privatelink/">AWS PrivateLink</a>. The virtual appliances, such as firewalls, are registered with a target group for the GWLB in an inspection VPC and offered as VPC endpoint service. GWLB endpoints (GWLBE) are deployed into each Availability Zone (AZ) in the VPCs (spoke VPCs), where traffic needs to be inspected. To route traffic to the security fleet behind the Gateway Load Balancer, you need to edit route tables of the spoke VPC to point to the Gateway Load Balancer endpoint as the next hop. Traffic, routed through Gateway Load Balancer endpoint, is delivered securely and privately to the Gateway Load Balancer using AWS PrivateLink. For more details on GWLB, refer to <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html">GWLB documentation</a> and <a href="https://aws.amazon.com/blogs/networking-and-content-delivery/category/networking-content-delivery/elastic-load-balancing/gateway-load-balancer/">GWLB blogs</a>.</p> 
<p>In this blog, we will go over the newly launched GWLB Target Failover feature. Specifically, we will discuss how it works, how to enable, its benefits, and the considerations in using this feature.</p> 
<h2>Challenges:</h2> 
<p>While our customers loved the simplicity, durability and availability offered by GWLB, they wanted more flexibility in the way GWLB handled the existing flows in the event of a target failure or when the target was being deregistered for a planned maintenance.</p> 
<p>By default, GWLB uses 5-tuple (source IP, source port, destination IP, destination port and transport protocol) to select the target and routes the traffic to that target. GWLB can also use 3-tuple (source IP, destination IP and transport protocol) or 2-tuple (source IP and destination IP) to select the target. We show this in Figure 1a.</p> 
<ul> 
 <li><strong>(1)</strong> Flow (F1) is being routed to target 1.</li> 
</ul> 
<p><img alt="Figure 1a for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13678 aligncenter" height="456" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure1a.png" width="642" /></p> 
<p style="text-align: center;"><em>Figure 1a: GWLB routing flows to target 1</em></p> 
<p>GWLB monitors the status of its targets through the configured <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/health-checks.html#health-check-settings">health checks</a>. If the health checks exceed the specified number of <em>UnhealthyThresholdCount</em> consecutive failures, the target is flagged unhealthy and GWLB takes the target out of the service. When the target is being deregistered, the target transitions to a draining state. In the draining state, the target can still be healthy and continue to process the traffic to drain the existing flows. After the <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/target-groups.html#deregistration-delay">deregistration delay</a> timeout expires, the target transitions to an unused state and existing flows are dropped.</p> 
<p>Until now, in an event of a target failure or target being deregistered, while new flows were routed to healthy target, GWLB continued to send existing flows to the same unhealthy target or to the target whose deregistration delay timeout as expired and has transitioned to unused state. As shown in Figure 1b:</p> 
<ul> 
 <li><strong>(1)</strong> Target T1 has failed or deregistered and is not available to process traffic (red cross).</li> 
 <li><strong>(2)</strong> GWLB keeps routing the existing flow (F1) to unavailable target T1. As a result, from client’s perspective, the connection appears to be in hung state (dotted arrow).</li> 
 <li><strong>(3)</strong> New flows (F2) are routed to a healthy target T2.</li> 
</ul> 
<p><img alt="Figure 1b for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13679 aligncenter" height="470" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure1b.png" width="620" /></p> 
<p style="text-align: center;"><em>Figure 1b: GWLB routing existing flows to failed/deregistered target</em></p> 
<p>This default behavior helps compute intensive firewalls that may be busy processing traffic and are unable to respond to health checks from GWLB in time. While this worked for compute intensive targets, in case of genuine target failure or target deregistration, existing flows went to unhealthy targets and packets were dropped.</p> 
<p>These existing flows recovered by either getting a TCP reset from the source of the flow, or after the idle flow timeout expired. Since GWLB is a transparent bump-in-the-wire appliance, it does not notify the source that the target processing its flow has failed or has deregistered. As a result, it required the customer to either re-architect the application, which is tough, such that it notices the interruption and reset the flow or just wait for the flow to timeout. Similarly, when customers want to undertake maintenance, they have to plan how to shift the traffic to another healthy target so that existing flows are not affected.</p> 
<p>Providing fault tolerance to virtual appliances is one of the key benefits of using GWLB. Limited ability to shift existing flows to another healthy target in a timely and efficient manner prevented customers from using GWLB’s fault tolerant characteristics to its full potential. It became the customer’s responsibility to restore the existing flows, and it was not an ideal customer experience.</p> 
<h2>Solution:</h2> 
<p>Customers told us they want more flexibility in handling existing flows. Today, we are launching a new GWLB target failover feature. Target failover, which can be configured as an attribute of target group, provides an option to change how to handle the existing flows. Using this feature, in the event of a target failure or target deregistration, customers can now rebalance the existing flows to a healthy target. As shown in Figure 2:</p> 
<ul> 
 <li><strong>(1)</strong> Target T1 has failed or deregistered and is not available to process traffic (red cross).</li> 
 <li><strong>(2)</strong> GWLB routes existing flow (F1) and</li> 
 <li><strong>(3)</strong> new flow (F2) to healthy targets.</li> 
</ul> 
<p><img alt="Figure 2 for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13680 aligncenter" height="470" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure2.png" width="624" /></p> 
<p style="text-align: center;"><em>Figure 2: GWLB target failover</em></p> 
<h3>Benefits:</h3> 
<p>Since existing flows are being rebalanced automatically to a new healthy target:</p> 
<ul> 
 <li>Customers are not required to re-architect their application or wait for the flow to timeout.</li> 
 <li>Service consumers (who consume the service offered by the service providers), should receive a reset from the healthy target and the existing flow is terminated, thus allowing for quicker failure detection. This depends on the logic implemented by the service providers, refer to “Considerations” section for more details. For less stateful appliances, such as IDS/IPS, the existing flow resumes with no interruption and the client doesn’t even detect a target failure.</li> 
 <li>The feature allows service providers to patch or upgrade the appliances during maintenance windows gracefully.</li> 
</ul> 
<h2>High-level overview:</h2> 
<p>The feature uses existing Elastic Load Balancing (ELB) API and provides two new target group attributes to control flow handling for target failure and target deregistration.</p> 
<p>You can use the existing modify-target-group-attributes API to define flow handling behavior using the two new parameters target_failover.on_unhealthy and target_failover.on_deregistration, as shown below:</p> 
<pre><code class="lang-bash">aws elbv2 modify-target-group-attributes \
--target-group-arn arn:aws:elasticloadbalancing:…/my-targets/73e2d6bc24d8a067 \
--attributes \
Key=target_failover.on_unhealthy, Value=rebalance[no_rebalance] \
Key=target_failover.on_deregistration, Value=rebalance[no_rebalance]</code></pre> 
<p>The attribute t<em>arget_failover.on_unhealthy</em> defines how GWLB should handle existing flow when a target becomes unhealthy. Similarly, the attribute <em>target_failover.on_deregistration</em> defines how GWLB should handle existing flow when a target is de-registered.</p> 
<p>You can assign one of the following two values to these attributes:</p> 
<ol> 
 <li><strong>Value = no_rebalance (Default):</strong> This is the current default behavior. When selected, GWLB will continue to send existing flows to failed/deregistered target. New flows are always sent to the healthy target. This also ensures that the existing GWLB flow handling behavior and the existing GWLBs are unaffected, until the user enables this feature (set the value to rebalance), thus ensuring backward compatibility.</li> 
 <li><strong>Value = rebalance:</strong> When selected, GWLB calculates the new hash value for existing flows and sends the flows to another healthy target. Unlike Network Load Balancer (NLB), GWLB does not send TCP RST back to clients because GWLB is a bump-in-the-wire entity and is not expected to play an active role in managing flow states. New flows are always sent to healthy target.</li> 
</ol> 
<p>Note that you must assign same value (either balance or no_rebalance) to the two attributes or else you will run into validation error like one below:</p> 
<pre><code class="lang-bash">An error occurred (ValidationError) when calling the ModifyTargetGroupAttributes operation: Target group attribute keys 'target_failover.on_unhealthy' and 'target_failover.on_deregistration' must both be set to the same value for this target group</code></pre> 
<p>You can also configure this feature and check the current flow handling behavior using AWS management console. As shown in Figure 3, by default, the “<em>Target Failover”</em> attribute is “<em>off” </em>(Value = no_rebalance).</p> 
<p><img alt="Figure 3 for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13681 aligncenter" height="777" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure3.jpg" width="1687" /></p> 
<p style="text-align: center;"><em>Figure 3: Target Group Attributes</em></p> 
<p>In order for GWLB to rebalance the existing flows, you need to enable the feature by toggling the “<em>Target Failover”</em> attribute to <em>“on”</em>. We show this in Figure 4 below.</p> 
<p><img alt="Figure 4 for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13682 aligncenter" height="716" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure4.jpg" width="932" /></p> 
<p style="text-align: center;"><em>Figure 4: Edit Target Group Attributes</em></p> 
<p>As shown in Figure 5, you can verify the status now shows <em>“Rebalance flows on”</em> under the <em>“Attributes”</em> tab of the desired target group.</p> 
<p><img alt="Figure 5 for GWLB Blog: Introducing AWS Gateway Load Balancer Target Failover for Existing Flows" class="size-full wp-image-13683 aligncenter" height="769" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/10/10/Figure5.jpg" width="1678" /></p> 
<p style="text-align: center;"><em>Figure 5: Verify Changes</em></p> 
<p>You can also configure this feature using AWS CloudFormation, and/or supported AWS SDKs.</p> 
<h2>Considerations:</h2> 
<ul> 
 <li>It is important to understand the time intervals involved in this feature. The total target failover time is a combination of multiple time intervals. Target Failover time = (time taken to detect failed target/drain the target) + (time taken to synchronize the GWLB data plane and to rebalance the flow to the new target). Sum of all these times may add up significantly and it may cause a delay in rebalancing existing flows to the healthy targets. Specifically, 
  <ul> 
   <li>This feature does not change the time it takes to detect target deregistration or failure. That timing is determined by the deregistration delay or the health check configuration. For example, when a customer configures 10 second health check interval and 3 missed heartbeats for failure detection, then the target detection time is 10 X 3 = 30 seconds.</li> 
   <li>For a target that is deregistered, GWLB waits for the deregistration delay time to expire, before it starts rebalancing the flows.</li> 
   <li>GWLB depends on TCP data segments to trigger rebalancing away from an unhealthy target. If a client’s TCP stack is retransmitting segments (because the target became unhealthy or because of other unrelated packet drops in the network) TCP’s exponential backoff can delay retransmissions increasing the time taken by GWLB to rebalance flows. The combination of failed target health check and drastically reduced traffic because of exponential backoff can cause the traffic to stall for a few minutes until a new TCP segment is retransmitted and arrives at the GWLB.</li> 
   <li>In order for flows to rebalance faster, we recommend using the lowest possible values for health check setting and the deregistration delay timeout. For example, setting “Deregistration Delay” to 60 seconds allows flow to rebalance to healthy target in ~120 seconds.</li> 
  </ul> </li> 
 <li>Enabling this feature results in rebalancing the existing flows of an unhealthy target to a healthy target appliance. Existing flows of a healthy target are not affected. Healthy target appliances will receive these new flows without the 3-way TCP handshake. AWS partners, independent software vendors (ISVs) and/or customer organizations building their own solutions should have logic in place that allows target appliances to handle these new “in-transit” flows.</li> 
 <li>Customers should check with their ISVs to understand how they have integrated their product with this feature. Specifically, pay attention to whether the ISV appliance is sending a reset and causing the client to restart the connection or whether it is rebalancing the flow to a healthy target without affecting the existing flow.</li> 
</ul> 
<h2>Conclusion:</h2> 
<p><a href="https://aws.amazon.com/elasticloadbalancing/gateway-load-balancer/">AWS Gateway Load Balancer (GWLB)</a> together with Gateway Load Balancer Endpoints (GWLBE) makes it easy for our customers to deploy, scale and manage virtual appliances fleets. Until now, AWS Gateway Load Balancer, in an event of a target failure and/or target de-registration, sent existing flow to an unhealthy and/or de-registered target. Today, with the launch of GWLB Target Failover feature, you now have the ability to rebalance existing flows to another healthy target, thus allowing you to change behavior for your existing flows. You should also evaluate the edge cases listed in considerations before using this feature.</p> 
<p><strong>An update was made on May 20, 2025:</strong> The effect of enabling this feature on existing flows was clarified.</p> 
<h2>About the Authors:</h2> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <p class="samvas_headshot1.jpg"><img alt="Milind Kulkarni Headshot1.jpg" class="alignleft size-full wp-image-5363" height="125" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2020/11/16/Milind_K.jpg" width="125" /></p> 
 <h3 class="lb-h4">Milind Kulkarni</h3> 
 <p>Milind is a Senior Product Manager at Amazon Web Services (AWS). He has over 20 years of experience in networking, data center architectures, SDN/NFV, and cloud computing. He is a co-inventor of nine US Patents and has co-authored three IETF Standards.</p> 
</div> 
<div class="blog-author-box" style="border: 1px solid #d5dbdb; padding: 15px;"> 
 <h3 class="lb-h4"><img alt="Pratik Mankad Headshot1.jpg" class="wp-image-5370 size-full alignleft" height="122" src="https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2020/11/06/pmankad-headshot1.jpg" width="122" />Pratik R. Mankad</h3> 
 <p>Pratik is a Solutions Architect at AWS with 18 years of experience in network engineering. He is passionate about network technologies and loves to innovate to help solve customer problems. He enjoys architecting solutions and providing technical guidance to help customers and partners achieve their business objectives.</p> 
</div>
