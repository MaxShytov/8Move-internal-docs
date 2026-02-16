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

- **Django REST Framework** for API layer
- **PostgreSQL** as primary database
- **Redis** for caching
- **Docker** for containerization
- **Flutter** for cross-platform client apps

## Shared Flutter Packages

The monorepo contains 5 Flutter apps and 2 shared packages:

### shared_core

State management, API services, and business logic shared across all apps.

| Module | Description |
|--------|-------------|
| `providers/auth/` | Authentication (login, logout, token refresh) |
| `providers/user_profile/` | User profile CRUD (fetch, update, change password) |
| `models/` | Shared data models (UserModel, etc.) |
| `services/` | API services (AuthApiService, S3StorageService, DioClient) |

### shared_ui

Reusable UI components and theme system.

| Module | Description |
|--------|-------------|
| `theme/` | AppTheme, AppColors, AppTypography, tokens |
| `widgets/profile/` | SharedProfileScreen, ProfilePersonalTab, ProfilePersonalLabels |
| `widgets/buttons/` | AppButton, AppOutlinedButton |
| `widgets/maps/` | Map utilities, polygon rendering |

### Shared Profile Module Pattern

The profile module follows a provider + widget architecture:

```
shared_core (state management)
├── userProfileProvider ← StateNotifierProvider
│   ├── fetchProfile()     ← GET /api/identity/users/me/
│   ├── updateProfile()    ← PATCH /api/identity/users/me/
│   ├── changePassword()   ← POST /api/identity/users/me/change-password/
│   └── auto-fetch on login, reset on logout

shared_ui (widgets)
├── SharedProfileScreen    ← title + TabBar + desktop responsive shell
│   ├── leading: Widget?   ← optional back button
│   ├── personalTab        ← required
│   └── additionalTabs     ← app-specific (Company, Addresses, etc.)
└── ProfilePersonalTab     ← avatar, name, email, phone, password, language, logout
    ├── auto-save (1.5s debounce)
    ├── S3 avatar upload
    └── ProfilePersonalLabels (localization)
```

Each app creates a thin `ProfileScreen` that wires up the shared widgets with app-specific callbacks (locale persistence, logout navigation).
