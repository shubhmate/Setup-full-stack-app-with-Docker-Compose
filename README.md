# Setup full-stack app with Docker Compose

A reference full-stack application setup using Docker Compose to orchestrate frontend, backend, database, and other services. This repository contains Dockerfiles and Docker Compose configuration to make local development, testing, and simple deployments repeatable and isolated.

> NOTE: This README is a template and intentionally generic so it fits different stacks (Node/React, Python/Flask, Ruby/Rails, etc.). Replace placeholder service names, commands, and environment variables with values from your project.

Table of contents
- [Project overview](#project-overview)
- [Tech stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Quick start](#quick-start)
- [Development workflow](#development-workflow)
- [Database & migrations](#database--migrations)
- [Running tests](#running-tests)
- [Building images & production](#building-images--production)
- [Environment variables](#environment-variables)
- [Troubleshooting](#troubleshooting)
- [Project structure](#project-structure)

## Project overview
This repository demonstrates how to run a full-stack application locally with Docker Compose. Services typically included:
- frontend (e.g., React, Vue, Angular)
- backend API (e.g., Node/Express, Python/Flask/Django, Ruby/Rails)
- database (e.g., PostgreSQL, MySQL)
- reverse proxy / local TLS (optional)
- worker/background job processor (optional)

The Docker Compose files define service relationships, networks, and volumes so you can bring the entire stack up with a single command.

## Tech stack
- Docker & Docker Compose
- Frontend: (placeholder) — replace with your framework
- Backend: (placeholder) — replace with your framework
- Database: PostgreSQL (common), or replace with your choice

Update this section to match the actual stack in the repo.

## Prerequisites
- Docker (recommended: latest stable)
- Docker Compose (v2 recommended — `docker compose ...`), or `docker-compose`
- git
- (Optional) make helper commands

Verify installation:
```bash
docker --version
docker compose version   # or docker-compose --version
```

## Quick start

1. Clone the repo
```bash
git clone https://github.com/shubhmate/Setup-full-stack-app-with-Docker-Compose.git
cd Setup-full-stack-app-with-Docker-Compose
```

2. Copy and edit environment variables
```bash
cp .env.example .env
# edit .env to add secrets/credentials
```

3. Start services
```bash
docker compose up --build
# or to run in background:
docker compose up --build -d
```

4. Check logs
```bash
docker compose logs -f
# or per service:
docker compose logs -f backend
```

5. Stop and remove containers (keep volumes)
```bash
docker compose down
```

6. To remove volumes as well:
```bash
docker compose down -v
```

If you change the Dockerfile or the build context, rebuild images:
```bash
docker compose up --build --force-recreate -d
```

## Development workflow
- Edit frontend code locally; if the frontend service mounts the source directory into the container, hot-reload should work.
- Edit backend code locally; restart backend service if hot-reload is not configured.
- Use `docker compose exec <service> /bin/sh` (or `/bin/bash`) to run commands inside a running container:
```bash
docker compose exec backend sh
# e.g., install deps, run migrations, run tests
```

Add convenient scripts or Makefile entries to simplify common tasks (start, stop, test, rebuild).

## Database & migrations
If your project uses a relational database:
- The database is configured in Docker Compose and persists to a named volume.
- Run migrations from the backend container:
```bash
docker compose exec backend sh -c "npm run migrate"       # Node example
docker compose exec backend sh -c "python manage.py migrate" # Django example
```

Ensure the backend service waits for the DB to be ready (use a wait-for script, healthchecks, or built-in retry logic).

## Running tests
Run tests in the container so they use the same environment as CI/dev:
```bash
docker compose exec backend sh -c "npm test"
# or
docker compose exec backend sh -c "pytest -q"
```

You can create a CI profile in Docker Compose (e.g., `docker-compose.ci.yml`) that spins up minimal services for tests.

## Building images & production
For production builds, create a production-oriented compose file (e.g., `docker-compose.prod.yml`) with optimized images, no bind mounts, and proper env secrets.

Build images locally:
```bash
docker compose -f docker-compose.prod.yml build
```

Deploy images to your registry (Docker Hub, GitHub Packages, etc.), and run on your host or orchestrator.

## Environment variables
Create a `.env` file at the repo root (not committed with secrets). Example `.env.example`:

```env
# .env.example
# Backend
BACKEND_PORT=8000
DATABASE_URL=postgres://user:password@db:5432/app_db

# Frontend
FRONTEND_PORT=3000

# Other
REDIS_URL=redis://redis:6379/0
```

Replace placeholders with real values and secure secrets elsewhere (CI secrets/vaults/ GitHub Actions secrets).

## Troubleshooting
- Port conflicts: make sure ports required by services are free or change them in `docker-compose.yml`.
- DB connection errors: check that DB credentials match and that the backend waits for DB readiness.
- Permission issues with volumes: ensure correct permissions on mounted directories.
- Use `docker compose ps` and `docker compose logs <service>` to inspect failing services.

## Project structure (example)
```
.
├── docker-compose.yml
├── docker-compose.prod.yml
├── backend/
│   ├── Dockerfile
│   └── ...
├── frontend/
│   ├── Dockerfile
│   └── ...
├── db/
│   └── init/
├── .env.example
└── README.md
```
Adjust to reflect the real layout of this repository.