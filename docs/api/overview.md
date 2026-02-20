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
| `/api/identity/users/me/` | GET, PUT | Get or update current user profile |
| `/api/identity/users/me/change-password/` | POST | Change current user's password |
| `/api/actors/suppliers/my-settings/` | GET | Suppliers linked to current user's client with settings |
| `/api/actors/suppliers/{id}/toggle-client-block/` | POST | Toggle client-side block on a supplier |
| `/api/actors/client-phones/` | GET, POST | List/create client phone numbers (filter by `owner`) |
| `/api/actors/client-phones/{id}/` | GET, PUT, PATCH, DELETE | Retrieve/update/delete client phone |
| `/api/actors/client-emails/` | GET, POST | List/create client email addresses (filter by `owner`) |
| `/api/actors/client-emails/{id}/` | GET, PUT, PATCH, DELETE | Retrieve/update/delete client email |
| `/api/products/products/{id}/images/` | GET, POST | List/add product images |
| `/api/products/products/{id}/images/{imageId}/` | PATCH, DELETE | Update/delete product image |
| `/api/products/products/{id}/images/{imageId}/set-primary/` | POST | Set image as primary |
| `/api/products/products/{id}/images/reorder/` | POST | Bulk reorder product images |

## Response Format

All API responses follow a consistent JSON format:

```json
{
    "status": "success",
    "data": { ... }
}
```
