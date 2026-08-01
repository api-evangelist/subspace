---
name: Provision a PacketAccelerator
description: Authenticate, create a PacketAccelerator toward a destination, verify it, and clean it up.
api: openapi/subspace-openapi-original.yml
operations:
- AcceleratorService_Create
- AcceleratorService_List
- AcceleratorService_Get
- AcceleratorService_Delete
---

# Provision a PacketAccelerator

Use the Subspace Product API to accelerate global internet traffic toward a destination host.

## Auth
1. Exchange your `client_id` + `client_secret` for a JWT at
   `POST https://id.subspace.com/oauth/token` (OAuth2 client-credentials). The JWT
   expires after 24 hours.
2. Send it as `Authorization: Bearer <jwt>` on every request to
   `https://api.subspace.com/v1`. Requires the `accelerators:write` scope
   (`accelerators:read` to only list/get).

## Steps
1. **Create** — `AcceleratorService_Create` (`POST /v1/accelerator`) with a body of
   `name`, `destination_ip`, `destination_port` (and optionally `subspace_port`).
   The response (`v1Accelerator`) returns `id`, `subspace_ipv4`, and `subspace_port`
   — route your traffic to that Subspace ingress.
2. **Verify** — `AcceleratorService_Get` (`GET /v1/accelerator/{id}`), or
   `AcceleratorService_List` (`GET /v1/accelerator`) paging via the `next_page` cursor.
3. **Tear down** — `AcceleratorService_Delete` (`DELETE /v1/accelerator/{id}`).

## Error / convention rules
- `401` = missing/expired JWT (re-mint it). `403` = token missing `accelerators:write`.
- `402` = plan quota exceeded. `429` = rate limited — back off.
- List responses paginate with a `next_page` cursor (`before`, `limit`); no idempotency key is supported.
