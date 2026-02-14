# API Overview

## Base URL

- **Development:** `http://localhost:8000/api/`
- **Production:** `https://api.8move.ch/api/` *(example)*

## Authentication

API uses token-based authentication.

```
Authorization: Token <your-token>
```

## Endpoints

*This section should be expanded with actual API endpoint documentation.*

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/auth/login/` | POST | User authentication |
| `/api/auth/logout/` | POST | Logout |

## Response Format

All API responses follow a consistent JSON format:

```json
{
    "status": "success",
    "data": { ... }
}
```
