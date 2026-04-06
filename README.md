# mssql-accounts-system-api

MuleSoft RAML 1.0 specification for the **System Layer API** that exposes full CRUD operations against a Microsoft SQL Server (MSSQL) backend (`accounts` table).

---

## Overview

This API is the **data-access layer** in a three-tier MuleSoft integration architecture:

```
mssql-accounts-process-api  ←──  mssql-accounts-system-api (this spec)
                                            │
                                     Microsoft SQL Server
                                     database: accounts
                                     └── accounts  (table)
```

---

## Resources

| Endpoint | Table | Description |
|---|---|---|
| `GET/POST /accounts` | `accounts` | List / create accounts |
| `GET/PUT/PATCH/DELETE /accounts/{id}` | `accounts` | Read / replace / update / delete a single account |
| `GET /health` | — | Liveness / readiness probe (unauthenticated) |

---

## RAML Project Structure

```
src/main/resources/api/
├── mssql-accounts-system-api.raml   ← main specification
├── datatypes/
│   ├── common-types.raml     ← SqlId, ISODateTime, Address, PaginationMeta
│   ├── account-type.raml     ← Account, AccountRequest, AccountUpdateRequest, AccountsResponse
│   └── error-type.raml       ← ErrorResponse, ErrorDetail
├── traits/
│   ├── pageable.raml         ← page / pageSize query parameters
│   ├── searchable.raml       ← q / name / phone / date-range filters
│   ├── sortable.raml         ← sortBy / sortOrder query parameters
│   ├── cacheable.raml        ← Cache-Control / ETag / Last-Modified headers
│   └── error-handling.raml   ← Standard 400/401/403/404/409/500 error responses
├── securitySchemes/
│   ├── client-id-enforcement.raml  ← Anypoint client_id + client_secret headers
│   ├── basic-auth.raml             ← HTTP Basic Authentication
│   └── oauth2.raml                 ← OAuth 2.0 Bearer Token (client_credentials)
├── libraries/
│   └── commons.raml          ← Central library re-exporting all types, traits & schemes
└── examples/
    ├── accounts/             ← account-request, account-response, accounts-response
    └── errors/               ← error-400, 401, 403, 404, 409, 500
```

---

## RAML Fragments Used

| Fragment type | Files |
|---|---|
| `#%RAML 1.0` (API root) | `mssql-accounts-system-api.raml` |
| `#%RAML 1.0 Library` | `libraries/commons.raml`, `datatypes/*.raml` |
| `#%RAML 1.0 Trait` | `traits/*.raml` |
| `#%RAML 1.0 SecurityScheme` | `securitySchemes/*.raml` |

---

## Security

All endpoints are protected by **Client ID Enforcement** by default.
OAuth 2.0 Bearer Tokens are additionally supported for process-layer consumers.
The `/health` endpoint is explicitly unsecured (`securedBy: []`).

---

## Account ID

Unlike the MongoDB-backed sibling API, account IDs in this API are **integers** (MSSQL `IDENTITY` / auto-increment column). URI parameters and response `id` fields are typed as `integer` with `minimum: 1`.

---

## Salesforce Integration Notes

Each `Account` record exposes a `salesforceId` field (Salesforce 18-char record ID)
to support bidirectional synchronisation with Salesforce implemented in the
**Process Layer API** (`mssql-accounts-process-api`).

---

## Environments

| `{environment}` | Base URI |
|---|---|
| `dev` | `https://dev.mssql-accounts-sys-api.example.com/api/v1` |
| `sit` | `https://sit.mssql-accounts-sys-api.example.com/api/v1` |
| `uat` | `https://uat.mssql-accounts-sys-api.example.com/api/v1` |
| `prod` | `https://prod.mssql-accounts-sys-api.example.com/api/v1` |
