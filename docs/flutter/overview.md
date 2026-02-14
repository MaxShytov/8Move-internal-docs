# Flutter Apps

## Applications

The project includes 5 Flutter applications in a monorepo (`mobile/`):

### Back Office

Desktop application for internal management.

- **Location:** `mobile/apps/back_office/`
- **Platforms:** macOS, Web
- **Purpose:** Management dashboard, data entry, reports

### DocuFlow

Document processing application.

- **Location:** `mobile/apps/docuflow/`
- **Platforms:** macOS, Web
- **Purpose:** Document scanning, receipt processing, OCR

### Driver Pro

Driver-facing mobile application.

- **Location:** `mobile/apps/driver_pro/`
- **Platforms:** Android, iOS
- **Purpose:** Delivery tracking, route navigation

### Fleet Planner

Route optimization for HoReCa dispatchers.

- **Location:** `mobile/apps/fleet_planner/`
- **Platforms:** macOS, Web
- **Purpose:** Delivery route planning, fleet tracking, AI-based optimization

### Supply Now

B2B supply management application.

- **Location:** `mobile/apps/supply_now/`
- **Platforms:** Web, Android
- **Purpose:** Supply chain, inventory management

## Shared Packages

| Package | Location | Purpose |
|---------|----------|---------|
| `shared_core` | `packages/shared_core/` | API clients (Dio + Retrofit), models (Freezed), state management (Riverpod) |
| `shared_ui` | `packages/shared_ui/` | Reusable UI components, themes, widgets |
| `workflow_editor` | `packages/workflow_editor/` | Workflow creation and editing component |

## Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| Flutter | 3.9.2+ | UI framework |
| Dart | 3.9.2+ | Language |
| Riverpod | 2.6.1 | State management |
| Dio | 5.9.0 | HTTP client |
| Retrofit | 4.9.0 | Type-safe REST API client |
| go_router | 14.6.2+ | Navigation |
| Freezed | 2.5.7 | Immutable models with code generation |
| json_serializable | 6.9.2 | JSON serialization |

## Getting Started

See the [Flutter Getting Started](getting-started.md) guide for setup instructions.
