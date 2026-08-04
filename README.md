# OutSystems

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

OutSystems is an enterprise low-code and AI-assisted application development platform company, founded in 2001 and headquartered in Boston, Massachusetts with engineering in Lisbon, Portugal. Its two product lines are OutSystems 11 (O11), the self-managed/PaaS platform, and OutSystems Developer Cloud (ODC), the cloud-native successor.

- Website: https://www.outsystems.com/
- Documentation: https://success.outsystems.com/documentation/outsystems_developer_cloud/
- API reference: https://success.outsystems.com/documentation/outsystems_developer_cloud/odc_rest_apis/api_references/
- GitHub: https://github.com/OutSystems
- Status: https://status.outsystems.com/
- Trust center: https://security.outsystems.com/

## APIs

ODC publishes **13 first-party OpenAPI 3.0.x specifications** covering **150 operations**, harvested verbatim from [OutSystems/docs-odc](https://github.com/OutSystems/docs-odc/tree/main/src/eap/reference/apis/resources):

| API | Version | Ops |
|---|---|---|
| User and Access Management | v1 | 37 |
| Environment Configurations | v1 | 16 |
| Asset Repository | v1 | 16 |
| Native Mobile Build | v1 | 13 |
| Dependency Management | v1 | 11 |
| Code Quality | v1 | 10 |
| External Library Generation | v1 | 9 |
| Deployments | v1 | 8 |
| Asset Configurations | v1 | 8 |
| Portfolio | v2 | 7 |
| Subscription | v1 | 7 |
| Build Operations | v1 | 6 |
| Portfolio (deprecated) | v1 | 2 |

Every ODC API is tenant-scoped at `https://{odc-portal-domain}/api/{domain}/{version}`, authenticates with OAuth 2.0 client-credentials against a per-tenant OIDC discovery document, uses offset/limit pagination, and is rate limited per API domain.

OutSystems also ships an official **remote MCP server** (early alpha) at `https://{tenant}.outsystems.dev/mcp`, distributed from [OutSystems/outsystems-mcp](https://github.com/OutSystems/outsystems-mcp) with a provider-published agent skill.

## Artifacts

`openapi/` · `overlays/` · `mcp/` · `skills/` · `packages/` · `cli/` · `components/` · `well-known/` · `llms/` · `authentication/` · `scopes/` · `conventions/` · `rate-limits/` · `errors/` · `lifecycle/` · `changelog/` · `conformance/` · `data-model/` · `security/` · `agentic-access/`

No A2A agent card is published (probed and missed), no AsyncAPI or webhook surface exists, and no idempotency contract is documented — those absences are recorded as data rather than filled in.
