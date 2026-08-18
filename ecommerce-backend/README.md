# L&A Embalagens — Backend

Ecommerce API for L&A Embalagens, a packaging and party-supplies business.

The backend will be built with FastAPI and will focus on real ecommerce concerns: safe inventory concurrency, an order state machine, asynchronous processing, payments, and catalog caching.

> 🚧 The backend is in the preparation phase. The architecture, stack, and primary technical decisions are defined, but code, dependencies, migrations, and tests have not yet been created.

## Backend goals

The planned checkout flow is:

```text
Cart → pickup or delivery choice → payment confirmation
```

The solution will cover:

- safe inventory handling under concurrent requests;
- orders with an auditable state-change history;
- store pickup or delivery to a saved address;
- manual payment confirmation in the MVP;
- asynchronous task processing;
- Redis-backed catalog caching;
- a foundation for a future payment-gateway integration.

## Defined stack

| Layer | Technology |
|---|---|
| API | Async FastAPI |
| Language | Python |
| ORM | SQLModel |
| Database | PostgreSQL |
| Migrations | Alembic |
| Cache and broker | Redis |
| Async queue | arq |
| Authentication | OAuth2 + JWT |
| Future payments | Stripe sandbox or an equivalent gateway |
| Containerization | Docker and Docker Compose |
| Tests | pytest and httpx |

## Planned architecture

```text
Client → FastAPI ──┬─→ PostgreSQL
                    ├─→ Redis
                    └─→ arq queue ──→ Async worker
                                            ├─→ payment confirmation
                                            ├─→ inventory processing
                                            ├─→ order expiration
                                            └─→ email notifications
```

Responsibilities:

- **FastAPI** exposes the REST API, validates requests, and coordinates use cases.
- **PostgreSQL** stores transactional data such as users, products, orders, payments, and history.
- **Redis** provides catalog caching and the worker queue broker.
- **The arq worker** executes long-running or asynchronous tasks without blocking the API.

## Planned checkout flow

1. The customer builds a cart.
2. They select store pickup or delivery to a saved address.
3. The API creates a pending order and pending payment.
4. In the MVP, an administrator manually confirms payment.
5. Confirmation enqueues a task.
6. The worker confirms payment, updates the order state, processes inventory, and sends a notification.
7. Unpaid orders will expire according to rules to be detailed during implementation.

The exact point at which inventory is reserved, actually deducted, and released after expiration must be formalized before checkout is implemented.

## Planned project structure

```text
ecommerce-backend/
├── app/
│   ├── main.py
│   ├── core/              # configuration, database, security, dependencies
│   ├── auth/              # users, session, registration, login
│   ├── addresses/         # saved user addresses
│   ├── catalog/           # categories and products
│   ├── inventory/         # concurrent inventory control
│   ├── cart/              # shopping cart
│   ├── orders/            # orders, items, state machine
│   ├── payments/          # payments and manual MVP confirmation
│   ├── notifications/     # asynchronous notifications
│   └── worker/            # arq queue and scheduled tasks
├── tests/
├── alembic/
└── db/                    # reference schema and development data
```

This structure will be created incrementally. Modules must maintain clear responsibilities and avoid circular dependencies.

## Defined technical decisions

### Inventory

Inventory will use **optimistic locking**, with a version column used to detect concurrent conflicts and allow controlled retries.

`SELECT FOR UPDATE` must not replace this approach without explicit justification and approval. The implementation must include concurrency tests proving that two simultaneous attempts cannot sell the same final unit.

### Orders

Orders will use an auditable state machine. The planned states are:

```text
pending → awaiting_payment → paid → preparing → out_for_delivery → delivered
```

Allowed transitions, the actor responsible for each transition, and cancellation or failure states still need to be defined before implementation.

### Payments

`Payment` will be a separate entity from `Order`. This supports multiple payment attempts and prepares the system for a later gateway integration.

The MVP will use manual confirmation. A real Stripe or other payment-gateway integration must not be introduced before there is a defined need. When added, it must use an idempotent webhook.

### Historical data

Each product price must be copied and preserved in the order item. Future catalog price changes must not alter existing orders.

### Schema

Order status, delivery type, and payment method will use `CHECK` constraints instead of PostgreSQL native `ENUM` types, retaining more flexibility for change.

Schema changes will use Alembic migrations. Applied migrations must not be edited; later changes require a new migration.

## Local development

Run instructions will be added when the following are available:

- backend Dockerfile;
- Python dependency file;
- `.env.example`;
- Alembic configuration;
- initial FastAPI application;
- initial migrations.

## Status

- [x] Stack and overall architecture defined
- [x] Modular organization planned
- [x] Core inventory, order, payment, and historical-data decisions defined
- [ ] Detailed data modeling and ER diagram
- [ ] Initial FastAPI structure
- [ ] Dockerfile and environment configuration
- [ ] PostgreSQL, Redis, and Alembic configuration
- [ ] Initial migrations
- [ ] Authentication
- [ ] Addresses
- [ ] Catalog
- [ ] Cart
- [ ] Orders and optimistic-locking inventory
- [ ] Manual payment confirmation and worker
- [ ] Caching, notifications, and order expiration
- [ ] Unit, integration, and concurrency tests
