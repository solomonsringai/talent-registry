# OpenClaw chatCompletions — Scope Fix

> **Talent Category:** Infrastructure / Auth / OpenClaw
> **Discovered:** 2026-03-29
> **Severity:** Breaking — blocks all external integrations using chatCompletions

## Problem

OpenClaw's `/v1/chat/completions` HTTP endpoint returns 403:

```json
{
  "ok": false,
  "error": {
    "type": "forbidden",
    "message": "missing scope: operator.write"
  }
}
```

This breaks:
- Agent orchestration dashboards → OpenClaw agent routing
- Vapi voice pipeline → OpenClaw phone line
- Any external service calling the chatCompletions API

## Root Cause

OpenClaw (as of 2026.3.28+) added explicit scope enforcement on the HTTP chatCompletions endpoint. The endpoint checks for `operator.write` scope via a request header:

```javascript
// In gateway-cli-DlnlX7IW.js
function resolveGatewayRequestedOperatorScopes(req) {
    const raw = getHeader(req, "x-openclaw-scopes")?.trim();
    if (!raw) return [];
    return raw.split(",").map(s => s.trim()).filter(s => s.length > 0);
}
```

If no `X-OpenClaw-Scopes` header is sent, the returned scopes array is **empty** → scope check fails → 403.

The shared gateway token (`gateway.auth.token`) still authenticates the request, but **scope declaration is now separate from authentication**.

## Fix

Add the `X-OpenClaw-Scopes` header to all chatCompletions requests:

```
X-OpenClaw-Scopes: operator.read,operator.write
```

### curl example

```bash
curl -X POST http://127.0.0.1:18789/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <gateway-token>" \
  -H "X-OpenClaw-Scopes: operator.read,operator.write" \
  -d '{"model":"openclaw:watson","messages":[{"role":"user","content":"hello"}]}'
```

### Python (urllib) example

```python
req = urllib.request.Request(
    "http://127.0.0.1:18789/v1/chat/completions",
    data=payload,
    headers={
        "Content-Type": "application/json",
        "Authorization": f"Bearer {token}",
        "X-OpenClaw-Scopes": "operator.read,operator.write",
    },
)
```

## Where to Apply

Any HTTP client calling the OpenClaw chatCompletions endpoint needs this header added. Common cases:
- Agent orchestration bridges
- Voice pipeline integrations (e.g. Vapi — may need a reverse proxy to inject the header)
- Any external service calling `/v1/chat/completions`

## Key Details

- **Header name:** `X-OpenClaw-Scopes` (case-insensitive)
- **Required value:** `operator.read,operator.write` (comma-separated)
- **Auth is separate:** The bearer token authenticates. The header declares scopes.
- **Without the header:** Scopes resolve to `[]` → any scope check fails
- **Device token auth:** WebSocket clients use device tokens with embedded scopes — they don't need this header. This only affects HTTP API callers.

## Detection

If you see this in logs or responses:
```
missing scope: operator.write
```
...on the chatCompletions endpoint, this is the fix.

## Notes

- This changed in OpenClaw 2026.3.28 (build f9b1079)
- Previous versions auto-promoted shared token auth to full operator scopes on HTTP paths
- The change is intentional — scope declaration is now opt-in on HTTP surfaces
- WebSocket connections (Control UI, CLI) are unaffected — they negotiate scopes during the connect handshake
