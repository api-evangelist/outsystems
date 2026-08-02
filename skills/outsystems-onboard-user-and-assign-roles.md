---
name: outsystems-onboard-user-and-assign-roles
description: Create users in an OutSystems Developer Cloud organization (singly or in bulk), place them in groups, and grant the organization and application roles they need.
api: openapi/outsystems-user-access-management-api-v1-openapi.json
operations:
- UserProfile_CreateUser
- UserProfile_QueryUsers
- UserProfile_GetUser
- BulkUserProfile_CreateBulkUserProfilesOperation
- BulkUserProfile_GetBulkUserProfileOperationStatus
- Group_QueryGroups
- Group_AddOrRemoveUsersFromGroup
- OrganizationRole_QueryOrganizationRoles
- UserOrganizationRoles_GrantOrganizationRoleToUser
- ApplicationRole_QueryApplicationRoles
- UserApplicationRoles_GrantApplicationRoleToUser
- UserApplicationRoles_QueryUserApplicationRoles
generated: '2026-08-02'
method: generated
---

# Onboard users and assign roles in ODC

Creates ODC users and gives them the right access, using the User and Access Management API (`/api/identity/v1`).

## Before you start

- Bearer token from the tenant `token_endpoint`, as in every ODC REST API.
- Required permissions: **User management > Manage users** to create or patch users, **User management > Manage end-user groups** for group membership, **User management > Manage organization roles** to grant organization roles, and **User management > View end users** for the read paths.
- Creating and role-granting are tenant-state mutations — confirm the exact users and roles with the user first.

## Steps

1. **Check whether the user already exists.** Call `UserProfile_QueryUsers` with a filter before creating. Users are paginated: pass `limit` (1–100, default 100) and `offset` (zero-based, default 0), and read `page.nextPageOffset` to advance.
2. **Create the user.**
   - One user: POST `UserProfile_CreateUser`.
   - Many users: POST `BulkUserProfile_CreateBulkUserProfilesOperation` with **between 1 and 100 users inclusive** — outside that range it returns `400`. It is asynchronous: it returns an operation key, and you poll `BulkUserProfile_GetBulkUserProfileOperationStatus` until terminal.
3. **Place the user in groups.** List groups with `Group_QueryGroups`, then PATCH `Group_AddOrRemoveUsersFromGroup` on the chosen group key. Group membership is the scalable path — prefer it over per-user role grants when the same access repeats.
4. **Grant organization roles.** List them with `OrganizationRole_QueryOrganizationRoles`, then POST `UserOrganizationRoles_GrantOrganizationRoleToUser` with the user key and role key.
5. **Grant application roles.** List them with `ApplicationRole_QueryApplicationRoles`, then POST `UserApplicationRoles_GrantApplicationRoleToUser` with the user key and role key.
6. **Verify.** Call `UserApplicationRoles_QueryUserApplicationRoles` and `UserProfile_GetUser` and report the resulting access back, quoting the user `key` (UUID) alongside the name.

## Rules

- **Bulk is rate limited hard:** `POST /users/bulk` allows **5 requests/minute** against a 100/minute domain-wide pool. Batch to the 100-user maximum rather than issuing many small bulk calls.
- **No idempotency key.** A repeated create is a new create attempt, not a no-op — always query first.
- Report the user `key` (UUID) with the display name; names are editable and ambiguous, keys are stable.
- This API handles personal data. Do not echo full user lists into logs or downstream messages beyond what the task needs.

## Related

- `scopes/outsystems-scopes.yml` — the full portal permission model
- `conventions/outsystems-conventions.yml` — offset/limit pagination and the `page` envelope
