# 🚚 Rider API Manager

A production-ready **delivery rider management API** built with FastAPI. Designed for food delivery platforms, logistics companies, or any business that needs to manage a fleet of delivery riders — with real-time order dispatch, live location tracking, and full rider lifecycle management.

---

## What Does This API Do?

In simple terms: **it's the brain behind a delivery operation.**

When a restaurant gets an order, this API automatically finds the nearest available rider, sends them the order in real-time, and tracks the delivery from pickup to drop-off. At the same time, admins get full visibility into rider performance, schedules, and activity logs.

---

## Key Features

| Feature | Description |
|---|---|
| 🔐 **Authentication** | JWT-based login for both Admins and Riders |
| 🏍️ **Rider Management** | Create, update, and manage your rider fleet |
| 📍 **Real-time Location** | Riders stream live GPS via WebSocket |
| 🧭 **Smart Order Dispatch** | Automatically assigns orders to the closest available rider using OSRM routing |
| 📦 **Order Lifecycle** | Full order tracking from pending → accepted → picked up → delivered |
| 🔔 **Push Notifications** | Firebase FCM push to rider's mobile app on new orders |
| 📊 **Daily Stats** | Track each rider's performance, bonuses, and deductions per day |
| 🏖️ **Time-Off Requests** | Riders request days off; admins approve them |
| 🔗 **Webhook Integration** | Restaurants push orders directly to the API via a secure webhook |
| 📋 **Request Logs** | Every API call is automatically logged with timing and user info |
| ⚡ **Redis Caching** | Rider locations and active orders cached for ultra-fast dispatch |

---

## Tech Stack

- **Framework:** FastAPI (Python) — async, fast, auto-docs included
- **Database:** PostgreSQL via SQLAlchemy (async)
- **Cache:** Redis — rider locations + active orders
- **Real-time:** WebSocket (built into FastAPI)
- **Push Notifications:** Firebase Cloud Messaging (FCM)
- **Routing Engine:** OSRM (Open Source Routing Machine) for accurate ETA
- **Auth:** JWT (HS256) with bcrypt password hashing

---

## Documentation Index

| File | What's Inside |
|---|---|
| [Authentication](./docs/01-authentication.md) | How login works, tokens, roles |
| [Riders](./docs/02-riders.md) | Managing your rider fleet |
| [Orders](./docs/03-orders.md) | Order flow, dispatch, and status updates |
| [Restaurants & Webhook](./docs/04-restaurants-webhook.md) | Connecting restaurants to auto-dispatch |
| [Daily Stats](./docs/05-daily-stats.md) | Rider performance tracking |
| [Time Off](./docs/06-time-off.md) | Leave management |
| [Real-time WebSocket](./docs/07-websocket.md) | Live location and order push |
| [Logs](./docs/08-logs.md) | Request logging and audit trail |
| [Setup & Configuration](./docs/09-setup.md) | Environment variables and how to run |

---

## Quick Look: How an Order Works

```
Restaurant sends webhook
        ↓
API verifies webhook secret
        ↓
Finds all active riders from Redis cache
        ↓
OSRM calculates real road distance to each rider
        ↓
Closest rider is assigned
        ↓
Rider gets notified via WebSocket (instant) + FCM push (background)
        ↓
Rider updates status: accepted → picked up → delivered
        ↓
Order tracking history is recorded at every step
```

---

## Who Is This For?

- **Food delivery startups** that need a backend without building from scratch
- **Logistics companies** managing courier fleets
- **Ghost kitchen operators** coordinating multiple restaurant brands
- **Developers** who want a solid FastAPI base to build on

---

## Live API Docs

FastAPI generates interactive documentation automatically. Once running, visit:

- **Swagger UI:** `http://your-server/docs`
- **ReDoc:** `http://your-server/redoc`
