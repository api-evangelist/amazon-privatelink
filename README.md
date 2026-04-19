# Amazon PrivateLink (amazon-privatelink)
AWS PrivateLink provides private connectivity between virtual private clouds (VPCs), AWS services, and your on-premises networks without exposing your traffic to the public internet. It makes it easy to connect services across different accounts and VPCs to simplify your network architecture while maintaining security and compliance.

**URL:** [https://aws.amazon.com/privatelink/](https://aws.amazon.com/privatelink/)

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

- [Amazon PrivateLink OpenAPI (13 operations)](openapi/amazon-privatelink-openapi.yaml)

### JSON Schema

18 schema files covering VPC endpoint services, endpoints, connections, and permissions.

### JSON Structure

18 JSON Structure files converted from JSON Schema with strict typing.

### JSON-LD

- [Amazon PrivateLink Context](json-ld/amazon-privatelink-context.jsonld)

### Examples

18 example JSON files generated from JSON Schema definitions.

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Amazon PrivateLink API](capabilities/shared/amazon-privatelink.yaml) — 7 operations for private connectivity

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Private Connectivity](capabilities/private-connectivity.yaml) | AWS PrivateLink API | 7 | Network Engineer, Platform Engineer |

## Vocabulary

- [Amazon PrivateLink Vocabulary](vocabulary/amazon-privatelink-vocabulary.yaml) — Unified taxonomy mapping resources, actions, workflows, and personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon PrivateLink Spectral Rules](rules/amazon-privatelink-spectral-rules.yml) — 16 rules across 8 categories enforcing Amazon PrivateLink API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
