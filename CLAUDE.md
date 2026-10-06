# CLAUDE.md

Small Express (CommonJS, Node 22) REST API serving `/users` and `/health` from an in-memory store.

## Commands

- `npm run dev`: start the API with `node --watch` on `PORT` (default 3000)
- `npm test`: run all tests with the built-in `node --test` runner
- `node --test --test-name-pattern="GET /health" tests/`: run a single test by name
- `npm run lint`: ESLint (`eslint:recommended`)

CI (`.github/workflows/ci.yml`) runs `npm install`, then lint, then tests on every push and PR. Both must pass.

## Conventions

- CommonJS (`require` / `module.exports`), not ES modules. ESLint is configured with `sourceType: "script"`.
- A new resource gets its own `routes/<name>.js` mounted in `server.js`, with data access added as functions in `db/store.js`.
- Errors are returned as `res.status(<code>).json({ error: "<message>" })`; use `return` on early error responses.
- Route params arrive as strings: convert with `Number(req.params.id)` before comparing with store ids.
- Tests use `node:test` and `supertest`, not Jest or Mocha. Don't add a test framework.
- Config comes from `process.env`; `.env.example` documents keys. Never read or commit `.env`.

## Architecture

- `server.js` builds the Express app, mounts one router per resource, and exports `app`. It only calls `listen()` when run directly (`require.main === module`), so tests import `app` without opening a port.
- `routes/<resource>.js` holds one `express.Router` per resource, mounted in `server.js` at `/<resource>`.
- `db/store.js` is the only data layer: a module-level array that resets on restart. Routes never touch the data directly.
- `tests/` uses `node:test` + `node:assert` + `supertest` against the exported `app`. Store state is shared across tests in a run, so don't assume a fixed user count.
