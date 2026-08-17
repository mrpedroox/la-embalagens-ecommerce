# L&A Embalagens — Full Stack Ecommerce

A packaging and party-supplies (bomboniere) ecommerce platform, built with a FastAPI backend and a React frontend. The project focuses on real backend engineering problems — safe inventory concurrency, an order state machine, and asynchronous payment processing — rather than just another product/order CRUD.

This repository contains both projects, each with its own detailed README:

- [`ecommerce-backend/`](./ecommerce-backend/README.md) — FastAPI API, PostgreSQL, Redis, async task queue
- [`ecommerce-frontend/`](./ecommerce-frontend/README.md) — React + Vite + TypeScript

## Overview

```
┌─────────────────┐        ┌──────────────────┐
│  ecommerce-frontend │  ───▶  │   ecommerce-backend  │
│   React + Vite    │  REST  │      FastAPI       │
└─────────────────┘        └──────────┬─────────┘
                                    │
                       ┌───────────┼───────────┐
                       ▼             ▼             ▼
                  PostgreSQL       Redis          Worker (arq)
                (transactional data) (cache/broker)  (payment, email)
```

**Checkout flow:** cart → delivery choice (pickup or home delivery) → payment confirmation — inspired by the Pague Menos checkout experience.

## Running the whole project

Prerequisites: Docker and Docker Compose.

```bash
git clone <repository-url>
cd ecommerce-project

# set up backend environment variables
cp ecommerce-backend/.env.example ecommerce-backend/.env

# start everything: database, redis, api, worker, and frontend
docker compose up -d

# apply database migrations
docker compose exec api alembic upgrade head
```

| Service | URL |
|---|---|
| Frontend | http://localhost:5173 |
| API | http://localhost:8000 |
| API docs (Swagger) | http://localhost:8000/docs |

## Repository structure

```
ecommerce-project/
├── README.md                 # this file
├── docker-compose.yml           # orchestrates all services
├── ecommerce-backend/            # FastAPI API (see its own README)
└── ecommerce-frontend/           # React app (see its own README)
```

## Project status

See detailed progress in each project's README:
- [Backend status](./ecommerce-backend/README.md#status)
- Frontend status — in progress

---

