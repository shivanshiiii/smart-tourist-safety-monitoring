# Sentinel Trail — Smart Tourist Safety Monitoring & Incident Response System

A full-stack prototype built for Smart India Hackathon 2025 (Problem Statement 25002),
integrating **Digital Tourist IDs**, **offline-capable SOS alerts**, and **geo-fencing**
into one real-time safety platform, with a **rule-based AI anomaly detection** engine
and a **live monitoring dashboard** for police/tourism department operators.

React (Vite) · Node.js / Express · Firebase (Firestore + real-time sync) · Socket.IO · Leaflet

## What's in here

- **`backend/`** — Express API: Digital ID issuance & verification, location
  ingestion, geofencing, anomaly detection, SOS + auto-drafted E-FIRs, Socket.IO
  real-time push. Runs against Firestore if configured, or an in-memory store
  out of the box — see [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).
- **`frontend/`** — React web app with two views: the tourist-facing app
  (register → get a Digital ID → SOS button → simulated live trip) and the
  Command Center dashboard (live map, alert feed, E-FIR list).
- **`docs/`** — [Architecture](docs/ARCHITECTURE.md), [API reference](docs/API.md),
  and the [blockchain-based identity verification proposal](docs/BLOCKCHAIN_PROPOSAL.md)
  — a forward-looking design for anchoring Digital IDs on-chain, written as a
  concrete migration path from what's actually implemented today.

## Quickstart

Requires Node.js 18+.

```bash
# 1. Backend
cd backend
npm install
cp .env.example .env      # defaults work out of the box, no Firebase project required
npm run dev                # http://localhost:4000

# 2. Frontend (new terminal)
cd frontend
npm install
cp .env.example .env
npm run dev                # http://localhost:5173

# 3. (optional) seed a couple of demo tourists
cd backend && npm run seed
```

Open `http://localhost:5173` for the tourist app, and `/dashboard` for the
Command Center. Register a tourist, press **Start simulated trip** to walk a
sample route into a seeded high-risk zone, and watch alerts land on the
dashboard in real time — or press the **SOS** button to see the full
panic-alert → auto-E-FIR flow.

### Connecting a real Firebase project

The app runs on an in-memory store by default so it works immediately after
cloning. To use real Firestore: create a Firebase project, generate a service
account key, and fill in the `FIREBASE_*` values in `backend/.env` (see
`.env.example` for the exact fields). No code changes are needed — 
`backend/src/lib/store.js` switches automatically.

## What each resume bullet maps to in this repo

- **"Built a full-stack mobile prototype… Digital Tourist IDs, offline SOS
  alerts, and geo-fencing into a single real-time platform"** → `backend/src/services/idService.js`,
  `backend/src/routes/sos.js`, `backend/src/services/geofencing.js`, wired
  together in `backend/src/routes/location.js`.
- **"AI-based anomaly detection logic alongside a real-time monitoring
  dashboard, using Firebase for live data sync and state management"** →
  `backend/src/services/anomalyDetection.js` (inactivity, route deviation,
  speed anomaly, distress-pattern detectors) and `frontend/src/pages/Dashboard.jsx`,
  synced via `backend/src/lib/store.js` (Firestore-backed when configured)
  and `backend/src/sockets/index.js`.
- **"Designed the underlying data architecture, including a forward-looking
  proposal for blockchain-based identity verification"** →
  [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) and
  [`docs/BLOCKCHAIN_PROPOSAL.md`](docs/BLOCKCHAIN_PROPOSAL.md).

## Notes on scope

This is a hackathon-grade prototype, not a production system. In particular:
SMS relay (Twilio) and blockchain anchoring are wired for but not required to
run the demo; the anomaly detector is an explainable rule engine rather than a
trained model, chosen deliberately since its output can trigger a police
dispatch; and auth/access-control on the dashboard is out of scope for this
version.
