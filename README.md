# Amazon PrivateLink

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

AWS PrivateLink provides private connectivity between virtual private clouds (VPCs), AWS services, and your on-premises networks without exposing your traffic to the public internet. It makes it easy to connect services across different accounts and VPCs to simplify your network architecture while maintaining security and compliance.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/amazon-privatelink/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - AWS, Networking, Private Connectivity, Security, VPC, Zero Trust, Endpoint Services

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-04-19

## APIs

### AWS PrivateLink API
The AWS PrivateLink API (part of Amazon EC2) provides programmatic access to create and manage VPC endpoint services, VPC endpoints, and endpoint connections for private AWS service connectivity without internet exposure.

**Human URL:** [https://aws.amazon.com/privatelink/](https://aws.amazon.com/privatelink/)

#### Tags:

 - Networking, Private Connectivity, VPC, Endpoint Services, Security

#### Properties

- [Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html)
- [OpenAPI](openapi/amazon-privatelink-openapi.yaml)
- [GettingStarted](https://aws.amazon.com/privatelink/getting-started/)
- [Pricing](https://aws.amazon.com/privatelink/pricing/)
- [FAQ](https://aws.amazon.com/privatelink/faqs/)
- [Authentication](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)
- [RateLimits](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-quotas.html)

## Common Properties

- [Portal](https://aws.amazon.com/privatelink/)
- [Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [Blog](https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/)
- [GitHubOrganization](https://github.com/aws)
- [Console](https://console.aws.amazon.com/vpc/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [StatusPage](https://health.aws.amazon.com/health/status)
- [SpectralRules](rules/amazon-privatelink-spectral-rules.yml)
- [NaftikoCapability](capabilities/private-connectivity.yaml)
- [Vocabulary](vocabulary/amazon-privatelink-vocabulary.yaml)
- [JSON-LD](json-ld/amazon-privatelink-context.jsonld)
- [JSONSchema](json-schema/amazon-privatelink-accept-vpc-endpoint-connections-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-create-vpc-endpoint-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-create-vpc-endpoint-result-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-create-vpc-endpoint-service-configuration-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-create-vpc-endpoint-service-configuration-result-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-delete-vpc-endpoints-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-describe-vpc-endpoint-connections-result-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-describe-vpc-endpoint-services-result-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-describe-vpc-endpoints-result-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-modify-vpc-endpoint-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-modify-vpc-endpoint-service-configuration-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-modify-vpc-endpoint-service-permissions-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-reject-vpc-endpoint-connections-request-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-service-configuration-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-service-detail-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-vpc-endpoint-connection-schema.json)
- [JSONSchema](json-schema/amazon-privatelink-vpc-endpoint-schema.json)
- [JSONStructure](json-structure/amazon-privatelink-accept-vpc-endpoint-connections-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-create-vpc-endpoint-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-create-vpc-endpoint-result-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-create-vpc-endpoint-service-configuration-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-create-vpc-endpoint-service-configuration-result-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-delete-vpc-endpoints-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-describe-vpc-endpoint-connections-result-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-describe-vpc-endpoint-services-result-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-describe-vpc-endpoints-result-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-modify-vpc-endpoint-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-modify-vpc-endpoint-service-configuration-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-modify-vpc-endpoint-service-permissions-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-reject-vpc-endpoint-connections-request-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-service-configuration-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-service-detail-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-vpc-endpoint-connection-structure.json)
- [JSONStructure](json-structure/amazon-privatelink-vpc-endpoint-structure.json)
- [Example](examples/amazon-privatelink-accept-vpc-endpoint-connections-request-example.json)
- [Example](examples/amazon-privatelink-create-vpc-endpoint-request-example.json)
- [Example](examples/amazon-privatelink-create-vpc-endpoint-result-example.json)
- [Example](examples/amazon-privatelink-create-vpc-endpoint-service-configuration-request-example.json)
- [Example](examples/amazon-privatelink-create-vpc-endpoint-service-configuration-result-example.json)
- [Example](examples/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-example.json)
- [Example](examples/amazon-privatelink-delete-vpc-endpoints-request-example.json)
- [Example](examples/amazon-privatelink-describe-vpc-endpoint-connections-result-example.json)
- [Example](examples/amazon-privatelink-describe-vpc-endpoint-services-result-example.json)
- [Example](examples/amazon-privatelink-describe-vpc-endpoints-result-example.json)
- [Example](examples/amazon-privatelink-modify-vpc-endpoint-request-example.json)
- [Example](examples/amazon-privatelink-modify-vpc-endpoint-service-configuration-request-example.json)
- [Example](examples/amazon-privatelink-modify-vpc-endpoint-service-permissions-request-example.json)
- [Example](examples/amazon-privatelink-reject-vpc-endpoint-connections-request-example.json)
- [Example](examples/amazon-privatelink-service-configuration-example.json)
- [Example](examples/amazon-privatelink-service-detail-example.json)
- [Example](examples/amazon-privatelink-vpc-endpoint-connection-example.json)
- [Example](examples/amazon-privatelink-vpc-endpoint-example.json)
- [NaftikoCapability](capabilities/shared/amazon-privatelink.yaml)

## Features

| Name | Description |
|------|-------------|
| Private VPC Endpoints | Connect to AWS services and endpoint services without using public IP addresses or internet gateways. |
| VPC Endpoint Services | Expose services running in your VPC to other VPCs and accounts using Network Load Balancers. |
| Interface Endpoints | Elastic network interfaces with private IP addresses that serve as entry points for supported services. |
| Gateway Endpoints | Route table targets for S3 and DynamoDB traffic without using internet gateways. |
| Cross-Account Connectivity | Enable service consumers in other AWS accounts to access your endpoint services privately. |
| Acceptance Control | Control which service consumers can connect to your endpoint service with acceptance required settings. |
| Private DNS | Configure private DNS names for interface endpoints to simplify connectivity without code changes. |
| Endpoint Policies | Control access to services through endpoint policy documents for fine-grained access control. |

## Use Cases

| Name | Description |
|------|-------------|
| SaaS Service Delivery | Deliver SaaS services to customers privately without internet exposure using PrivateLink. |
| Microservices Private Connectivity | Enable microservices in different VPCs or accounts to communicate privately. |
| Regulatory Compliance | Meet compliance requirements by keeping data transfer off the public internet. |
| Third-Party Service Integration | Connect to marketplace services and partner APIs without public internet routing. |
| On-Premises Private Access | Access AWS services from on-premises networks via VPN or Direct Connect without public endpoints. |

## Integrations

| Name | Description |
|------|-------------|
| AWS VPC | PrivateLink endpoints live in VPC subnets and use VPC security groups for access control. |
| AWS Direct Connect | Access endpoint services from on-premises via Direct Connect without internet routing. |
| AWS VPN | Combine PrivateLink with Site-to-Site VPN for private access from on-premises. |
| AWS Network Load Balancer | Back endpoint services with NLBs for high availability and automatic scaling. |
| AWS Marketplace | Subscribe to AWS Marketplace services and connect privately using PrivateLink. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [amazon-privatelink-openapi.yaml](openapi/amazon-privatelink-openapi.yaml)

### JSON Schema

- [amazon-privatelink-accept-vpc-endpoint-connections-request-schema.json](json-schema/amazon-privatelink-accept-vpc-endpoint-connections-request-schema.json)
- [amazon-privatelink-create-vpc-endpoint-request-schema.json](json-schema/amazon-privatelink-create-vpc-endpoint-request-schema.json)
- [amazon-privatelink-create-vpc-endpoint-result-schema.json](json-schema/amazon-privatelink-create-vpc-endpoint-result-schema.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-request-schema.json](json-schema/amazon-privatelink-create-vpc-endpoint-service-configuration-request-schema.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-result-schema.json](json-schema/amazon-privatelink-create-vpc-endpoint-service-configuration-result-schema.json)
- [amazon-privatelink-delete-vpc-endpoint-service-configurations-request-schema.json](json-schema/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-schema.json)
- [amazon-privatelink-delete-vpc-endpoints-request-schema.json](json-schema/amazon-privatelink-delete-vpc-endpoints-request-schema.json)
- [amazon-privatelink-describe-vpc-endpoint-connections-result-schema.json](json-schema/amazon-privatelink-describe-vpc-endpoint-connections-result-schema.json)
- [amazon-privatelink-describe-vpc-endpoint-services-result-schema.json](json-schema/amazon-privatelink-describe-vpc-endpoint-services-result-schema.json)
- [amazon-privatelink-describe-vpc-endpoints-result-schema.json](json-schema/amazon-privatelink-describe-vpc-endpoints-result-schema.json)
- ...and 8 more

### JSON Structure

- [amazon-privatelink-accept-vpc-endpoint-connections-request-structure.json](json-structure/amazon-privatelink-accept-vpc-endpoint-connections-request-structure.json)
- [amazon-privatelink-create-vpc-endpoint-request-structure.json](json-structure/amazon-privatelink-create-vpc-endpoint-request-structure.json)
- [amazon-privatelink-create-vpc-endpoint-result-structure.json](json-structure/amazon-privatelink-create-vpc-endpoint-result-structure.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-request-structure.json](json-structure/amazon-privatelink-create-vpc-endpoint-service-configuration-request-structure.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-result-structure.json](json-structure/amazon-privatelink-create-vpc-endpoint-service-configuration-result-structure.json)
- [amazon-privatelink-delete-vpc-endpoint-service-configurations-request-structure.json](json-structure/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-structure.json)
- [amazon-privatelink-delete-vpc-endpoints-request-structure.json](json-structure/amazon-privatelink-delete-vpc-endpoints-request-structure.json)
- [amazon-privatelink-describe-vpc-endpoint-connections-result-structure.json](json-structure/amazon-privatelink-describe-vpc-endpoint-connections-result-structure.json)
- [amazon-privatelink-describe-vpc-endpoint-services-result-structure.json](json-structure/amazon-privatelink-describe-vpc-endpoint-services-result-structure.json)
- [amazon-privatelink-describe-vpc-endpoints-result-structure.json](json-structure/amazon-privatelink-describe-vpc-endpoints-result-structure.json)
- ...and 8 more

### JSON-LD

- [amazon-privatelink-context.jsonld](json-ld/amazon-privatelink-context.jsonld)

### Examples

- [amazon-privatelink-accept-vpc-endpoint-connections-request-example.json](examples/amazon-privatelink-accept-vpc-endpoint-connections-request-example.json)
- [amazon-privatelink-create-vpc-endpoint-request-example.json](examples/amazon-privatelink-create-vpc-endpoint-request-example.json)
- [amazon-privatelink-create-vpc-endpoint-result-example.json](examples/amazon-privatelink-create-vpc-endpoint-result-example.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-request-example.json](examples/amazon-privatelink-create-vpc-endpoint-service-configuration-request-example.json)
- [amazon-privatelink-create-vpc-endpoint-service-configuration-result-example.json](examples/amazon-privatelink-create-vpc-endpoint-service-configuration-result-example.json)
- [amazon-privatelink-delete-vpc-endpoint-service-configurations-request-example.json](examples/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-example.json)
- [amazon-privatelink-delete-vpc-endpoints-request-example.json](examples/amazon-privatelink-delete-vpc-endpoints-request-example.json)
- [amazon-privatelink-describe-vpc-endpoint-connections-result-example.json](examples/amazon-privatelink-describe-vpc-endpoint-connections-result-example.json)
- [amazon-privatelink-describe-vpc-endpoint-services-result-example.json](examples/amazon-privatelink-describe-vpc-endpoint-services-result-example.json)
- [amazon-privatelink-describe-vpc-endpoints-result-example.json](examples/amazon-privatelink-describe-vpc-endpoints-result-example.json)
- ...and 8 more

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [amazon-privatelink.yaml](capabilities/shared/amazon-privatelink.yaml)

### Workflow Capabilities

- [private-connectivity.yaml](capabilities/private-connectivity.yaml)

## Vocabulary

- [amazon-privatelink-vocabulary.yaml](vocabulary/amazon-privatelink-vocabulary.yaml)

## Rules

- [amazon-privatelink-spectral-rules.yml](rules/amazon-privatelink-spectral-rules.yml)

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
