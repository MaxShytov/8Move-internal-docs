# Getting Started — Django Backend

## Prerequisites

- **Python** 3.12+
- **Docker** & Docker Compose (for PostgreSQL and Redis)
- **Git**

## Quick Start (Automated)

The fastest way to get the backend running:

```bash
cd django_project
./run-dev.sh
```

This script will:

1. Create a Python virtual environment (if missing)
2. Start Docker containers (PostgreSQL + Redis)
3. Install Python dependencies
4. Run database migrations
5. Start the development server at `http://127.0.0.1:8000`

!!! tip "Run tests with the script"
    ```bash
    ./run-dev.sh --test
    ```
    This runs both Django and Flutter model tests, then exits.

---

## Manual Setup

If you prefer to set up step by step:

### 1. Clone the repository

```bash
git clone https://gitlab.stemsc.com/group.8move/qr-move.git
cd qr-move/django_project
```

### 2. Start infrastructure services

```bash
docker compose up -d
```

This starts:

| Service | Image | Port | Credentials |
|---------|-------|------|-------------|
| **PostgreSQL** | postgres:15.8-alpine | `localhost:15432` | DB: `develop`, User: `develop`, Password: `develop` |
| **Redis** | redis:7-alpine | `localhost:6379` | — |

### 3. Create virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### 4. Install dependencies

```bash
cd app
pip install --upgrade pip
pip install -r requirements.txt
```

### 5. Run migrations

```bash
python manage.py migrate
```

### 6. Seed initial data

```bash
python manage.py seed
```

### 7. Create superuser

```bash
python manage.py createsuperuser
```

### 8. Start the development server

```bash
python manage.py runserver 0.0.0.0:8000
```

The application will be available at `http://localhost:8000`.

---

## Available URLs

| URL | Description |
|-----|-------------|
| `http://localhost:8000/admin/` | Django Admin panel |
| `http://localhost:8000/api/schema/swagger-ui/` | Swagger API documentation |
| `http://localhost:8000/api/schema/redoc/` | ReDoc API documentation |
| `http://localhost:8000/health/` | Health check endpoint |

---

## Common Commands

### Database

```bash
# Create new migrations after model changes
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Show migration status
python manage.py showmigrations

# Django shell with auto-imports
python manage.py shell_plus
```

### Testing

```bash
# Run all tests
python manage.py test --noinput

# Run specific app tests
python manage.py test fleet_management.tests

# Run single test class
python manage.py test fleet_management.tests.test_models.TestDepot

# Run with verbosity
python manage.py test fleet_management.tests --verbosity=2
```

!!! important
    Always use Django's test runner (`python manage.py test`), **not** pytest.

### Code Quality

```bash
# Format code
black .

# Linting
flake8

# Django system checks
python manage.py check
```

### Docker

```bash
# Start services
docker compose up -d

# View logs
docker compose logs -f

# Stop services
docker compose down

# Stop and remove volumes (reset database)
docker compose down -v
```

---

## Environment Variables

The `run-dev.sh` script sets these automatically. For manual setup, export them in your shell:

```bash
export N8N_BEAUTIFY_WEBHOOK_URL="https://n8n.trident.software/webhook/beautify-description"
```

Other variables are configured in `app/main/settings.py` with sensible defaults for local development.

---

## Celery (Background Tasks)

Some features use Celery for async task processing. To run the full stack locally:

```bash
# Terminal 1: Django server
python manage.py runserver 0.0.0.0:8000

# Terminal 2: Celery worker
celery -A main worker -l info

# Terminal 3: Celery beat (scheduled tasks)
celery -A main beat -l info
```

!!! note
    Celery is optional for basic development. Most features work without it.

---

## Troubleshooting

### Docker is not running

```
✖ Cannot connect to Docker. Start Docker Desktop.
```

**Solution:** Open Docker Desktop and wait for it to start, then retry.

### Port already in use

```
Error: That port is already in use.
```

**Solution:** Kill the process using the port:
```bash
lsof -i :8000 | grep LISTEN
kill -9 <PID>
```

### Database connection error

**Solution:** Make sure Docker containers are running:
```bash
docker compose ps
docker compose up -d
```

### Migration conflicts

```bash
python manage.py makemigrations --merge
python manage.py migrate
```
