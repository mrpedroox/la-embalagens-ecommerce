# L&A Embalagens — Backend

Ecommerce API for L&A Embalagens (packaging and party supplies / bomboniere), built with FastAPI. The checkout flow is inspired by the Pague Menos website: cart → delivery choice (pickup or home delivery) → payment confirmation. The project focuses on real backend problems — safe inventory concurrency, an order state machine, asynchronous payment processing, and caching — not just product/order CRUD.

> 🚧 Actively in development. See [Status](#status) for current progress.

## Table of contents

- [Why this project](#why-this-project)
- [Stack](#stack)
- [Architecture](#architecture)
- [Project structure](#project-structure)
- [Running locally](#running-locally)
- [Tests](#tests)
- [Technical decisions](#technical-decisions)
- [Status](#status)

## Why this project

Most portfolio ecommerce projects stop at product/order CRUD. This one goes further, implementing:

- **Optimistic locking on inventory** — prevents overselling when two requests race for the last unit of a product
- **Order state machine** with an auditable history (`pending → awaiting_payment → paid → preparing → out_for_delivery → delivered`)
- **Pickup or delivery** — an order can be picked up in-store or shipped to one of the customer's saved addresses
- **Asynchronous payment flow** — manually confirmed in the MVP, with a task queue and an idempotent webhook endpoint already in place for a real payment gateway later
- **Catalog caching** with Redis invalidation
- **Concurrency tests** proving the inventory solution actually holds up under simultaneous load

## Stack

| Layer | Technology |
|---|---|
| API | [FastAPI](https://fastapi.tiangolo.com/) (async) |
| ORM | [SQLModel](https://sqlmodel.tiangolo.com/) |
| Database | PostgreSQL |
| Migrations | Alembic |
| Cache / broker | Redis |
| Async task queue | [arq](https://github.com/samuelcolvin/arq) |
| Authentication | OAuth2 + JWT |
| Payments | Stripe (sandbox) |
| Containerization | Docker + Docker Compose |
| Tests | pytest + httpx |

## Architecture

```
Client → FastAPI ──┬─→ PostgreSQL (transactional data)
                    ├─→ Redis (catalog cache)
                    └─→ Queue (arq) ──→ Async worker
                                              ├─→ Payment confirmation
                                              ├─→ Inventory deduction
                                              └─→ Email delivery
```

**Checkout flow (summary):**

1. Customer builds the cart, chooses pickup or delivery (with a saved address), and checks out → API creates an `order` (`pending`) and a `payment` (`awaiting`)
2. MVP: an admin manually confirms the payment. Advanced phase: the API creates a payment session with the gateway and returns a checkout URL
3. Payment confirmation (manual or via the gateway's webhook) → a task is enqueued
4. The worker processes the task: confirms the payment, transitions the order to `paid`, deducts inventory for real (optimistic locking), sends a confirmation email
5. Unpaid orders automatically expire via a scheduled task, releasing the reserved inventory

## Project structure

```
app/
├── main.py            # FastAPI app creation
├── core/              # config, database, security, dependencies
├── auth/              # User, Session — registration, login, JWT
├── addresses/           # Address — multiple saved addresses per user
├── catalog/           # Category, Product
├── inventory/          # optimistic locking on stock
├── cart/              # shopping cart
├── orders/             # Order, OrderItem, state machine
├── payments/           # Payment — manual confirmation (MVP) + future gateway
├── notifications/        # async email sending
└── worker/             # task queue setup and scheduled tasks

tests/                 # tests per module, including the concurrency test
alembic/                # database migrations
db/                   # reference schema.sql + seed.sql
```

## Running locally

Prerequisites: Docker and Docker Compose.

```bash
# clone the repository
git clone <repository-url>
cd ecommerce-backend

# copy environment variables
cp .env.example .env

# start the services (api, database, redis)
docker compose up -d

# apply migrations
docker compose exec api alembic upgrade head
```

The API will be available at `http://localhost:8000`, with interactive docs at `http://localhost:8000/docs`.

## Tests

```bash
docker compose exec api pytest
```

Highlight: `tests/inventory/test_concurrency.py` simulates two concurrent requests competing for the last unit in stock, proving that only one of them succeeds in reserving it.

## Technical decisions

- **Optimistic locking instead of `SELECT FOR UPDATE`**: avoids holding row locks under high concurrency, preferring to detect conflicts via a `version` column and retry.
- **`CHECK` constraints instead of native Postgres `ENUM`** for order status, delivery type, and payment method: easier to change as the project evolves, without `ALTER TYPE`.
- **Line-item price is "frozen" in `order_item`**: doesn't reference `product.price` directly, so future price changes don't retroactively affect past orders.
- **`Payment` as a separate entity from `Order`**: leaves room for multiple payment attempts per order and for future gateway integration via an idempotent webhook.
- **Manual payment confirmation in the MVP**: reduces initial complexity without requiring a remodel once a real gateway (Stripe/Mercado Pago) is integrated later.

## Status

- [x] Architecture and data modeling (ER diagram + SQL schema)
- [ ] Environment setup (Docker Compose, Alembic)
- [ ] Authentication (registration/login with JWT)
- [ ] Addresses (CRUD for user addresses)
- [ ] Catalog (products, categories)
- [ ] Cart
- [ ] Orders: pickup/delivery + inventory with optimistic locking
- [ ] Payment (manual confirmation in the MVP → gateway later)
- [ ] Caching, load tests, and deployment

---

