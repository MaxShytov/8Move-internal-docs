# Changelog

All notable documentation and platform changes are documented here.

---

## 2026-02-17

### Platform (Back Office + Backend)

#### :material-new-box: Bexio CSV Import with Persistent Logs

Full Bexio CSV client import pipeline — backend service + Flutter UI:

- **Backend**: `BexioCsvImportService` with Bexio ID matching, name+postal manual review, duplicate email detection
- **Backend**: `BexioImportLog` model with JSONField for per-row details (action, client_id, errors, manual review reasons)
- **Backend**: Preview + Import API endpoints, ReadOnly ViewSet for import logs, management command for CLI
- **Backend**: Phone/Email models, salutation field, industries M2M on Actor
- **Flutter**: Import dialog with file picker, CSV preview (row count), dry run / skip geocoding options, progress bar
- **Flutter**: Import results dialog with stats, errors, manual review, duplicate emails sections
- **Flutter**: "Email Report" button — formats import results as text and opens default mail client via `mailto:`
- **Flutter**: Import History screen (`/clients/import-history`) with expandable cards, lazy-loaded detail drill-down
- **Flutter**: "View Import History" button on clients list + link from results dialog

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/actors/clients/preview-bexio-csv/` | Preview CSV file (row count, headers) |
| POST | `/api/actors/clients/import-bexio-csv/` | Import clients from Bexio CSV |
| GET | `/api/actors/bexio-import-logs/` | List import logs (summary, no JSON) |
| GET | `/api/actors/bexio-import-logs/{id}/` | Import log detail (full JSON fields) |

#### :material-wrench: Client Form — UX Improvements (Proximity & Emphasis)

Redesigned client edit/create form using Proximity and Emphasis design principles:

- **SegmentedButton** for client type (Person/Company) — replaces RadioListTile for stronger visual emphasis
- **Merged sections**: Industry and Description moved into Basic Information (reduces card count, Proximity)
- **Group dividers**: form sections organized into logical groups (Addresses, Settings, Users, Sync)
- **Required field emphasis**: red asterisk via RichText label instead of plain text
- **Optional field de-emphasis**: helper text "Optional" on non-required fields
- **Client ID in header**: merged into subtitle ("Acme Corp · #123") instead of separate bar
- **Settings hierarchy**: Notifications, Invoicing, Sync sections use smaller `titleMedium` headers
- **Inline logo avatar**: logo moved from separate section to 128px circular avatar next to Company Name / Official Name, with camera picker overlay (same UX as Profile avatar). Bottom sheet with Gallery / Camera / Remove options.

---

## 2026-02-16

### Platform (Supply Now + Backend)

#### :material-new-box: Supply Now — Change Password

Users can now change their password directly from the Profile screen:

- **Change Password dialog**: current password, new password, confirm password
- **Client-side validation**: min 8 chars, must contain letters and numbers, passwords must match
- **Server-side validation**: checks current password correctness
- **Localized**: all labels translated to EN, DE, FR, IT

#### :material-wrench: Supply Now — Auto-Save Profile

Profile editing now uses auto-save instead of a manual Save button:

- **Name fields**: debounced auto-save (1.5s after typing stops)
- **Avatar**: instant save on upload or clear
- **Improved snackbars**: floating style with check/error icons
- **Supplier tab**: shows supplier count in tab label

#### :material-shield-lock: RLA Filters for Multi-Supplier

Fixed Row-Level Access (RLA) filters to ensure proper data isolation:

- **ClientSupplierLink**: added `rla_roles_filter()` — supplier users see their links, client users see their links
- **Order**: fixed `rla_roles_filter()` to include client user filtering (clients can now see their orders)
- **13 automated tests**: covering RLA isolation for suppliers, clients, cross-isolation, and edge cases

#### :material-database: New Seed Data

- **Swiss Timepieces Group SA**: 2 seed users (Philippe Aubert — admin, Caroline Roux — manager)
- **Standalone Boutique**: client with 0 suppliers + 1 admin user (Thomas Weber) — edge case testing

#### :material-cellphone: Supply Now — Client Company Profile

New "Company" tab on Profile screen showing real company data from the API:

- Company name, official name, VAT number, fiscal number
- Website, email, description
- Admin vs Manager role display

#### :material-cellphone: Supply Now — Company Logo Editing

Admin users can now upload or change their company logo directly from the Profile screen:

- **Camera/Gallery picker**: choose image source via bottom sheet
- **S3 upload**: logo uploaded via presigned URL, then saved to company
- **Instant preview**: picked image shown immediately while upload is in progress
- **All roles see logo**: company and supplier logos display for all users

#### :material-wrench: Supply Now — Swiss-Style Language Selectors

Replaced country flag emojis with Swiss convention 2-letter abbreviations (EN, DE, FR, IT):

- **Company document language**: picker with abbreviation badges instead of flags
- **Personal app language**: same Swiss-style abbreviation display
- Applies to all language selectors throughout the profile

#### :material-shield-lock: Supply Now — Company Blocking Management

Admin users can block/unblock their own company (self-block):

- **Confirmation dialogs**: localized block/unblock confirmation with consequences explained
- **Block toggle**: admin-only, with status badge display
- **Full l10n**: all blocking labels/messages translated to EN, DE, FR, IT

#### :material-wrench: Backend — Absolute Logo URLs

Fixed `my-settings` endpoints to return absolute URLs for logos:

- Client `my-settings`: `request.build_absolute_uri()` for client logo
- Supplier `my-settings`: same fix for supplier logo
- Fixes `CachedNetworkImage` loading on mobile (requires absolute URLs)

#### :material-cellphone: Supply Now — Client Addresses Management

New "Addresses" tab on Profile screen for managing company delivery addresses:

- **Multi-company support**: shows addresses from all linked companies, each card displays company name
- **Address CRUD**: create, edit, delete addresses (admin only)
- **Google Places Autocomplete**: search addresses with auto-fill of street, city, postal code, country, coordinates
- **Purpose assignment**: set address as Primary, Billing, or Registered via picker
- **Purpose display**: color-coded badges (green = Primary, blue = Billing, purple = Registered)
- **Company tab integration**: each company card shows all 3 purpose slots with assigned addresses, tappable to change
- **Order protection**: addresses linked to orders are locked (cannot be edited or deleted)
- **Localized**: all labels translated to EN, DE, FR, IT

#### :material-server: Backend — Address Purpose & Order Protection

- **`set-purpose` fix**: changing purpose now clears the address from any other purpose field first (prevents duplicate assignments)
- **`get_purpose` fix**: replaced dict-based lookup with if/elif chain to avoid key collision when one address had multiple purposes
- **`order_count` annotation**: API returns order count per address for edit/delete protection
- **`perform_update` / `perform_destroy`**: addresses with orders return 409 Conflict instead of allowing modification

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/identity/users/me/change-password/` | Change current user's password (requires current password, validates strength) |

#### :material-cellphone: Supply Now — Company Card Redesign

Restructured the Company tab card with Material Design section dividers and improved layout:

- **VAT moved up**: VAT number now appears directly under company name
- **Section dividers**: Addresses, Contact Info, and Settings sections with centered `─── TITLE ───` separators
- **Tabulated address rows**: fixed-width labels for aligned address display (Primary, Billing, Registered, Default Delivery)
- **Default delivery address**: shown first with orange shipping icon, italic hint "Can be changed during checkout", admin can pick from address list
- **Contact Info section**: website and email with edit icon for admin users
- **Contact info editor**: bottom sheet with website + email fields, admin-only
- **Address comments**: shown in Addresses tab cards with italic grey text
- **Image source pickers**: all converted from AlertDialog to bottom sheet (avatar + company logo)
- **Purpose picker title**: bottom sheet now shows "Set Purpose" title
- **Address count in tab**: Addresses tab label shows count when > 0
- **Flexible labels**: replaced fixed-width with ConstrainedBox for better text accommodation
- **InkWell touch feedback**: replaced GestureDetector with InkWell for Material Design compliance

#### :material-server: Backend — Website in Client API

- **`my-settings`**: added `website` field to client company response (was already on Actor model, just not exposed)

#### :material-wrench: DocuFlow — BUG-001: Receipt Save 500 Error Fixed

Fixed HTTP 500 error when saving receipts in DocuFlow ([BUG-001](tasks/bugs/BUG-001.md)):

- **Backend**: added `read_only_fields` to `ReceiptUpdateSerializer` — `user`, `exchange_rate`, `amount_base`, `tax_base` are now server-computed only
- **Flutter**: removed computed fields from PATCH request — prevents type mismatch in `compute_base_amounts()`

#### :material-new-box: DocuFlow — Receipt List Sort by ID

Added ID-based sorting to receipt list (default: newest first):

- **Backend**: added `id` to `ordering_fields`, default ordering changed to `-id`
- **Flutter**: added "ID (newest)" / "ID (oldest)" sort options with translations in all 5 languages
- **Default sort**: `-id` (newest receipts appear first)

### Platform (All Apps — Shared Profile Module)

#### :material-new-box: Shared Profile Module — Personal Tab for All 5 Apps

Extracted a shared Profile/Personal module into `shared_core` + `shared_ui` packages and integrated it across all Flutter apps:

- **shared_core**: new `userProfileProvider` — fetch, update profile, change password, auto-fetch on login/reset on logout
- **shared_ui**: new `ProfilePersonalTab` — avatar (S3 upload), name editing (1.5s debounce auto-save), email/phone (read-only), change password dialog, language selector, logout
- **shared_ui**: new `SharedProfileScreen` — title + TabBar shell, desktop responsive (max-width 600dp on ≥840dp), optional leading widget (back arrow), supports app-specific tabs
- **Back Office**: new profile screen accessible via avatar click on dashboard header; removed language switcher, email, and popup menu from header
- **Fleet Planner**: new profile screen; removed language switcher from header, fixed avatar to show user image instead of letter initial
- **Driver Pro**: new profile screen accessible from bottom navigation
- **DocuFlow**: replaced custom `DioService` and `UserProvider` with shared_core providers; simplified profile screen
- **Supply Now**: refactored profile to use `SharedProfileScreen` with 4 tabs (Personal, Companies, Addresses, Suppliers)

#### :material-folder-move: DocuFlow — Migrated to Shared Core Providers

Replaced DocuFlow's custom networking and user management with shared packages:

- **Removed**: `DioService` (230 lines) — replaced by `shared_core` `dioClientProvider`
- **Removed**: `UserProvider` (83 lines) — replaced by `shared_core` `userProfileProvider`
- **Added**: `core/providers.dart` — thin wrappers for `s3StorageServiceProvider`
- **Updated**: all services now use `dioClientProvider` from shared_core

---

## 2026-02-15

### Documentation

#### :material-new-box: Users & Roles Section
Added comprehensive descriptions for all 6 user roles on the platform:

| Document | Description |
|----------|-------------|
| [Platform Admin](users/platform-admin.md) | Django Admin — full system management, onboarding, configuration |
| [Supplier — Back Office](users/supplier-back-office.md) | Product catalog, orders, invoicing, delivery, fleet |
| [Client — Supply Now](users/client-supply-now.md) | Catalog browsing, ordering, delivery tracking, profile |
| [Driver — Driver Pro](users/driver-driver-pro.md) | Route execution, GPS tracking, signatures, statistics |
| [Delivery Manager — Fleet Manager](users/delivery-manager-fleet-manager.md) | Route planning, fleet management, real-time tracking |
| [Employee — DocuFlow](users/employee-docuflow.md) | Receipt scanning, OCR, expense management |

Each document includes: screen/feature tables, Mermaid diagrams, role differences, and data flows.

#### :material-folder-move: Tasks Reorganized by App
Tasks are now grouped by application instead of flat list:

- **Back Office**: BO-01 Supplier Client Management
- **Supply Now**: SN-01 Multi-Supplier Verification, SN-02 Client Company Profile

#### :material-file-document-plus: BO-01 Task Added
New task [BO-01](tasks/current/back-office/BO-01.md) — Supplier Client Management (Block Clients & Edit Settings) for Back Office app.

### Platform (Supply Now + Backend)

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/actors/suppliers/my-settings/` | Returns suppliers linked to current user's client with both supplier-controlled and client-controlled settings |
| POST | `/api/actors/suppliers/{id}/toggle-client-block/` | Toggles `is_blocked` on `SupplierSettingsClientControlled` (admin only) |

#### :material-cellphone: Supply Now — Real Supplier Data on Profile

Replaced hardcoded mock data ("Fresh Foods AG", "Metro Cash & Carry", "Old Supplier Ltd") on Profile > Suppliers tab with real data from API:

- **Supplier card**: name, logo, fulfillment scheme, invoicing period, blocking status
- **Admin toggle**: block/unblock supplier with confirmation dialog
- **Refresh**: pull-to-refresh + refresh button
- **Blocking priority**: "Blocked by Supplier" (red) overrides client toggle

#### :material-cellphone: Supply Now — Blocking in Supplier Selector

Both the Home tab selector and Categories screen selector now show blocking indicators:

- **Selector dropdown**: blocked suppliers shown with red/orange badges, reduced opacity
- **Compact selector button**: red border + block icon when selected supplier is blocked
- **Two-way blocking**: "Supplier blocked you" vs "You blocked supplier" with different messages

#### :material-shield-lock: Django Admin Improvements

- Added supplier/client filters to `ClientSettingsSupplierControlled` admin
- Added supplier/client filters to `SupplierSettingsClientControlled` admin

---

## 2026-02-14

### Documentation

- Initial task docs: SN-01 (Multi-Supplier Verification), SN-02 (Client Company Profile)
- Full detail pages for both tasks
- Task templates for AI Chatbot, Developers, Admins, End Users

---

## 2026-02-13

### Documentation

- Initial MkDocs Material documentation site
- Architecture overview, tech stack
- Backend & Flutter getting started guides
- API overview
- Development workflow guide
