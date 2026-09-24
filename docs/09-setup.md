# Setup & Configuration

---

## Requirements

- Python 3.11+
- PostgreSQL
- Redis
- (Optional) Firebase project for push notifications
- (Optional) OSRM server for accurate routing (falls back to Haversine straight-line distance)

---

## Environment Variables

Create a `.env` file in the project root:

```env
# Database
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/rider_db

# JWT
SECRET_KEY=your-very-long-random-secret-key-here

# Redis
REDIS_URL=redis://localhost:6379

# CORS — comma-separated list of allowed frontend origins
CORS_ORIGINS=http://localhost:3000,https://your-admin-dashboard.com

# Firebase (optional — push notifications)
FIREBASE_CREDENTIALS_PATH=backend/firebase-service-account.json
```

---

## Installation

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Create database tables
python -m backend.databases.create_tables

# 3. Start the server
uvicorn backend.main:app --host 0.0.0.0 --port 8000 --reload
```

---

## First-Time Setup

After starting the server, create your first admin:

```
POST /riders/admin
{
  "firstname": "Super",
  "lastname": "Admin",
  "username": "admin",
  "email": "admin@yourcompany.com",
  "password": "strongpassword"
}
```

Then log in with those credentials to get your admin token, and start adding riders and restaurants.

---

## API Documentation (Auto-generated)

FastAPI generates interactive documentation automatically:

| URL | Description |
|---|---|
| `/docs` | Swagger UI — try endpoints directly in the browser |
| `/redoc` | ReDoc — cleaner read-only reference |

---

## Architecture Overview

```
┌─────────────────────────────────────┐
│           Client Apps               │
│  (Admin Dashboard, Rider Mobile)    │
└────────────┬────────────────────────┘
             │ HTTP + WebSocket
┌────────────▼────────────────────────┐
│           FastAPI Server            │
│  - Auth (JWT)                       │
│  - Rider Management                 │
│  - Order Dispatch (OSRM routing)    │
│  - WebSocket Manager                │
│  - Request Logging Middleware       │
└─────┬──────────────┬────────────────┘
      │              │
┌─────▼──────┐  ┌────▼───────┐
│ PostgreSQL  │  │   Redis    │
│ (main data) │  │ (locations │
│             │  │  + cache)  │
└─────────────┘  └────────────┘
                      │
               ┌──────▼──────┐
               │  Firebase   │
               │    FCM      │
               │  (push      │
               │   notify)   │
               └─────────────┘
```

---

## Production Checklist

- [ ] Change `SECRET_KEY` to a long random string
- [ ] Set `CORS_ORIGINS` to your actual frontend domains
- [ ] Use a managed PostgreSQL service (e.g., RDS, Supabase)
- [ ] Use a managed Redis service (e.g., Upstash, ElastiCache)
- [ ] Set up HTTPS (e.g., behind Nginx or a load balancer)
- [ ] Add Firebase credentials for push notifications
- [ ] (Optional) Self-host or use a public OSRM instance for routing
