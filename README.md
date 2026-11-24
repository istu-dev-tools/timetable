# Timetable

Self-hosted system for **automatic generation and editing of university timetables**.

The system builds a schedule that satisfies all hard constraints (no teacher / group / room double-booking, room type and capacity, availability), then keeps improving it against weighted soft criteria (gaps, building transitions, compactness, preferences, …) in the background. Administrators, teachers and an AI agent (via MCP) all edit the schedule through the same move-evaluation pipeline.

## Stack

| Layer | Technology |
|---|---|
| Solver | Go — DSatur + backtracking (feasibility), Simulated Annealing / Tabu Search (optimization), multi-start + island model + successive halving |
| API | Go, REST + WebSocket |
| Frontend | React + TypeScript |
| Storage | PostgreSQL (EXCLUDE constraints, transactional checkpoints) |
| Deployment | docker compose (local server / VPS) |

## Documentation

- [Problem research: constraints, requirements](docs/timetabling-problem-research.md) (RU)
- [Algorithms and architecture design](docs/timetabling-algorithms-architecture.md) (RU)

## Status

Early development. Planning and progress: [project board](https://github.com/orgs/istu-pro-dev/projects/1).

## Run with Docker Compose

The root `compose.yaml` downloads the published web and API images from GHCR. Clone the
repository, then start the stack:

```sh
cp deploy/.env.example .env
docker compose pull
docker compose up -d
```

The application is available at `http://localhost` by default. Set `ADMIN_PASSWORD` in `.env`
before starting to create the initial administrator account. The default compose configuration is
intended for local evaluation. For an internet-facing deployment, set
`APP_ENV=production` and configure `JWT_SECRET` with at least 32 random bytes, a strong
`POSTGRES_PASSWORD`, `ADMIN_PASSWORD`, and `COOKIE_SECURE=true`.

Images are published as `ghcr.io/istu-pro-dev/timetable-api` and
`ghcr.io/istu-pro-dev/timetable-web`; `IMAGE_TAG` can select a branch, release, or commit tag.
The existing `make up` command remains the local development flow and builds images from source.

After the first successful CI publication, an organization owner must set both GHCR packages to
**Public** in their package settings. GHCR package visibility is separate from repository visibility
and cannot be set by the repository's `GITHUB_TOKEN` publish permission. Once public, the compose
stack can pull both images anonymously.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)
