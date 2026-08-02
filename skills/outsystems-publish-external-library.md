---
name: outsystems-publish-external-library
description: Upload a .NET package to OutSystems Developer Cloud, generate an external library from it, poll the generation to completion, and read its logs and contents.
api: openapi/outsystems-external-library-generation-api-v1-openapi.json
operations:
- Uploads_CreateUpload
- Upload_UploadOperation
- GenerationOperations_CreateGenerationOperation
- GenerationOperations_GetGenerationOperationByOperationKey
- GenerationOperations_GetGenerationOperationLogMessages
- GenerationOperations_GetGenerationOperationContents
- GenerationOperations_GetGenerationOperations
- GenerationOperations_DeleteGenerationOperationByOperationKey
generated: '2026-08-02'
method: generated
---

# Publish an external library to ODC

Turns a C#/.NET class library into an ODC external library that apps can consume as visual-language actions, using the External Library Generation API (`/api/external-libraries/v1`).

## Before you start

- The .NET project must reference the `OutSystems.ExternalLibraries.SDK` NuGet package and decorate its public surface with the SDK attributes that map C# onto the OutSystems visual language. Target framework must match what the SDK documents.
- Bearer token from the tenant `token_endpoint`. Uploading, generating and deleting are tenant-state mutations — confirm with the user first.

## Steps

1. **Create the upload.** POST `Uploads_CreateUpload` to register the package upload, and use `Upload_UploadOperation` to check the upload surface.
2. **Start the generation.** POST `GenerationOperations_CreateGenerationOperation` referencing the uploaded package. It returns an `operationKey` — generation is asynchronous.
3. **Poll to terminal.** Call `GenerationOperations_GetGenerationOperationByOperationKey` every 5–15 seconds until the operation reaches a terminal state.
4. **Read the logs.** On failure — or to show what was mapped — call `GenerationOperations_GetGenerationOperationLogMessages` for the `operationKey` and surface the messages verbatim.
5. **Inspect what was produced.** Call `GenerationOperations_GetGenerationOperationContents` to list the generated library's exposed elements before telling the user it is ready to consume.
6. **List or clean up.** `GenerationOperations_GetGenerationOperations` enumerates operations; `GenerationOperations_DeleteGenerationOperationByOperationKey` removes one. Deletion is destructive — confirm explicitly.

## Rules

- **Upload size cap:** the remote-MCP path documents a 50 MB decoded limit (~67 MB base64-encoded), with concurrent uploads queued per replica rather than rejected. Pre-flight the package size before starting.
- **Rate limits:** 100 requests/minute domain-wide; each `POST` endpoint allows 10/minute.
- **A generation operation key that is not a valid GUID returns `400`**, with the reason in the ProblemDetails `detail`.
- `404` "Generation Operation not found" means the key is wrong or the operation was deleted — do not retry it as a transient failure.
- No idempotency key — re-POSTing starts a second generation.

## Related

- `packages/outsystems-packages.yml` — the `OutSystems.ExternalLibraries.SDK` NuGet package
- `mcp/outsystems-tool-crosswalk.yml` — the same capability is exposed as an MCP tool domain
