---
name: Provision a SIPTeleport
description: Authenticate, create a SIPTeleport for a SIP destination, read its entry points, and manage it.
api: openapi/subspace-openapi-original.yml
operations:
- SipTeleportService_Create
- SipTeleportService_List
- SipTeleportService_Get
- SipTeleportService_Update
- SipTeleportService_Delete
---

# Provision a SIPTeleport

Improve SIP/VoIP call quality by teleporting traffic to a destination over Subspace.

## Auth
Get a JWT from `POST https://id.subspace.com/oauth/token` (client-credentials) and
send `Authorization: Bearer <jwt>` to `https://api.subspace.com/v1`. Requires
`sipteleport:write` (`sipteleport:read` for list/get).

## Steps
1. **Create** — `SipTeleportService_Create` (`POST /v1/sipteleport`) with `name` and
   `destination`. The `v1SipTeleportResponse` returns `id`, `teleport_entry_points[]`
   (each an `address` + `transport_type` of `UDP_TCP` or `TLS`), and a `status`
   (`UNKNOWN` / `DISABLED` / `ENABLED`). Point your SIP client at the entry-point addresses.
2. **Read** — `SipTeleportService_Get` (`GET /v1/sipteleport/{id}`) or
   `SipTeleportService_List` (`GET /v1/sipteleport`), paging via `next_page`.
3. **Update** — `SipTeleportService_Update` (`PUT /v1/sipteleport/{id}`).
4. **Delete** — `SipTeleportService_Delete` (`DELETE /v1/sipteleport/{id}`).

## Error / convention rules
- `401` re-mint JWT; `403` scope missing; `402` quota; `409` conflict on create; `429` rate limited.
- Wait for `status: ENABLED` before routing production traffic.
