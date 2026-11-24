# TITAN

**TITAN** is an internal eFootball/PES gaming community platform for managing players, teams, competitions, fixtures, results, global player ratings, statistics, and awards.

The platform is designed to help the TITAN community run organized competitions and identify high-performing players for future representative events.

## Project status

> Active development. The admin experience is being implemented first. Captain and player experiences are planned for a later phase.

## Repository structure

This repository is intended to be the project monorepo:

```text
TITANS/
├── apps/
│   ├── frontend/       # Next.js + TypeScript application
│   └── backend/        # NestJS + TypeScript API
├── docs/               # Architecture, API and development documentation
├── infrastructure/     # Deployment and infrastructure configuration
├── .github/            # CI workflows and repository templates
├── .gitignore
└── README.md
```

The frontend and backend will be added under `apps/` as their source is integrated. Do not commit generated folders such as `node_modules/`, `.next/`, `dist/` or local environment files.

## Core product areas

- **Players and profiles** — player identity, profile images and career information.
- **Teams and registrations** — persistent team identity, season-specific rosters and staff roles.
- **Competitions and seasons** — recurring competitions such as Titans Premier League (TPL), with preserved season history.
- **Tournaments and fixtures** — league, knockout and hybrid formats.
- **Team fixtures** — configurable active lineups, individual pairings, substitutes and match results.
- **Global ratings** — one persistent TITAN rating per player, with format-specific performance statistics.
- **Statistics and awards** — player/team performance, Golden Boot, Golden Glove, Best Player and Best Team.
- **Administration and permissions** — admin-first workflows designed to support future manager, coach, captain and player permissions.

## Key domain rules

1. A player's global TITAN rating belongs to the player, not to a team or season.
2. Only competitions explicitly marked **OFFICIAL** affect global ratings.
3. Solo, Duo and Squad results contribute to one global rating using format-appropriate calculations.
4. Solo, Duo, Squad and Custom performance summaries remain separately queryable.
5. Historical seasons, registrations, results and awards must not be overwritten by later seasons.
6. Team fixture scores are derived from their individual match results.
7. Actual participants must be recorded for each individual match, including substitutions.
8. Confirmed results and rating updates must be idempotent, auditable and safely correctable.
9. Backend authorization is authoritative. Hiding frontend controls is not a security boundary.
10. Draft lineup edits should be local until the user submits a complete business action.

## Architecture principles

- **Backend:** NestJS, TypeScript, MongoDB/Mongoose and Redis.
- **Frontend:** Next.js, TypeScript and a server-state query cache.
- **API design:** endpoints represent business actions; avoid a request for every UI interaction.
- **Caching:** frontend query caching plus backend Redis cache-aside with explicit invalidation.
- **Persistence:** MongoDB is the source of truth. Redis is a cache, not permanent storage.
- **Media:** use object storage/CDN delivery for player photos, team logos and generated award cards; do not store image binaries in Redis.
- **Authorization:** role- and resource-scoped permissions enforced by backend guards/services.
- **Quality:** strict TypeScript, no `any`, DTO validation, automated tests and auditable administrative changes.

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for the current architectural baseline.

## Local development

The exact install and run commands will be documented after the frontend and backend packages are added. Each application will have its own package manifest and environment configuration.

Never commit:
- `.env`, `.env.*` containing secrets, API keys, tokens or database credentials.
- MongoDB connection strings or Redis credentials.
- Cloudinary, JWT, OAuth or payment secrets.
- Private user data, production database dumps or uploaded media containing personal information.

Use each application's `.env.example` for documented variable names with placeholder values only.

## Development workflow

- Use focused branches such as `feat/team-fixtures`, `fix/rating-replay` and `chore/redis-cache`.
- Keep changes scoped and reviewable.
- Add or update tests for business rules.
- Do not commit generated build output or dependency directories.
- Use pull requests for changes that affect rating calculations, result confirmation, permissions or historical data.

## License

All rights reserved unless a license is added explicitly by the project owner.
