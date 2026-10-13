# test-manager-library-3145556-infra

Infrastructure for the Manager Library project (task T-20). One command starts:

- `db`: an empty PostgreSQL 16 engine (user, database and named volume only).
- `api`: the Laravel API (port 8000).
- `app`: the React frontend (port 5173).

This repository contains no `CREATE TABLE` and no SQL init scripts. The schema is owned by the API migrations
(constitution Art. V, ADR-001), so the database has no tables until the migrations are run.

Spec: https://github.com/mauro-1508/test-manager-library-3145556-docs/tree/develop/spec-laravel

## Prerequisites

- Docker with Docker Compose v2.
- The sibling repositories cloned side by side with this one:

```
<parent folder>/
  test-manager-library-3145556-infra/   (this repository)
  test-manager-library-3145556-api/     (Laravel, has a Dockerfile, serves on port 8000)
  test-manager-library-3145556-app/     (React + Vite, provides its own Dockerfile, port 5173)
```

## Start from scratch

```
cp .env.example .env
# edit .env and replace every placeholder
docker compose up -d --build
```

## Verify the database starts empty

```
docker compose exec db psql -U <POSTGRES_USER> -d <POSTGRES_DB> -c "\dt library.*"
```

Expected: no relations found.

## Run the migrations

```
docker compose exec api php artisan migrate --force
```

## Stop and reset

```
docker compose down       # stop, keep data
docker compose down -v    # stop and delete the database and covers volumes
```

## Environment variables

See `.env.example`. `DATABASE_URL` is composed by `docker-compose.yml` from the `POSTGRES_*` variables.
The frontend receives `VITE_API_URL=http://localhost:8000/api/v1`.
