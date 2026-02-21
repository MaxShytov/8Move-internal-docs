# Changelog

All notable documentation and platform changes are documented here.

---

## 2026-02-21

### Platform (Back Office + Backend)

#### :material-new-box: Product List — Client Prices, Frozen Columns & Advanced Filters

**Backend:**

- `get_price()` now returns individual price detection with `is_individual` flag and `regular_price` for strikethrough display
- New product filters: `has_image`, `has_base_price`, `base_unit_null`, `price_category`, `price_category_null`

**Flutter:**

- Products list: frozen Name column stays pinned when scrolling horizontally through price columns
- Client price column: select a client to see their specific prices, with individual price highlighted and regular price shown as strikethrough
- Client price dialog: inline editing of individual prices per client
- Advanced filter bottom sheet: filter by image, base price, base unit, and price category
- "No category" filter in category tree sidebar with count badge

#### :material-wrench: Orders List — Redesigned Compact Layout

- Redesigned columns: Status → Client (name + order # + ID) → Delivery Address (with copy + Google Maps link) → Dispatch Slot → Date → Total (with weight)
- Zone color badges on delivery addresses
- Auto-filter by current supplier when selected in header
- Removed debug print statements

#### :material-wrench: Google Maps — Context-Aware Error Messages

Replaced generic "Configuration Google Maps manquante" with specific messages:

- **No supplier link:** "Compte non associé à un fournisseur" — instructs user to contact admin
- **No API keys:** "Clés API non configurées" — instructs user to ask company admin
- **Fetch error:** "Erreur de chargement" — suggests checking internet connection

### Platform (Django Admin)

#### :material-shield-lock: Delivery Zone — Interactive Map Preview

Added an interactive Leaflet.js map directly in the DeliveryZone admin change form. The map renders zone polygons with the zone's display color on OpenStreetMap tiles, supports zoom/pan, and shows feature info on click. The fieldset is now open by default for quick visual verification. Copy GeoJSON and Open geojson.io buttons are still available.

---

## 2026-02-20

### Platform (Back Office + Backend)

#### :material-new-box: Multiple Product Images

Full-stack feature: products now support multiple images with a primary designation.

**Backend:**

- New `ProductImage` model with FK to Product, `is_primary` flag, `sort_order`, `alt_text`
- Partial unique constraint: at most one primary image per product
- Schema migration + data migration (existing single `Product.images` → `ProductImage` records)
- `ProductImageViewSet` with `set-primary` and `reorder` actions
- Backward-compatible: `images` field in API still returns primary image URL (string), new `product_images` field returns full array

**Flutter:**

- `ProductImagesSection` widget: responsive grid of thumbnails with primary badge, set-primary, delete
- S3 upload with progress indicator
- Edit mode: dedicated images section; Create mode: simple single uploader
- Localized in EN, DE, FR, IT

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/products/products/{id}/images/` | List all images for a product |
| POST | `/api/products/products/{id}/images/` | Add image to product |
| PATCH | `/api/products/products/{id}/images/{imageId}/` | Update image metadata |
| DELETE | `/api/products/products/{id}/images/{imageId}/` | Delete image |
| POST | `/api/products/products/{id}/images/{imageId}/set-primary/` | Set image as primary |
| POST | `/api/products/products/{id}/images/reorder/` | Bulk reorder images |

#### :material-wrench: Products List Rework & M3 Polish

- Products list: full rework with improved layout, filtering, and stats
- Material Design 3 polish: consistent `titleMedium/w600` card headers across all product sections
- Theme tokens in `ImageUploadField` (no more hardcoded colors)
- Individual prices: client-side pagination (10 per page)
- Category price dialog: unit-aware price display
- Product unit dialog: ordering constraints (min_order_quantity, is_orderable)

#### :material-wrench: Client Form — Industry Multi-Select Dialog

Replaced inline FilterChip industry selector with a dropdown-style multi-select dialog:

- `InputDecorator` with dropdown arrow shows selected industries as compact chips
- Tapping opens `_IndustryMultiSelectDialog` with checkbox list for all industries
- Confirm/cancel actions, M3-styled header — consistent with other dialogs in the app

#### :material-new-box: Product List — Inline Price Editing

Edit icons next to base price and category prices in the products list allow editing prices without navigating to the product form:

- `_PriceEditIcon` widget with `GestureDetector` (opaque hit test to prevent row tap propagation)
- `BasePriceDialog` — new dialog for creating/editing base price with price input + unit dropdown
- `CategoryPriceDialog` reused for category price editing with pre-filled data from provider
- Icons shown even when no price is set (dash `—`), enabling quick price creation

#### :material-wrench: Product Form — Default Units & Refresh

- New individual prices and barcodes now default to the product's base unit instead of requiring manual selection
- Refresh button in product form header to reload all sections (units, prices, images, barcodes, QR codes)
- `main_category_null` filter added to backend and Flutter for filtering products without a main category

---

## 2026-02-18

### Platform (Django Admin)

#### :material-shield-lock: Supplier Force Delete Action

Added "Force Delete" admin action for Suppliers in Django Admin that bypasses all PROTECT constraints:

- Temporarily patches Django's PROTECT → CASCADE on all FK fields using `_collect_for_force_delete()` helper
- Collects the full dependency tree via Django's `Collector`, then deletes via raw SQL in dependency order
- Wrapped in `transaction.atomic()` — full rollback on any error
- Shows deletion summary with counts per model type
- 13 unit tests covering: empty queryset, no-dependencies, products, orders, invoices, depots, full chain, bulk delete, isolation, rollback, PROTECT restoration

#### :material-wrench: Product Admin — Unit FK Filtering

Added `ProductInlineMixin` to filter unit dropdowns in Barcode, QRCode, and ProductPrice inlines to only show units belonging to the current product. Moved logic from `ProductInlineFormSet` to admin-level `formfield_for_foreignkey`.

### Platform (Back Office)

#### :material-new-box: Supplier Settings Screen Redesign

Complete redesign of Supplier Settings page using Material Design 3 patterns:

- ListTile-based layout with section groups replacing old card-based form
- SwitchListTile for toggles, bottom sheet pickers for selections
- ExpansionTile for advanced settings (Invoice Numbers, Weight Limits, API Keys)
- Modern Android Settings-style look

#### :material-new-box: Multiple Phones & Emails for Clients and Suppliers

Full CRUD for multiple phone numbers and email addresses on both Client and Supplier entities:

- **Backend**: `ClientPhone`/`ClientEmail` serializers + viewsets with auto-primary logic and RLA access control
- **Backend**: `phones_data`/`emails_data` fields on client detail response
- **Flutter**: `ClientPhonesSection`/`ClientEmailsSection` with M3 list items in client edit form
- **Flutter**: `SupplierPhonesSection`/`SupplierEmailsSection` in Supplier Settings
- **Flutter**: Add/Edit dialogs with phone type picker, label, primary toggle
- **Flutter**: Swiss phone formatter for display

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET/POST | `/api/actors/client-phones/?owner={id}` | List/create client phone numbers |
| PATCH/DELETE | `/api/actors/client-phones/{id}/` | Update/delete client phone |
| GET/POST | `/api/actors/client-emails/?owner={id}` | List/create client emails |
| PATCH/DELETE | `/api/actors/client-emails/{id}/` | Update/delete client email |

#### :material-wrench: Clients List — Copy Buttons & Column Reorder

- Copy-to-clipboard buttons next to Company Name, #ID, and Bexio ID
- Invoicing column moved before User column for better information hierarchy
- Addresses section widget updates with copy-to-clipboard enhancements
- New localization strings (EN, DE, FR, IT)

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

#### :material-wrench: Clients List — Copy Address & Bexio ID

- **Copy address button**: added copy-to-clipboard icon next to map pin for delivery addresses in the clients list table
- **Copy address in detail**: added copy button in the `AddressesSection` widget (client form address list) with localized tooltip (EN/DE/FR/IT)
- **Bexio ID display**: shows `Bexio: 1234` next to `#ID` in the client name cell when an external identifier exists
- **Backend**: added `bexio_id` field to `ClientListSerializer` via `GenericRelation` on Client → `ExternalIdentifier`, with prefetch for N+1 optimization
- **Sync section fix**: fixed client form sync parameters parsing to read from nested `external_identifiers[]` / `sync_logs[]` response structure

#### :material-new-box: Multiple Phones & Emails — Full CRUD

Complete multi-phone/email management for clients (backend + Flutter):

- **Backend**: `ClientPhoneSerializer` / `ClientEmailSerializer` with auto-primary logic (first phone/email auto-becomes primary; setting new primary demotes old one)
- **Backend**: `ClientPhoneViewSet` / `ClientEmailViewSet` with RLA access control, `perform_destroy` auto-promotes next item to primary on deletion
- **Backend**: Added `phones_data` / `emails_data` to both `ClientListSerializer` and `ClientUpdateSerializer`
- **Backend**: Added `rla_roles_filter` to `ClientPhone` / `ClientEmail` models
- **Flutter**: `ClientPhonesSection` / `ClientEmailsSection` — M3-style list items with type icon, formatted phone, type badge, primary star, edit/delete actions
- **Flutter**: `ClientPhoneDialog` / `ClientEmailDialog` — add/edit dialogs with SegmentedButton for phone type, label, primary toggle
- **Flutter**: Phone formatting — Swiss-aware: `+41 (0) 79 745-33-22`, `+41 79 745-33-22`, `079 745-33-22`
- **Flutter**: Phones and emails sections displayed side-by-side in client edit form
- **Flutter**: In create mode, single email+phone fields auto-create initial records after save

#### :material-new-box: Contact Popup in Clients List

- **"+N more" chip**: M3-style chip in Contact Information column shows count of additional phones/emails beyond primary
- **Contact popup dialog**: clicking the chip opens a dialog listing all emails and phones with M3-style items, primary star, type badges, and quick copy/call/email actions
- **Phone formatting in list**: primary phone now displays formatted in the clients list table

#### :material-wrench: Clients List — Delivery Address Layout

- **Zone left, coords right**: delivery zone badge stays left-aligned, lat/lon coordinates + map icon moved to right-aligned position
- **Map icon next to coords**: map icon placed inline after coordinates instead of on the address line

#### :material-wrench: Address Form Header

- **Client name + IDs**: address form title now shows client name, client ID, and address ID (e.g., "Edit Address — Acme Corp #123, Address #456")
- **Async client name loading**: fetches client name on init for display in header

#### :material-wrench: Client Form Section Reorder

- **Business Information above Contact Information**: moved fiscal/VAT fields section above the contact details section for better logical flow

#### :material-wrench: Address Delivery Zone Fix

- **Supplier context for zones**: `clientAddressesProvider` now passes `supplierId` to API, so delivery zone badges display correctly in the client form address section

#### :material-folder-move: Rename Bexio Import → Data Import

- **Backend**: `BexioImportLog` → `DataImportLog` model with migration, added `ROLLED_BACK` status
- **Backend**: Added `DataImportRollbackService` for rolling back imports
- **Flutter**: Renamed screens/providers from `bexio_import_*` → `data_import_*`
- **Flutter**: Updated router path from `/clients/import-history` to `/clients/data-import-logs`

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/actors/client-phones/?owner={id}` | List phones for a client |
| POST | `/api/actors/client-phones/` | Create client phone |
| PUT/PATCH | `/api/actors/client-phones/{id}/` | Update client phone |
| DELETE | `/api/actors/client-phones/{id}/` | Delete client phone (auto-promotes primary) |
| GET | `/api/actors/client-emails/?owner={id}` | List emails for a client |
| POST | `/api/actors/client-emails/` | Create client email |
| PUT/PATCH | `/api/actors/client-emails/{id}/` | Update client email |
| DELETE | `/api/actors/client-emails/{id}/` | Delete client email (auto-promotes primary) |

#### :material-shield-lock: Test Coverage Expansion

Comprehensive test suite covering new and existing functionality:

- **Backend (110 tests)**: ClientPhone/Email CRUD + auto-primary logic, RLA supplier isolation, list serializer phones_data/emails_data, DataImportRollback service, BexioCsvClassifier (all Col6 classification types), BexioCsvImport (country normalization, field extraction, CSV parsing, client creation with phones/emails/addresses, dry run, duplicate email detection, import log)
- **Flutter shared_core (216 tests)**: ClientModel, ClientAddressModel (with formattedAddress/isPrimary/isBilling/isRegistered/hasOrders extensions), ClientUserLinkModel, ClientPhoneModel, ClientEmailModel, OrderItemModel (with totalWeight calculation), DeliverySlotModel (with status/reason enums), phone formatter (Swiss formats), color utils (hex parsing), formatters (currency/weight/distance/duration/time/date)

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
