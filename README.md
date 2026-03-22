# LMS API Master

Backend for an educational platform: REST API for courses and lessons, role-based permissions, subscriptions and payments (Stripe), background jobs with **Celery**, and interactive API docs via **drf-yasg** (Swagger / ReDoc).

![Python](https://img.shields.io/badge/python-3.13+-blue.svg)
![Django](https://img.shields.io/badge/django-5.2+-green.svg)
![DRF](https://img.shields.io/badge/DRF-3.16+-red.svg)
![PostgreSQL](https://img.shields.io/badge/postgresql-15-blue.svg)
![Redis](https://img.shields.io/badge/redis-broker-red.svg)
[![Tests](https://github.com/AJLbN0H/lms-api-master/actions/workflows/tests.yml/badge.svg)](https://github.com/AJLbN0H/lms-api-master/actions/workflows/tests.yml)

## Features

- **Courses & lessons** — full CRUD for educational content (`materials`).
- **Permissions** — different behaviour for regular users, content owners, and moderators.
- **Subscriptions & payments** — subscription model and Stripe integration for paid access (`users`).
- **Background tasks** — Celery workers (e.g. course update checks, inactive-user handling).
- **Auth** — JWT via `djangorestframework-simplejwt`.
- **API docs** — Swagger UI at `/swagger/`, ReDoc at `/redoc/`.

## Stack

| Layer        | Technology                          |
| ------------ | ----------------------------------- |
| API          | Django REST Framework               |
| DB           | PostgreSQL                          |
| Tasks        | Celery                              |
| Scheduler    | django-celery-beat                  |
| Broker/cache | Redis                               |
| Docs         | drf-yasg (OpenAPI / Swagger)        |

## Project layout

- `materials/` — courses, lessons, serializers, views, tasks.
- `users/` — custom user model, subscriptions, payments, auth-related endpoints.
- `config/` — Django settings, URLs, Celery app (`celery_config.py`).

## Quick start (Docker)

1. Clone the repo:

   ```bash
   git clone https://github.com/AJLbN0H/lms-api-master.git
   cd lms-api-master
   ```

2. Copy the environment template and set secrets:

   ```bash
   cp .env.sample .env
   ```

   Set at least `SECRET_KEY`, `NAME` / `USER` / `PASSWORD` / `HOST=db` for Compose, and `STRIPE_API_KEY` if you use payments.

3. Start the stack:

   ```bash
   docker compose up --build
   ```

   The `app` service runs migrations, then `runserver` on **http://localhost:8000**.  
   API entry points are under `/materials/` and `/users/` (root `/` is not routed).

4. Run tests inside Docker:

   ```bash
   docker compose exec app pytest -q
   ```

   Coverage (optional):

   ```bash
   docker compose exec app pytest --cov=users --cov=materials --cov-report=term-missing
   ```

> **Secrets:** do not commit real keys. Use `.env` (gitignored) or your host/CI secret store.

## Local run (without Docker)

You need **PostgreSQL** and **Redis** running locally. In `.env`, set `HOST` / `PORT` for the DB (e.g. `127.0.0.1` / `5432`) and point `CELERY_BROKER_URL`, `CELERY_RESULT_BACKEND`, and if needed `CACHE_LOCATION` at Redis (replace host `redis` with `127.0.0.1`).

### pip (aligned with Docker image)

```bash
python -m venv .venv
.venv\Scripts\activate   # Windows
# source .venv/bin/activate  # Linux / macOS

pip install -r requirements.txt
cp .env.sample .env   # then edit HOST, Redis URLs, etc.

python manage.py migrate
python manage.py runserver
```

### Poetry

Install runtime + test dependencies:

```bash
poetry install --no-root --with dev
cp .env.sample .env   # configure DB/Redis for your machine

poetry run pytest
```

`requirements.txt` is what the **Dockerfile** installs; keep it in sync with production/runtime needs. `pyproject.toml` / `poetry.lock` are used for development and CI.

## Celery (local)

```bash
celery -A config worker -l info
celery -A config beat -l info
```

Ensure `CELERY_BROKER_URL` and `CELERY_RESULT_BACKEND` match your Redis (see `.env.sample`).

## CI

GitHub Actions runs on pushes to `main` / `develop` and on pull requests targeting `main`: **Python 3.13**, **PostgreSQL 15**, **Redis 7**, then `poetry install --with dev` and `poetry run pytest`.

Workflow: [`.github/workflows/tests.yml`](.github/workflows/tests.yml).

## Roadmap

- Raise automated test coverage toward 90%+.
- Harden production config (WSGI/ASGI server, secrets management).
- Optional: student assessments inside the platform; richer notifications.

## Testing

Pytest is configured via `pytest.ini` (`DJANGO_SETTINGS_MODULE=config.settings`). Current line coverage for `users` + `materials` is about **83%** (run `pytest --cov=...` as above to refresh).
