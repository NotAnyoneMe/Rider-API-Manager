# Request Logs

Every API call is automatically logged — no setup needed. Logs record who called what endpoint, when, how fast it responded, and from which IP.

---

## Get All Logs (Admin)

**`GET /logs/`** — Admin only

Supports pagination:

```
GET /logs/?limit=100&offset=0
```

Returns newest logs first.

```json
[
  {
    "id": 4521,
    "user_id": 7,
    "method": "PATCH",
    "endpoint": "/orders/101/status",
    "status_code": 200,
    "response_time_ms": 45.23,
    "ip_address": "192.168.1.10",
    "created_at": "2025-01-15T11:05:00Z"
  }
]
```

`user_id` is `null` for unauthenticated requests (like the webhook endpoint).

---

## Get Logs for a Specific User (Admin)

**`GET /logs/user/{user_id}`** — Admin only

Filter logs by a specific rider or admin:

```
GET /logs/user/7?limit=50&offset=0
```

---

## What Gets Logged

- Every HTTP request (GET, POST, PATCH, DELETE)
- Method, endpoint path, status code
- Response time in milliseconds
- IP address of the caller
- User ID (extracted from JWT token if present)

Logs are useful for:
- Debugging issues
- Auditing who changed what and when
- Monitoring performance (slow endpoints)
- Investigating suspicious activity
