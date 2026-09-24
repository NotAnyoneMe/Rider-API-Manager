# Daily Stats

Admins can record daily performance data for each rider. This is used for payroll, performance reviews, and bonuses/deductions.

---

## Add Daily Stats (Admin)

**`POST /stats/{rider_id}`** — Admin only

One record per rider per day. If you try to add stats for a date that already has a record, the API returns an error.

```json
{
  "date": "2025-01-15",
  "hours_worked": 8.5,
  "target_orders": 20,
  "orders_completed": 18,
  "orders_failed": 2,
  "avg_delivery_time_mins": 22.5,
  "delay_minutes": 15.0,
  "bonus_amount": 50.00,
  "deduction_amount": 0.00,
  "excuse": null
}
```

If `date` is omitted, today's date is used automatically.

When stats are added, the rider's **lifetime counters** (`orders_success`, `total_orders`) are updated automatically.

---

## Get Rider Stats

**`GET /stats/{rider_id}`**

Returns all daily stat records for a rider, newest first. Admins can view any rider's stats; riders can only view their own.

```json
[
  {
    "id": 55,
    "rider_id": 7,
    "date": "2025-01-15",
    "hours_worked": 8.5,
    "target_orders": 20,
    "orders_completed": 18,
    "orders_failed": 2,
    "avg_delivery_time_mins": 22.5,
    "delay_minutes": 15.0,
    "bonus_amount": 50.00,
    "deduction_amount": 0.00,
    "excuse": null,
    "created_at": "2025-01-15T22:00:00Z"
  }
]
```

---

## Update Stats (Admin)

**`PATCH /stats/{stat_id}`** — Admin only

Correct a previously entered stat record. Only include the fields you want to change:

```json
{
  "bonus_amount": 75.00,
  "excuse": "Traffic jam on King Fahd Rd"
}
```
