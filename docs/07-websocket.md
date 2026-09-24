# Real-time WebSocket

The WebSocket connection is how riders stay connected to the system in real time. It serves two purposes:
1. **Receiving new orders** — the server pushes orders to the rider instantly
2. **Sending location updates** — the rider streams their GPS position continuously

---

## Connecting

```
ws://your-server/orders/ws/{rider_id}?token=<jwt_token>
```

The rider must pass their JWT token as a query parameter. The server validates it before accepting the connection. If the token is invalid or belongs to a different rider, the connection is rejected immediately.

**On successful connection, the server sends:**
```json
{
  "event": "connected",
  "rider_id": 7,
  "message": "Connected successfully. Waiting for orders..."
}
```

---

## Receiving a New Order

When a restaurant webhook comes in and this rider is assigned, they receive:

```json
{
  "event": "new_order",
  "order_id": 101,
  "restaurant_name": "Burger House",
  "restaurant_address": "King Fahd Rd, Riyadh",
  "restaurant_lat": 24.7136,
  "restaurant_lng": 46.6753,
  "items_summary": "1x Burger, 2x Fries",
  "total_amount": 45.50,
  "has_qr_check": false,
  "eta_to_branch_mins": 7.5,
  "distance_to_branch_km": 1.8,
  "routing_source": "osrm",
  "customer": {
    "name": "Sara Al-Otaibi",
    "phone": "+966507654321",
    "address": "Al Olaya District, Building 12",
    "latitude": 24.7890,
    "longitude": 46.7100
  }
}
```

The rider's mobile app displays this and the rider accepts or acts accordingly.

---

## Sending Location Updates

The rider's app sends location updates over the same connection:

```json
{
  "event": "location_update",
  "latitude": 24.7180,
  "longitude": 46.6790
}
```

**How location is handled:**
- Every update is saved to **Redis immediately** (used for order dispatch)
- The **database** is only updated every 60 seconds (to reduce DB load)

This means the system always has near-real-time rider positions for dispatch, without hammering the database on every GPS ping.

---

## Disconnection

When a rider disconnects (closes the app, loses signal, etc.):
- Their WebSocket connection is removed
- Their location is **deleted from Redis** — they are no longer considered "active"
- They won't receive new orders until they reconnect

---

## Push Notifications (FCM) as Backup

If a rider is not connected via WebSocket (app is in background), new orders also trigger a **Firebase push notification** to their device — provided the rider has registered their FCM token. See [Riders](./02-riders.md#register-fcm-token).
