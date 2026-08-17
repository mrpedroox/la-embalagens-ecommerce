# L&A Embalagens — Frontend

React frontend for the L&A Embalagens ecommerce (packaging and party supplies / bomboniere). Consumes the [FastAPI backend](../ecommerce-backend/README.md) and implements user registration/login, product browsing, cart, and a checkout flow with pickup or delivery — inspired by the Pague Menos website.

> 🚧 Planning stage. Structure and stack are defined; implementation hasn't started yet. See [Status](#status).

## Table of contents

- [Stack](#stack)
- [Project structure](#project-structure)
- [Design direction](#design-direction)
- [Auth strategy](#auth-strategy)
- [Running locally](#running-locally)
- [Status](#status)

## Stack

| Layer | Technology |
|---|---|
| Framework | React + [Vite](https://vitejs.dev/) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Routing | react-router |
| HTTP client | axios (or fetch wrapper) with an auth interceptor |
| Tests | Vitest + React Testing Library |

## Project structure

```
src/
├── main.tsx
├── App.tsx
├── routes.tsx              # route definitions (react-router)
│
├── api/
│   ├── client.ts             # axios/fetch instance with token interceptor
│   ├── auth.ts               # /register, /login, /me calls
│   ├── catalog.ts
│   ├── cart.ts
│   └── orders.ts
│
├── features/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   ├── RegisterPage.tsx
│   │   ├── useAuth.ts          # authentication context hook
│   │   └── AuthContext.tsx
│   │
│   ├── catalog/
│   │   ├── ProductListPage.tsx
│   │   ├── ProductDetailPage.tsx
│   │   └── components/
│   │       ├── ProductCard.tsx
│   │       └── StockBadge.tsx
│   │
│   ├── cart/
│   │   ├── CartPage.tsx
│   │   ├── CartContext.tsx
│   │   └── components/
│   │       └── CartItem.tsx
│   │
│   ├── checkout/
│   │   ├── CheckoutPage.tsx         # delivery choice + payment method
│   │   └── OrderConfirmationPage.tsx
│   │
│   └── orders/
│       ├── OrderHistoryPage.tsx
│       └── OrderDetailPage.tsx
│
├── components/            # generic, reusable components
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── Modal.tsx
│   ├── Navbar.tsx
│   ├── Footer.tsx
│   └── ProtectedRoute.tsx     # route guard for authenticated pages
│
├── hooks/
│   └── useDebounce.ts
│
├── lib/
│   └── utils.ts
│
├── types/
│   ├── product.ts
│   ├── order.ts
│   └── user.ts
│
└── styles/
    └── globals.css

tests/
└── auth/
    └── LoginPage.test.tsx
```

## Design direction

Early exploration used an earthy, editorial catalog aesthetic as a design study (see `homepage-mockup.html` in project notes) — but that was built around a generic artisan-goods placeholder brand, not L&A Embalagens' real identity. L&A's actual visual identity (bright orange/green signage) is more energetic and playful than that mockup, fitting a packaging/bomboniere business. The real visual direction — palette, typography, and layout — still needs to be adapted to that identity before implementation starts.

## Auth strategy

The backend issues a JWT on login. Planned approach: **httpOnly cookie** rather than `localStorage`, so the token isn't readable by JavaScript (mitigates XSS). This requires:
- The backend to set the cookie on the `/login` response
- The frontend's API client to send requests with `credentials: 'include'`

## Running locally

Prerequisites: Node.js and the backend running (see [backend README](../ecommerce-backend/README.md)).

```bash
cd ecommerce-frontend
npm install
npm run dev
```

The app will be available at `http://localhost:5173`, expecting the API at `http://localhost:8000` (configurable via `VITE_API_URL`).

## Status

- [x] Stack decision (React + Vite + TypeScript + Tailwind)
- [x] Directory structure defined
- [ ] Visual identity adapted from L&A's real branding
- [ ] Auth pages (login/register) wired to the backend
- [ ] Product catalog browsing
- [ ] Cart
- [ ] Checkout (pickup/delivery + payment method)
- [ ] Order history

---

