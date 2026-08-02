---
name: outsystems-deploy-asset-across-stages
description: Deploy or promote an OutSystems Developer Cloud asset revision from one stage to another, poll the operation to a terminal state, and read its messages on failure.
api: openapi/outsystems-deployments-api-v1-openapi.json
operations:
- Environments_ListEnvironments
- AssetRepository_ListAssets
- AssetRepository_GetAssetLatestRevision
- DeploymentAnalysis_LaunchDeploymentAnalysis
- DeploymentAnalysis_GetResult
- DeploymentOperations_Post
- DeploymentOperations_Get
- DeploymentOperations_GetMessages
generated: '2026-08-02'
method: generated
---

# Deploy an asset across ODC stages

Promotes an asset revision between OutSystems Developer Cloud environments (stages), with an impact analysis first and a poll-to-terminal loop after.

## Before you start

- Get an access token: POST to the `token_endpoint` from `https://{odc-portal-domain}/identity/.well-known/openid-configuration` with `grant_type=client_credentials`. Send it as `Authorization: Bearer <token>`. Tokens last 12 hours — cache and reuse, do not mint one per call.
- The API Client must hold **Release management > Deploy assets** for the target stage, and **Stage > View stage** to enumerate environments. Without them the call returns `403`, not `401`.
- This is a **tenant-state mutation**. Restate the exact asset, revision, source stage and target stage to the user and wait for explicit confirmation before step 4.

## Steps

1. **Resolve the environments.** Call `Environments_ListEnvironments` (Portfolio v2, `GET /environments`) and pick the source and target `environmentKey`. Never guess an environment key — names are ambiguous, keys are stable.
2. **Resolve the asset and revision.** Call `AssetRepository_ListAssets` with `nameContains` to find the `assetKey`, then `AssetRepository_GetAssetLatestRevision` for the `revisionNumber` you intend to ship.
3. **Run the impact analysis.** POST `DeploymentAnalysis_LaunchDeploymentAnalysis`, then poll `DeploymentAnalysis_GetResult` with the returned analysis key until terminal. Surface any blocking consumers to the user before deploying — this is what stops a promotion breaking a dependent app.
4. **Start the deployment.** POST `DeploymentOperations_Post` with the asset revision and target `environmentKey`. It returns an `operationKey`; the deployment is asynchronous.
5. **Poll to terminal.** Call `DeploymentOperations_Get` with the `operationKey` every 5–15 seconds until the operation reaches a terminal state. Do not busy-wait in a tight loop.
6. **On failure, read the messages.** Call `DeploymentOperations_GetMessages` for the `operationKey` and report the messages verbatim, along with the `traceId` from the error body.

## Rules

- **Rate limits:** the Deployments domain allows 100 requests/minute, and each `POST` endpoint allows 10/minute. A `429` carries no `Retry-After` header, so apply your own backoff against those published numbers.
- **No idempotency key.** Re-POSTing `DeploymentOperations_Post` creates a *second* deployment operation; it does not deduplicate. Track the `operationKey` you received and resume polling instead of retrying the POST.
- **Errors** come back as a ProblemDetails object on `application/json` (not `application/problem+json`) with `type`, `title`, `status`, `detail`, `instance` and a `traceId`. Quote the `traceId` when escalating to OutSystems support.
- `401` means the token is missing or expired; `403` means the token is valid but the API Client lacks the permission for that stage. Do not retry a `403` — fix permissions in the ODC Portal.
- No `5xx` responses are declared in the spec, so treat any 500-class failure as an undocumented condition and surface it rather than parsing it.

## Related

- `conventions/outsystems-conventions.yml` — pagination, async start-then-poll pattern, auth
- `rate-limits/outsystems-rate-limits.yml` — per-domain and per-endpoint caps
- `errors/outsystems-problem-types.yml` — the error envelope
