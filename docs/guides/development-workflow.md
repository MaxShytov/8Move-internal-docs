# Development Workflow

## Branch Strategy

- `main` — production-ready code
- `develop` — integration branch
- `feature/*` — new features
- `fix/*` — bug fixes

## Commit Messages

Use conventional commits format:

```
type(scope): description

feat(backend): add supplier verification endpoint
fix(docuflow): resolve receipt search crash
refactor(shared_ui): move image upload to shared package
```

### Types

| Type | Usage |
|------|-------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code restructuring |
| `docs` | Documentation |
| `chore` | Maintenance tasks |

## Testing

```bash
# Run all Django tests
python manage.py test --noinput

# Run specific app tests
python manage.py test fleet_management.tests

# Run single test class
python manage.py test fleet_management.tests.test_models.TestDepot
```

!!! important
    Always use Django's test runner (`python manage.py test`), **not** pytest.

## Code Quality

```bash
# Format code
black .

# Linting
flake8

# Django checks
python manage.py check
```
