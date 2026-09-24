# Restaurants & Webhook

Restaurants connect to the system using a **webhook**. When a customer places an order on the restaurant's platform, the restaurant sends the order details to this API, which handles all dispatch automatically.

---

## Adding a Restaurant (Admin)

**`POST /restaurants/`** — Admin only

```json
{
  "name": "Burger House",
  "latitude": 24.7136,
  "longitude": 46.6753,
  "address": "King Fahd Rd, Riyadh",
  "phone": "+966501234567",
  "webhook_secret": "bh-secret-xyz789"
}
```

Each restaurant gets a unique `webhook_secret`. This is like a password — only requests with the correct secret are accepted. Keep it safe and share it only with the restaurant's technical team.

---

## Get All Restaurants (Admin)

**`GET /restaurants/`** — Admin only

Returns all registered restaurants.

---

## Receiving an Order via Webhook

**`POST /webhook/order`** — No auth required (secured by `webhook_secret`)

This is the endpoint the restaurant's system calls when a new order is placed. The restaurant includes its `webhook_secret` in every request.

**Request:**
```json
{
  "webhook_secret": "bh-secret-xyz789",
  "external_order_id": "ORD-2024-5521",
  "customer_name": "Sara Al-Otaibi",
  "customer_phone": "+966507654321",
  "customer_latitude": 24.7890,
  "customer_longitude": 46.7100,
  "customer_address": "Al Olaya District, Building 12",
  "items_summary": "1x Burger, 2x Fries, 1x Coke",
  "total_amount": 45.50,
  "has_qr_check": false
}
```

**What happens automatically:**
1. Verifies the `webhook_secret` against registered restaurants
2. Checks if this `external_order_id` was already received (prevents duplicate orders)
3. Saves the customer info
4. Finds all active riders from Redis (real-time locations)
5. Calculates road distance to the restaurant using OSRM
6. Assigns the closest rider
7. Notifies rider via WebSocket + FCM push

**Response:**
```json
{
  "message": "Order assigned successfully.",
  "order_id": 101,
  "assigned_rider": "Ahmed Hassan",
  "distance_to_branch_km": 1.8,
  "eta_to_branch_mins": 7.5,
  "routing_source": "osrm"
}
```

---

## Idempotency (Duplicate Prevention)

If the restaurant sends the same `external_order_id` twice (e.g., due to a retry), the API **won't create a duplicate order**. It returns the existing order's details instead. This is handled automatically.

---

## No Active Riders?

If no riders are currently active (no location updates in Redis), the webhook returns:

```json
{
  "detail": "No active riders available."
}
```
Status code: `503 Service Unavailable`

The restaurant system should handle this case and retry or alert staff.
