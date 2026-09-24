# Orders

Orders are the core of the system. They are created automatically when a restaurant sends a webhook, then managed by the assigned rider.

---

## Order Lifecycle

Every order goes through these statuses in sequence:

```
PENDING → ACCEPTED → AT_BRANCH → PICKED_UP → DELIVERING → AT_CUSTOMER → DELIVERED
```

If something goes wrong:
```
→ FAILED
→ CANCELLED
→ FORWARDED
```

---

## How Orders Are Created

Orders are **not** created manually. They come in from restaurants via webhook — see [Restaurants & Webhook](./04-restaurants-webhook.md).

When a webhook arrives:
1. The API verifies the restaurant's secret
2. Finds all riders who are currently active (location in Redis)
3. Uses OSRM routing to calculate real road distance to each rider
4. Assigns the closest rider
5. Notifies the rider instantly via WebSocket
6. Sends a push notification to the rider's phone (if FCM token is registered)

---

## Update Order Status (Rider)

**`PATCH /orders/{order_id}/status`** — Rider only (own orders)

The rider updates the order status as they progress through the delivery:

```json
{
  "status": "accepted",
  "note": "On my way"
}
```

Valid status values: `pending`, `accepted`, `at_branch`, `picked_up`, `delivering`, `at_customer`, `delivered`, `faild`, `cancelled`, `forwarded`

The API automatically records timestamps for key milestones:
- `accepted_at` — when the rider accepts
- `picked_up_at` — when the rider picks up the food
- `delivered_at` — when the order is delivered

When an order is marked `delivered` or `faild`, the rider's lifetime stats (`orders_success`, `total_orders`) are updated automatically.

---

## Get Order Details (Rider)

**`GET /orders/{order_id}`** — Rider only (own orders)

Returns full order details including customer info, restaurant, status, and timestamps.

```json
{
  "id": 101,
  "external_order_id": "ORD-2024-5521",
  "resturant_id": 3,
  "customer_id": 88,
  "rider_id": 7,
  "status": "picked_up",
  "items_summary": "1x Burger, 2x Fries",
  "total_amount": 45.50,
  "has_qr_check": false,
  "qr_verified": false,
  "eta_to_branch_mins": 8.3,
  "accepted_at": "2025-01-15T11:05:00Z",
  "picked_up_at": "2025-01-15T11:18:00Z",
  "delivered_at": null,
  "created_at": "2025-01-15T11:00:00Z"
}
```

---

## Get Order Tracking History (Rider)

**`GET /orders/{order_id}/tracking`** — Rider only (own orders)

Returns every status change for the order, with the rider's GPS coordinates at each step:

```json
[
  {
    "id": 1,
    "order_id": 101,
    "status": "pending",
    "rider_latitude": 24.713,
    "rider_longitude": 46.675,
    "note": null,
    "created_at": "2025-01-15T11:00:00Z"
  },
  {
    "id": 2,
    "order_id": 101,
    "status": "accepted",
    "rider_latitude": 24.715,
    "rider_longitude": 46.678,
    "note": "On my way",
    "created_at": "2025-01-15T11:05:00Z"
  }
]
```

This creates a full audit trail and can be used to replay the delivery route on a map.

---

## QR Check

Orders can optionally require a QR code scan on delivery (`has_qr_check: true`). The `qr_code` and `qr_verified` fields on the order support this flow — useful for confirming handoff to the correct customer.
