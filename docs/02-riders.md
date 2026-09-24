# Riders

Riders are the delivery personnel in your fleet. Admins manage them; riders can view their own profile and update their location.

---

## Create a Rider

**`POST /riders/`** — Admin only

```json
{
  "firstname": "Ahmed",
  "lastname": "Hassan",
  "username": "ahmed_h",
  "email": "ahmed@example.com",
  "password": "securepass123",
  "age": 25,
  "nationality": "Saudi",
  "id_document": "DOC-001",
  "contract_id": "CNT-2024-01",
  "pay_type": "per_order",
  "per_order_rate": 5.00,
  "vehicle_type": "motorcycle",
  "license_number": "LIC-12345",
  "license_expiry": "2026-12-31"
}
```

**Pay types:**
- `salary` — rider gets a fixed monthly salary (`fixed_salary_amount`)
- `per_order` — rider gets paid per delivery (`per_order_rate`)

**Vehicle types:** `motorcycle`, `car`, `bicycle`, `on_foot`

---

## Get All Riders

**`GET /riders/`** — Admin only

Returns all riders ordered by newest first.

---

## Get Active Riders

**`GET /riders/active`** — Admin only

Returns only riders who have sent a location update in the **last 10 minutes**. This is how you know who is currently on the road.

---

## Get a Specific Rider

**`GET /riders/{rider_id}`**

Admins can view any rider. Riders can only view themselves.

---

## Update Rider Info

**`PATCH /riders/{rider_id}`** — Admin only

Update any combination of these fields:

```json
{
  "firstname": "Ahmed",
  "pay_type": "salary",
  "fixed_salary_amount": 3000.00
}
```

---

## Delete a Rider

**`DELETE /riders/{rider_id}`** — Admin only

Permanently removes the rider from the system.

---

## My Profile (Rider)

**`GET /riders/profile`** — Rider only

A rider calls this to get their own profile without needing to know their ID.

---

## Update Location (REST)

**`PATCH /riders/{rider_id}/location`** — Rider only (own location)

```json
{
  "latitude": 24.7136,
  "longitude": 46.6753
}
```

> **Note:** For continuous tracking, use the WebSocket instead. This REST endpoint is for one-off updates. See [WebSocket docs](./07-websocket.md).

---

## Register FCM Token

**`PATCH /riders/fcm-token`** — Rider only

When a rider opens the mobile app, the app registers its push notification token:

```json
{
  "fcm_token": "fxyz1234..."
}
```

This is what enables push notifications when new orders arrive.

---

## Rider Object Reference

```json
{
  "id": 1,
  "firstname": "Ahmed",
  "lastname": "Hassan",
  "age": 25,
  "username": "ahmed_h",
  "email": "ahmed@example.com",
  "role": "rider",
  "nationality": "Saudi",
  "pay_type": "per_order",
  "per_order_rate": 5.00,
  "fixed_salary_amount": 0.0,
  "vehicle_type": "motorcycle",
  "license_number": "LIC-12345",
  "license_expiry": "2026-12-31",
  "latitude": 24.7136,
  "longitude": 46.6753,
  "last_location_update": "2025-01-15T10:30:00Z",
  "orders_success": 142,
  "orders_failed": 3,
  "total_orders": 145,
  "created_at": "2024-06-01T09:00:00Z"
}
```
