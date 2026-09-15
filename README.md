# Lendsqr Wallet Backend

A production-shaped wallet service for a mobile lending app, built in Node.js + TypeScript. Users can create accounts, fund a wallet, transfer funds peer-to-peer, and withdraw — with every onboarding blocked against the Lendsqr Adjutor "Karma" blacklist before an account is created.

## Why it's structured this way

This started as the Lendsqr backend engineering assessment (the "Demo Credit" wallet brief) and is built the way a real fintech wallet service would be: layered architecture, typed request/response contracts, and tests that cover both the happy path and the failure modes that actually matter in a ledger system — insufficient funds, duplicate transfers, unauthenticated access.

## Architecture

```
src/
├── routes/         auth + wallet route definitions
├── controllers/     request handling, delegates to services
├── services/         auth.service.ts, wallet.service.ts — business logic and the ledger operations
├── middleware/       JWT auth middleware
├── validators/        request-shape validation
├── config/            database.ts (Knex/MySQL), swagger.ts (interactive API docs)
└── __tests__/          Jest + Supertest integration tests against a real test database
```

- **KnexJS** as the SQL query builder/ORM against **MySQL**, with migrations under `src/database/migrations`
- **JWT** for session auth
- **Lendsqr Adjutor API** integration — every new user is checked against the Karma blacklist before an account is created, so onboarding a previously-flagged user fails closed
- **Swagger** (`swagger-jsdoc` + `swagger-ui-express`) serving interactive API docs at `/api-docs`
- **Jest + Supertest** integration tests covering registration, auth, and wallet operations (funding, balance, transfer, withdrawal) for both success and failure cases

## API

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/register` | Create an account (blocked if the user is Karma-blacklisted) |
| POST | `/api/auth/login` | Authenticate, receive a JWT |
| GET | `/api/wallets/balance` | Current wallet balance |
| POST | `/api/wallets/fund` | Fund a wallet |
| POST | `/api/wallets/transfer` | Transfer funds to another user |
| POST | `/api/wallets/withdraw` | Withdraw funds |

Full request/response schemas are in the Swagger UI once the server is running.

## Setup

```bash
npm install
cp .env.example .env   # fill in your own DB credentials and Adjutor API key
npm run migrate
npm run dev
```

Run the test suite:

```bash
npm test
```

## Stack

TypeScript · Node.js · Express · KnexJS · MySQL · JWT · Jest · Supertest · Swagger
