# Time Off

Riders can request days off. Admins review and approve them.

---

## Request Time Off

**`POST /timeoff/{rider_id}`** — Rider (own) or Admin

```json
{
  "date": "2025-01-20",
  "type": "sick",
  "reason": "Doctor's appointment"
}
```

**Time off types:**
- `day_off` — scheduled day off
- `sick` — sick leave
- `excused` — excused absence (admin-approved reason)
- `unexcused` — unexcused absence

Only one time-off record per rider per date is allowed.

---

## Get Time Off Records

**`GET /timeoff/{rider_id}`**

Returns all time-off records for a rider, newest first. Riders can only view their own records.

```json
[
  {
    "id": 12,
    "rider_id": 7,
    "date": "2025-01-20",
    "type": "sick",
    "reason": "Doctor's appointment",
    "is_approved": false,
    "created_at": "2025-01-18T09:00:00Z"
  }
]
```

`is_approved` starts as `false` and becomes `true` after admin approval.

---

## Approve Time Off (Admin)

**`PATCH /timeoff/{timeoff_id}/approve`** — Admin only

No body needed — just call the endpoint:

```
PATCH /timeoff/12/approve
```

Response includes the updated record with `is_approved: true`.
