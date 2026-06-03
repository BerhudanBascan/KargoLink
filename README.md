# KargoLink — Logistics Aggregation Platform (MVP)

A centralized logistics MVP that aggregates multiple Turkish cargo carriers (Yurtiçi, Aras, MNG, and others) under a single API — enabling real-time pricing comparison, automated booking, and GPS-based shipment tracking.

## What it does

Instead of manually checking each carrier's website or calling their APIs separately, KargoLink normalizes all carrier APIs behind a unified interface. A single request returns sorted price/ETA quotes from all integrated carriers. The customer picks one, the system books it, and tracking events are aggregated in real time.

**Core flows:**
- Price comparison: query N carriers in parallel, normalize and rank results
- Booking automation: place order with selected carrier via their API
- Shipment tracking: poll carrier tracking endpoints, unify status codes
- Admin panel: carrier management, pricing rules, user support dashboard
- Bulk shipping: Excel import for multi-shipment corporate orders

## Tech Stack

| Layer | Tech |
|-------|------|
| Frontend | React, TypeScript, Tailwind CSS |
| Backend | Node.js, Express |
| Database | PostgreSQL |
| Real-time | REST polling + webhook receivers |
| Auth | JWT |
| Deployment | Docker |

## Project Structure

```
kargolink/
├── frontend/          # React + TypeScript SPA
│   ├── src/
│   │   ├── pages/     # Dashboard, Shipments, Tracking, Admin
│   │   ├── components/
│   │   └── api/       # API client layer
│   └── package.json
│
├── backend/           # Node.js REST API
│   ├── src/
│   │   ├── routes/    # /quotes, /bookings, /tracking, /carriers
│   │   ├── services/
│   │   │   ├── quotingService.ts    # Parallel carrier queries
│   │   │   ├── bookingService.ts    # Order placement
│   │   │   └── trackingService.ts   # Status aggregation
│   │   ├── adapters/  # Per-carrier API adapters (Yurtici, Aras, MNG...)
│   │   └── server.ts
│   └── package.json
│
└── docker-compose.yml
```

## API Overview

```http
POST /api/quotes          # Get price quotes from all carriers
POST /api/bookings        # Book shipment with selected carrier
GET  /api/tracking/:id    # Unified tracking status
GET  /api/carriers        # List available carriers
POST /api/bulk            # Bulk shipment upload (Excel)
```

**Quote request example:**
```json
{
  "origin": "34000",
  "destination": "06000",
  "weight": 2.5,
  "dimensions": { "w": 20, "h": 15, "d": 10 }
}
```

**Quote response:**
```json
{
  "quotes": [
    { "carrier": "Yurtici", "price": 45.90, "eta_days": 1, "service": "Express" },
    { "carrier": "Aras", "price": 38.50, "eta_days": 2, "service": "Standard" },
    { "carrier": "MNG", "price": 41.00, "eta_days": 2, "service": "Standard" }
  ]
}
```

## Setup

```bash
# Backend
cd backend
npm install
cp .env.example .env   # Add DB credentials and carrier API keys
npm run dev            # http://localhost:3001

# Frontend
cd frontend
npm install
npm run dev            # http://localhost:5173

# Docker (full stack)
docker-compose up -d
```

### Environment variables

```env
DATABASE_URL=postgresql://user:pass@localhost:5432/kargolink
JWT_SECRET=your_secret

# Carrier API keys
YURTICI_API_KEY=
ARAS_API_KEY=
MNG_API_KEY=
```

## Status

MVP — core quoting, booking, and tracking flows are implemented. Carrier adapters cover the three major Turkish carriers. Admin panel handles carrier management and shipment history. Bulk upload via Excel is functional.

## License

MIT