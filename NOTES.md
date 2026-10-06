# Notes

## What's in CLAUDE.md, and what I left out

I kept four short sections. A one-line description. Commands: `dev`, `test`, a single-test command, `lint`, and what CI checks. Conventions: rules Claude would otherwise have to guess, like CommonJS rather than ES modules, the `{ error }` response shape, converting route params with `Number()` before lookups, and sticking with `node:test` + `supertest` instead of adding a test framework. Architecture: the few facts that take reading several files to piece together. `server.js` only calls `listen()` when run directly, so tests can import `app`. Each resource has its own router. `db/store.js` is the only data layer, and it's in-memory, so its state carries over between tests.

I left out a file-by-file listing, since Claude can see the tree itself, and generic advice like "write good tests". I also left out the course instructions and anything secret: `.env` is mentioned only as something never to read or commit. Every line is something Claude couldn't get from a single file, or would likely get wrong.

While setting up, I found that most `.gitignore` lines had leading spaces, so git wasn't matching them and `.env` was not actually ignored. I fixed that in the same commit.

## Permission rules

- **Allow:** `npm test`, `npm run lint`, `node --test`. These only read and check code, and Claude runs them constantly, so asking every time is just friction.
- **Ask:** `git push` and `npm install`. A push publishes my work, and an install changes dependencies and the lockfile. Both are fine sometimes, but I want to decide each time.
- **Deny:** reading `.env` (both the `Read` tool and `cat .env`), plus `git push --force` / `-f`. Without the `.env` rule, Claude could read real secrets into the conversation while debugging config, and from there they could end up in a log, a commit, or a PR description. Without the force-push rule, one mistaken command could overwrite remote history that other people depend on.

These rules match by prefix, so they're a guardrail, not a security boundary. For example, `git push origin main --force` doesn't start with `git push --force`. It still lands on the `ask` rule for `git push`, so I'd see it before it ran.

## Verification

- [X] `/memory` lists `CLAUDE.md` as loaded. <!-- fill in after checking -->
- [X] `/permissions` shows the allow / ask / deny rules above. <!-- fill in after checking -->
- [X] Asked "How do I run the tests here?" and got the answer from CLAUDE.md. <!-- fill in after checking -->
