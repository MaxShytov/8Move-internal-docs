# Architecture Overview

## System Components

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Flutter Apps    │────▶│  Django Backend  │────▶│   PostgreSQL    │
│  (Back Office,   │     │  (REST API)      │     │                 │
│   DocuFlow,      │     └────────┬────────┘     └─────────────────┘
│   Mobile)        │              │
└─────────────────┘              │
                           ┌─────┴─────┐
                           │   Redis    │
                           │  (Cache)   │
                           └───────────┘
```

## Key Design Decisions

*This section should be expanded with actual architectural decisions as the project evolves.*

- **Django REST Framework** for API layer
- **PostgreSQL** as primary database
- **Redis** for caching
- **Docker** for containerization
- **Flutter** for cross-platform client apps
