# Getting Started

## Prerequisites

- Python 3.12+
- Docker & Docker Compose
- Git

## Setup

### 1. Clone the repository

```bash
git clone git@github.com:trident-software/qr-move.git
cd qr-move/django_project
```

### 2. Start infrastructure services

```bash
docker-compose up -d
```

This starts:

- **PostgreSQL** on `localhost:15432`
- **Redis** on `localhost:16379`
- **pgAdmin** on `http://localhost:15433`

### 3. Create virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
```

### 4. Install dependencies

```bash
pip install -r requirements/development.txt
```

### 5. Run migrations

```bash
python manage.py migrate
```

### 6. Create superuser

```bash
python manage.py createsuperuser
```

### 7. Start development server

```bash
python manage.py runserver
```

The application will be available at `http://localhost:8000`.

## Common Commands

```bash
# Run tests
python manage.py test --noinput

# Create migrations
python manage.py makemigrations

# Django shell
python manage.py shell_plus

# Code formatting
black .
```
