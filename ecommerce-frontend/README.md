# L&A Embalagens — Frontend

Frontend application for L&A Embalagens, a packaging and party-supplies ecommerce business.

The application will be built with React, Vite, and TypeScript. It will consume the FastAPI API and provide registration, authentication, catalog, cart, checkout, and order flows.

> 🚧 The frontend is in the planning phase. The stack, feature-based organization, and authentication strategy are defined, but the application has not yet been implemented.

## Defined stack

| Layer | Technology |
|---|---|
| Framework | React + Vite |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Routing | react-router |
| HTTP client | axios or an equivalent fetch-based wrapper |
| Tests | Vitest and React Testing Library |

## Backend integration

The frontend will consume the FastAPI API through REST.

```text
React + Vite → FastAPI API → PostgreSQL / Redis / Worker
```

API contracts will be defined alongside incremental backend work. Screens must not assume endpoints, fields, or rules that have not yet been formalized.

## Planned project structure

```text
ecommerce-frontend/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── routes.tsx
│   │
│   ├── api/
│   │   ├── client.ts
│   │   ├── auth.ts
│   │   ├── catalog.ts
│   │   ├── cart.ts
│   │   └── orders.ts
│   │
│   ├── features/
│   │   ├── auth/
│   │   ├── catalog/
│   │   ├── cart/
│   │   ├── checkout/
│   │   └── orders/
│   │
│   ├── components/
│   │   ├── ui/
│   │   ├── Navbar.tsx
│   │   ├── Footer.tsx
│   │   └── ProtectedRoute.tsx
│   │
│   ├── hooks/
│   ├── lib/
│   ├── types/
│   └── styles/
│       └── globals.css
│
└── tests/
```

The structure will be created as each flow is implemented. Generic components must remain in `components/`, while feature-specific components belong to their corresponding feature.

## Planned functionality

### Authentication

- registration;
- login;
- authenticated-user lookup;
- protected routes for authenticated sessions.

### Catalog

- product listing;
- product detail pages;
- stock availability indicators;
- category navigation when supported by the backend.

### Cart and checkout

- cart-item management;
- pickup or delivery selection;
- saved-address selection for delivery;
- order confirmation and the payment method available in the MVP.

### Orders

- customer order history;
- order detail view;
- presentation of the current order status.

## Authentication strategy

The backend will issue a JWT on login, but the token will be stored in an `httpOnly` cookie rather than `localStorage`.

This reduces token exposure to scripts running in the browser. The implementation must ensure that:

- the backend sets the cookie in the login response;
- the HTTP client sends requests with credentials included;
- the backend configures CORS for the frontend origin with credential support;
- cookie attributes such as `SameSite` and `Secure` are configured for the environment.

The HTTP client must not depend on reading the JWT in JavaScript or adding it manually to each request.

## Visual direction

The visual identity remains to be defined before screen implementation begins.

An early exploration used an editorial aesthetic with earthy tones, but it does not represent the real brand. L&A Embalagens has a more energetic identity, with orange and green references. The palette, typography, visual components, layout, and brand assets still need to be defined.

## Local development

Run instructions will be added when the following are available:

- initial Vite project;
- `package.json` and a lockfile;
- Tailwind configuration;
- frontend Dockerfile;
- test configuration;
- `VITE_API_URL` environment variable.

The application will use `VITE_API_URL` to define the API URL, with `http://localhost:8000` as the expected local-development value.

## Status

- [x] Stack defined
- [x] Feature-based structure planned
- [x] Authentication strategy defined
- [ ] L&A visual identity adapted and documented
- [ ] React + Vite + TypeScript base project
- [ ] Tailwind CSS and generic components
- [ ] Routes and credential-aware HTTP client
- [ ] Registration and login pages
- [ ] Product catalog
- [ ] Cart
- [ ] Pickup or delivery checkout
- [ ] Order history and details
- [ ] Interface tests
