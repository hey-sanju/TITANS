# TITAN Architecture Baseline

This document records the current agreed direction. Implementation details must be reconciled with the existing source code before changing schemas or APIs.

## 1. Delivery scope

Implement the admin experience first. Design domain models, APIs and authorization so captain and player experiences can be added without replacing the data model.

External tournaments involving other clans are out of scope for the current release.

## 2. Competition model

Use separate concepts for:
- Competition: recurring identity, for example TPL.
- Season: a historical edition, for example TPL Season 1.
- Tournament: the competition instance and format within a season.
- Team: persistent team identity.
- Team registration: season/tournament-specific roster and staff snapshot.
- Team fixture: one team against another.
- Individual match: one actual pairing within a team fixture.

Historical season records must remain queryable and must not be overwritten by a later season.

## 3. Team lineups and permissions

A tournament configures active players per team, roster size and substitution rules. A fixture stores a snapshot of each team's submitted lineup, bench and individual pairings.

Future captain workflows must support:
- Selecting eligible starters from the registered roster.
- Assigning individual opponents/pairings.
- Selecting substitutes.
- Submitting a complete lineup as one business action.
- Recording substitutions and actual participants for each match.

Use resource-scoped permissions. A captain can manage only their own team's permitted fixture data. Admin-only result confirmation/correction remains the default until explicitly changed.

Backend authorization is mandatory; frontend role checks are only presentation logic.

## 4. Global rating

- One persistent global rating per player across teams, seasons and official internal formats.
- Only competitions marked OFFICIAL contribute to rating.
- Unofficial events may retain results/statistics without changing rating.
- Solo, Duo and Squad use format-appropriate calculation strategies.
- Keep format-specific performance summaries separate from the global rating.
- Initial rating: 0; initial division: Unranked.
- Starting K-factor: 32, configurable through rating settings.
- Store rating transactions with before/change/after values and source match references.
- Prevent duplicate application. Correcting historical results requires deterministic replay and an audit record.
- Byes and non-participating substitutes do not affect rating.

## 5. API and frontend efficiency

APIs should represent meaningful business actions, not individual UI clicks. Keep temporary edits (for example, lineup arrangement) in local frontend state and submit the complete validated action once.

Use a frontend server-state cache (recommended: TanStack Query) for request deduplication, stale-time control, pagination and mutation-driven invalidation.

Avoid fetching the same dashboard resources repeatedly when navigating between tabs. Use grouped dashboard/read endpoints where the data is naturally consumed together, without creating oversized generic endpoints.

## 6. Redis and persistence

MongoDB remains the source of truth.

Use Redis for:
- Cache-aside reads of frequently accessed dashboards, standings, leaderboards and profiles.
- Explicit invalidation or versioned keys after relevant writes.
- Optional rate-limit counters and other justified ephemeral coordination.

Define TTLs per data type. Do not cache every endpoint by default. Never rely on Redis as the only copy of a result, rating or audit record.

Media binaries belong in object storage/CDN delivery, not Redis. Browser/CDN caching should handle images.

## 7. Result lifecycle

Separate:
1. Result submission.
2. Result confirmation.
3. Result correction.

Confirmation must be idempotent and atomically coordinate the authoritative result, rating transactions and derived data updates. Corrections require an audit reason and deterministic rating replay.

## 8. Engineering standards

- TypeScript strict mode; avoid `any`.
- Validate API input with DTOs.
- Enforce authorization in backend guards and services.
- Add database indexes for actual query patterns and unique constraints for idempotency.
- Test rating calculations, permissions, cache invalidation, duplicate submissions and historical corrections.
- Keep secrets out of Git; commit placeholder `.env.example` files only.
- Inspect existing implementations before replacing schemas or services.
