---
name: outsystems-impact-analysis-before-delete
description: Work out what breaks before deleting or redeploying an OutSystems Developer Cloud asset, by walking its consumers and producer graph and running a deletion analysis.
api: openapi/outsystems-dependency-management-api-v1-openapi.json
operations:
- AssetRepository_ListAssets
- AssetRepository_GetAssetLatestRevision
- DependencyManagement_ListAssetConsumers
- DependencyManagement_ListAssetRevisionConsumers
- DependencyManagement_ListAssetProducers
- DependencyManagement_ListAssetProducerGraph
- DependencyManagement_ListAssetRevisionPublicElements
- DependencyManagement_ListReferencedElements
- DependencyManagement_SearchPublicElements
- DeletionAnalysis_LaunchDeletionAnalysis
- DeletionAnalysis_GetResult
- AssetRepository_DeleteAsset
generated: '2026-08-02'
method: generated
---

# Analyze impact before deleting an ODC asset

Answers "what depends on this?" before a destructive change, using the Dependency Management API (`/api/dependency-management/v1`) and the Asset Repository API.

## Before you start

- Bearer token from the tenant `token_endpoint`.
- Required permissions: **Asset management > Open** to read the graph, **Asset management > Delete** to actually delete.
- Deletion is irreversible. Never call `AssetRepository_DeleteAsset` without presenting the analysis result and getting explicit confirmation for that specific asset.

## Steps

1. **Resolve the asset.** `AssetRepository_ListAssets` with `nameContains`, then `AssetRepository_GetAssetLatestRevision` for the current `revisionNumber`.
2. **Who consumes it?** `DependencyManagement_ListAssetConsumers` for the asset, and `DependencyManagement_ListAssetRevisionConsumers` for a specific revision. Anything returned here breaks if the asset goes away.
3. **What does it consume?** `DependencyManagement_ListAssetProducers` and `DependencyManagement_ListAssetProducerGraph` walk the other direction — useful for judging whether the asset is a leaf or a hub.
4. **Which elements are actually in use?** `DependencyManagement_ListAssetRevisionPublicElements` lists what the asset exposes; `DependencyManagement_ListReferencedElements` lists what it references. `DependencyManagement_SearchPublicElements` finds a named element across the tenant.
5. **Run the formal deletion analysis.** POST `DeletionAnalysis_LaunchDeletionAnalysis`, then poll `DeletionAnalysis_GetResult` with the analysis key until terminal.
6. **Report, then stop.** Present the consumers and the analysis outcome to the user. Only proceed to `AssetRepository_DeleteAsset` on explicit confirmation of that asset key.

## Rules

- **Rate limits:** 100 requests/minute domain-wide for this API. Parallelize the independent reads in steps 2–4 rather than serializing them, but stay inside the pool.
- The graph is **revision-scoped**: most of these endpoints key off `(assetKey, revisionNumber)`, not `assetKey` alone. Analyzing the wrong revision gives the wrong answer.
- Report asset keys (UUIDs) alongside names — two assets can share a display name.
- Errors are ProblemDetails on `application/json`; quote the `traceId` when escalating.

## Related

- `data-model/outsystems-data-model.yml` — the derived entity graph, including the revision axis
- `skills/outsystems-deploy-asset-across-stages.md` — the deployment-side analysis
