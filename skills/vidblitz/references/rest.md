# Vidblitz REST API

Use the REST API only when writing code for the user (a script, a backend, an automation). In a chat, use the MCP tools. Every MCP tool is one of these operations with the same input.

- Base URL: `https://vidblitz.ai/api/v1`
- Auth: `Authorization: Bearer vb_live_…` (an API key from Settings → API and MCP, created by a workspace owner or admin) or an OAuth access token.
- `POST` and `PATCH` take a JSON object body; `GET` and `DELETE` take query parameters; `{field}` in a path comes from the input.
- Errors: `{ "error": "a sentence for the person", "code": "stable_code" }`, plus `issues` for 400.
- OpenAPI 3.1: `https://vidblitz.ai/api/v1/openapi.json`. Docs: https://vidblitz.ai/developers

| MCP tool | REST call |
| --- | --- |
| `get_account` | `GET /account` |
| `get_options` | `GET /options` |
| `list_brand_profiles` | `GET /brand-profiles` |
| `create_video` | `POST /videos` (also takes an `Idempotency-Key` header) |
| `get_video_request` | `GET /video-requests/{request_id}` |
| `list_projects` | `GET /projects?limit=&offset=` |
| `get_project` | `GET /projects/{project_id}` |
| `confirm_video` | `POST /projects/{project_id}/confirm` with `quote_id`, `acknowledged_warnings` |
| `rename_project` | `PATCH /projects/{project_id}` with `title` |
| `cancel_project` | `POST /projects/{project_id}/cancel` |
| `delete_project` | `DELETE /projects/{project_id}` |
| `retry_video` | `POST /projects/{project_id}/retry` (Vidblitz staff only) |

`wait_for_video` has no REST twin: poll `GET /video-requests/{request_id}`, then `GET /projects/{project_id}`, every 10–30 seconds while checking and every few minutes during production.

The same rule holds in code: never call `/confirm` without the person's agreement to the quote's exact credits and warnings.
