# OutSystems

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
