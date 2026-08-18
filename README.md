# L&A Embalagens — Full Stack Ecommerce

An ecommerce platform for packaging and party supplies.

The project will consist of a FastAPI backend and a React frontend. Its architecture focuses on real ecommerce concerns, including safe inventory concurrency, order lifecycle management, asynchronous processing, and the usual catalog, cart, and checkout features.

> 🚧 The project is in its initial preparation phase. The architecture and organization are documented, but the implementation, dependencies, and infrastructure files have not yet been created.

## Architecture overview

```text
React + Vite ── REST ──▶ FastAPI
                            ├─ PostgreSQL: transactional data
                            ├─ Redis: cache and task broker
                            └─ arq worker: asynchronous processing
```

The planned services are:

- **frontend** — React application responsible for the shopping experience;
- **api** — FastAPI application;
- **db** — PostgreSQL for transactional data;
- **redis** — catalog cache and task broker;
- **worker** — asynchronous processing with arq.

## Planned checkout flow

```text
Cart → pickup or delivery choice → payment confirmation
```

At a high level:

1. The customer adds products to the cart.
2. They choose store pickup or delivery to a saved address.
3. The API creates a pending order and a pending payment.
4. In the MVP, an administrator manually confirms the payment.
5. An asynchronous task confirms the payment, updates the order, processes inventory, and sends a notification.
6. Unpaid orders will expire according to rules that will be defined during implementation.

## Repository structure

```text
la-embalagens-ecommerce/
├── README.md
├── docker-compose.yml
├── ecommerce-backend/
│   └── README.md
└── ecommerce-frontend/
    └── README.md
```

At the current stage, the backend and frontend directories contain architecture and planning documentation only.

## Project documentation

- [Backend](./ecommerce-backend/README.md) — FastAPI API, PostgreSQL, Redis, worker, and domain decisions.
- [Frontend](./ecommerce-frontend/README.md) — React stack, feature-based organization, and authentication strategy.

## Current status

Already defined:

- overall architecture;
- backend and frontend stacks;
- services planned in Docker Compose;
- modular organization;
- core inventory, order, payment, and authentication decisions.

Not implemented yet:

- backend and frontend code;
- Dockerfiles;
- Python and Node.js dependency files;
- example environment file;
- database migrations and schema;
- tests;
- Alembic, Vite, Tailwind, and quality-tool configuration.

## Next steps

1. Prepare the executable backend foundation and its infrastructure.
2. Configure PostgreSQL, Redis, Alembic, and tests.
3. Implement backend domains incrementally.
4. Prepare the frontend foundation and integrate authentication.
5. Build catalog, cart, checkout, and order flows vertically.

## Running the project

Run instructions will be added when Dockerfiles, dependency files, and the example environment file are available.
