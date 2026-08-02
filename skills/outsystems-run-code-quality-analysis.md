---
name: outsystems-run-code-quality-analysis
description: Submit an OutSystems Developer Cloud code-quality analysis, poll it to completion, and read the findings, scores and trend for a portfolio or asset revision.
api: openapi/outsystems-code-quality-api-v1-openapi.json
operations:
- CodeAnalysisControllerV_SubmitCodeAnalysisRequest
- CodeAnalysisControllerV_GetAnalysisRequestStatus
- SyncControllerV_GetAnalysisStatus
- FindingsControllerV_GetFindings
- FindingsOverviewControllerV_GetFindingsSummary
- FindingsOverviewControllerV_GetFindingsTrend
- FindingsOverviewControllerV_GetAppsOverview
- FindingsOverviewControllerV_GetAssetRevisionFindingsSummary
- PatternsControllerV_GetPatterns
- PatternsControllerV_GetPatternsById
generated: '2026-08-02'
method: generated
---

# Run a code-quality analysis in ODC

Measures technical debt across an ODC portfolio using the Code Quality API (`/api/code-quality/v1`).

## Before you start

- Bearer token from the tenant `token_endpoint`.
- Required permissions: **Analyze > View Code Quality findings** for every read path, **Analyze > Manage code quality findings** to change finding state.

## Steps

1. **Check the current analysis state.** Call `SyncControllerV_GetAnalysisStatus`. Pass the optional `portfolioKey` to scope it to one portfolio; leave it off and the response aggregates every portfolio's latest run. When nothing has run yet for the requested scope it returns `200` with an empty result — not `404`.
2. **Submit an analysis.** POST `CodeAnalysisControllerV_SubmitCodeAnalysisRequest`. It returns an `analysisKey` and runs asynchronously.
3. **Poll to terminal.** Call `CodeAnalysisControllerV_GetAnalysisRequestStatus` with the `analysisKey` every 5–15 seconds until terminal.
4. **Read the findings.** Call `FindingsControllerV_GetFindings`. The `isLatest` filter follows the same scoping rule as step 1: add `portfolioKey` for one portfolio's latest findings, omit it for the latest across every portfolio.
5. **Summarize.** Use `FindingsOverviewControllerV_GetFindingsSummary` for the roll-up, `FindingsOverviewControllerV_GetAppsOverview` for per-asset quality metrics, `FindingsOverviewControllerV_GetAssetRevisionFindingsSummary` for one asset revision, and `FindingsOverviewControllerV_GetFindingsTrend` for movement over time.
6. **Explain a finding.** Resolve its rule with `PatternsControllerV_GetPatterns` / `PatternsControllerV_GetPatternsById` before advising a fix — do not paraphrase a pattern you have not read.

## Rules

- **This API uses PascalCase query parameters** where every other ODC API uses camelCase: `Limit`, `Offset`, `AssetKeys`, `Categories`, `AssetType`, `Severities`, `Since`, `To`. Sending camelCase here will silently not filter.
- **`ScoreRanges` changed shape on 2026-07-29.** It replaced `Scores` on the findings-trend endpoint, and across `assets-quality-metrics`, `findings-summary`, `findings-trend`, `common-findings-metrics` and `quality-score-distribution` it is now **one comma-separated string** (`0-49,50-84,85-100`), not an array of items. Integrations written before that date break.
- **Rate limits:** 100 requests/minute domain-wide; `POST /code-analyses` allows 10/minute.
- No idempotency key — a repeated submit starts another analysis.

## Related

- `changelog/outsystems-changelog.yml` — the dated record of the `ScoreRanges` breaking change
- `conventions/outsystems-conventions.yml` — the casing deviation is recorded there too
