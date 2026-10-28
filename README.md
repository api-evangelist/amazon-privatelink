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
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
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

**Areas:** AWS · Networking · Private Connectivity · Security · VPC · Zero Trust · Endpoint Services

## Amazon PrivateLink on APIs.io

Everything profiled for Amazon PrivateLink is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/amazon-privatelink/](https://apis.io/providers/amazon-privatelink/)** — or go straight to one of the APIs or properties below.

### APIs (4)

- [Amazon PrivateLink Endpoint Connections API](https://apis.io/apis/amazon-privatelink/amazon-privatelink-endpoint-connections-api/) — Endpoint connection management
- [Amazon PrivateLink Endpoint Services API](https://apis.io/apis/amazon-privatelink/amazon-privatelink-endpoint-services-api/) — VPC endpoint services (provider side)
- [Amazon PrivateLink Principals API](https://apis.io/apis/amazon-privatelink/amazon-privatelink-principals-api/) — Endpoint service principal management
- [Amazon PrivateLink VPC Endpoints API](https://apis.io/apis/amazon-privatelink/amazon-privatelink-vpc-endpoints-api/) — VPC endpoints (consumer side)

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Amazon Privatelink Agentic Access](https://apis.io/agentic-access/amazon-privatelink/amazon-privatelink-agentic-access/)

#### JSON Schema (18)

The data models the API reads and writes.

- [AcceptVpcEndpointConnectionsRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-accept-vpc-endpoint-connections-request/)
- [CreateVpcEndpointRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-request/)
- [CreateVpcEndpointResult](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-result/)
- [CreateVpcEndpointServiceConfigurationRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-request/)
- [CreateVpcEndpointServiceConfigurationResult](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-result/)
- [DeleteVpcEndpointServiceConfigurationsRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-delete-vpc-endpoint-service-configurations-request/)
- [DeleteVpcEndpointsRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-delete-vpc-endpoints-request/)
- [DescribeVpcEndpointConnectionsResult](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-connections-result/)
- [DescribeVpcEndpointServicesResult](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-services-result/)
- [DescribeVpcEndpointsResult](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-describe-vpc-endpoints-result/)
- [ModifyVpcEndpointRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-request/)
- [ModifyVpcEndpointServiceConfigurationRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-configuration-request/)
- [ModifyVpcEndpointServicePermissionsRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-permissions-request/)
- [RejectVpcEndpointConnectionsRequest](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-reject-vpc-endpoint-connections-request/)
- [ServiceConfiguration](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-service-configuration/)
- [ServiceDetail](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-service-detail/)
- [VpcEndpoint](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-vpc-endpoint/)
- [VpcEndpointConnection](https://apis.io/schemas/amazon-privatelink/amazon-privatelink-vpc-endpoint-connection/)

#### JSON Structure (18)

Data models expressed in JSON Structure.

- [Amazon Privatelink Accept Vpc Endpoint Connections Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-accept-vpc-endpoint-connections-request-structure/)
- [Amazon Privatelink Create Vpc Endpoint Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-request-structure/)
- [Amazon Privatelink Create Vpc Endpoint Result Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-result-structure/)
- [Amazon Privatelink Create Vpc Endpoint Service Configuration Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-request-structure/)
- [Amazon Privatelink Create Vpc Endpoint Service Configuration Result Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-result-structure/)
- [Amazon Privatelink Delete Vpc Endpoint Service Configurations Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-structure/)
- [Amazon Privatelink Delete Vpc Endpoints Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-delete-vpc-endpoints-request-structure/)
- [Amazon Privatelink Describe Vpc Endpoint Connections Result Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-connections-result-structure/)
- [Amazon Privatelink Describe Vpc Endpoint Services Result Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-services-result-structure/)
- [Amazon Privatelink Describe Vpc Endpoints Result Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-describe-vpc-endpoints-result-structure/)
- [Amazon Privatelink Modify Vpc Endpoint Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-request-structure/)
- [Amazon Privatelink Modify Vpc Endpoint Service Configuration Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-configuration-request-structure/)
- [Amazon Privatelink Modify Vpc Endpoint Service Permissions Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-permissions-request-structure/)
- [Amazon Privatelink Reject Vpc Endpoint Connections Request Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-reject-vpc-endpoint-connections-request-structure/)
- [Amazon Privatelink Service Configuration Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-service-configuration-structure/)
- [Amazon Privatelink Service Detail Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-service-detail-structure/)
- [Amazon Privatelink Vpc Endpoint Connection Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-vpc-endpoint-connection-structure/)
- [Amazon Privatelink Vpc Endpoint Structure](https://apis.io/json-structure/amazon-privatelink/amazon-privatelink-vpc-endpoint-structure/)

#### JSON-LD (1)

Linked-data contexts for the provider's vocabulary.

- [Amazon Privatelink Context](https://apis.io/jsonld/amazon-privatelink/amazon-privatelink-context/)

#### Vocabulary (1)

The provider's domain terms, defined.

- [Amazon Privatelink Vocabulary](https://apis.io/vocabularies/amazon-privatelink/amazon-privatelink-vocabulary/)

#### Examples (18)

Example requests and responses.

- [Amazon Privatelink Accept Vpc Endpoint Connections Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-accept-vpc-endpoint-connections-request-example/)
- [Amazon Privatelink Create Vpc Endpoint Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-request-example/)
- [Amazon Privatelink Create Vpc Endpoint Result Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-result-example/)
- [Amazon Privatelink Create Vpc Endpoint Service Configuration Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-request-example/)
- [Amazon Privatelink Create Vpc Endpoint Service Configuration Result Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-create-vpc-endpoint-service-configuration-result-example/)
- [Amazon Privatelink Delete Vpc Endpoint Service Configurations Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-delete-vpc-endpoint-service-configurations-request-example/)
- [Amazon Privatelink Delete Vpc Endpoints Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-delete-vpc-endpoints-request-example/)
- [Amazon Privatelink Describe Vpc Endpoint Connections Result Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-connections-result-example/)
- [Amazon Privatelink Describe Vpc Endpoint Services Result Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-describe-vpc-endpoint-services-result-example/)
- [Amazon Privatelink Describe Vpc Endpoints Result Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-describe-vpc-endpoints-result-example/)
- [Amazon Privatelink Modify Vpc Endpoint Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-request-example/)
- [Amazon Privatelink Modify Vpc Endpoint Service Configuration Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-configuration-request-example/)
- [Amazon Privatelink Modify Vpc Endpoint Service Permissions Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-modify-vpc-endpoint-service-permissions-request-example/)
- [Amazon Privatelink Reject Vpc Endpoint Connections Request Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-reject-vpc-endpoint-connections-request-example/)
- [Amazon Privatelink Service Configuration Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-service-configuration-example/)
- [Amazon Privatelink Service Detail Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-service-detail-example/)
- [Amazon Privatelink Vpc Endpoint Connection Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-vpc-endpoint-connection-example/)
- [Amazon Privatelink Vpc Endpoint Example](https://apis.io/examples/amazon-privatelink/amazon-privatelink-vpc-endpoint-example/)

#### Collections (8)

Postman and Open Collection request collections.

- [Amazon PrivateLink Endpoint Connections API](https://apis.io/collections/amazon-privatelink/open-amazon-privatelink-endpoint-connections-api/)
- [Amazon PrivateLink Endpoint Connections Endpoint Services API](https://apis.io/collections/amazon-privatelink/open-amazon-privatelink-endpoint-services-api/)
- [Amazon PrivateLink Endpoint Connections Principals API](https://apis.io/collections/amazon-privatelink/open-amazon-privatelink-principals-api/)
- [Amazon PrivateLink Endpoint Connections VPC Endpoints API](https://apis.io/collections/amazon-privatelink/open-amazon-privatelink-vpc-endpoints-api/)
- [Amazon PrivateLink Endpoint Connections API](https://apis.io/collections/amazon-privatelink/postman-amazon-privatelink-endpoint-connections-api/)
- [Amazon PrivateLink Endpoint Connections Endpoint Services API](https://apis.io/collections/amazon-privatelink/postman-amazon-privatelink-endpoint-services-api/)
- [Amazon PrivateLink Endpoint Connections Principals API](https://apis.io/collections/amazon-privatelink/postman-amazon-privatelink-principals-api/)
- [Amazon PrivateLink Endpoint Connections VPC Endpoints API](https://apis.io/collections/amazon-privatelink/postman-amazon-privatelink-vpc-endpoints-api/)

#### Governance rules (2)

Spectral rulesets for the provider's contracts.

- [Amazon PrivateLink — jsonschema rules](https://apis.io/rules/amazon-privatelink/amazon-privatelink-jsonschema-spectral-rules/)
- [Amazon PrivateLink API Rules](https://apis.io/rules/amazon-privatelink/amazon-privatelink-spectral-rules/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Amazon Privatelink Authentication](https://apis.io/security/amazon-privatelink/amazon-privatelink-authentication/)
- [Amazon Privatelink Domain Security](https://apis.io/security/amazon-privatelink/amazon-privatelink-domain-security/)
- [Amazon Privatelink Trust Center](https://apis.io/security/amazon-privatelink/amazon-privatelink-trust-center/)
- [Amazon Privatelink Vulnerability Disclosure](https://apis.io/security/amazon-privatelink/amazon-privatelink-vulnerability-disclosure/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Amazon Privatelink Plans Pricing](https://apis.io/plans/amazon-privatelink/amazon-privatelink-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Amazon Privatelink Rate Limits](https://apis.io/rate-limits/amazon-privatelink/amazon-privatelink-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Amazon Privatelink Finops](https://apis.io/finops/amazon-privatelink/amazon-privatelink-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://aws.amazon.com/privatelink/)
- [Documentation](https://docs.aws.amazon.com/vpc/latest/privatelink/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [Blog](https://aws.amazon.com/blogs/networking-and-content-delivery/tag/aws-privatelink/)
- [Terms of service](https://aws.amazon.com/service-terms/)
- [Privacy policy](https://aws.amazon.com/privacy/)
- [GitHub](https://github.com/aws)
- [Status](https://health.aws.amazon.com/health/status)

## API Evangelist rating

**Kin Score 45.0** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 71.4 |
| Contract quality | 24.2 |
| Contract governance | 27.3 |
| Operational transparency | 36.8 |
| Developer ergonomics | 46.4 |
| Access clarity | 60.5 |

**Agent readiness 22.7** — agent-aware

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | — |
| OpenAPI Examples | documented (partial credit) |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | — |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | distinct |

**Regulatory alignment 35.3** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/amazon-privatelink/](https://apis.io/providers/amazon-privatelink/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Amazon PrivateLink: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/amazon-privatelink/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
