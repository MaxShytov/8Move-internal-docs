# Project Structure

## Directory Layout

```
django_project/
├── config/                 # Django settings
│   └── settings/
│       ├── base.py         # Common settings
│       ├── development.py  # Dev settings
│       └── production.py   # Production settings
├── apps/                   # Django applications
│   ├── accounts/           # User auth, profiles
│   ├── core/               # Shared utilities, base models
│   ├── dashboard/          # Main dashboard
│   └── employees/          # Employee management
├── templates/              # Global templates
│   ├── core/components/    # Reusable UI components
│   └── layouts/            # Base layouts
├── static/                 # Static assets (CSS, JS)
├── requirements/           # Dependencies
│   ├── base.txt
│   ├── development.txt
│   └── production.txt
└── manage.py
```

## Key Patterns

### Base Models

All models inherit from `TimeStampedModel`:

```python
from apps.core.models import TimeStampedModel

class MyModel(TimeStampedModel):
    # Automatically gets created_at and updated_at
    name = models.CharField(max_length=255)
```

### Settings

Environment is controlled via `DJANGO_SETTINGS_MODULE`:

- Development: `config.settings.development`
- Production: `config.settings.production`
