# Working Notes

## 2026-02-21

- [x] Backend: Interactive Leaflet.js map preview in DeliveryZone admin (full-width, dynamic CDN load, zone color, popups)

## 2026-02-20

- [x] Back Office: Replace industry FilterChip selector with multi-select dropdown dialog in client form
- [x] Backend: ProductImage model with is_primary, sort_order, alt_text + partial unique constraint
- [x] Backend: Schema migration (0006) + data migration (0007) for existing product images
- [x] Backend: ProductImageSerializer with S3ImageField, backward-compatible images field in ProductSerializer
- [x] Backend: ProductImageViewSet with set-primary and reorder actions, nested router
- [x] Backend: Order serializers updated for backward compat with new product images
- [x] Backend: ProductImageInline in admin with image preview
- [x] Flutter: ProductImageModel (freezed) + Product model updated with productImages field
- [x] Flutter: Products API service — 6 new image endpoints
- [x] Flutter: ProductImagesSection widget with grid view, upload, set primary, delete
- [x] Flutter: Product form integration — images section for edit, simple uploader for create
- [x] Flutter: Product images localization strings in EN/DE/FR/IT
- [x] Back Office: Products list full rework with improved layout, filtering, stats
- [x] Back Office: M3 polish — consistent card headers, theme tokens in ImageUploadField
- [x] Back Office: Individual prices pagination (10 per page)
- [x] Back Office: Category price dialog unit-aware display, product unit dialog ordering constraints
- [x] Backend: Seed data updates for pricing, products, secondary units
- [x] Back Office: Inline price editing in products list — edit icons next to base price and category prices
- [x] Back Office: BasePriceDialog widget for creating/editing base price from product list
- [x] Back Office: CategoryPriceDialog reuse for editing category prices from list
- [x] Back Office: Default base unit for new individual prices and barcodes
- [x] Back Office: Refresh button in product form header to reload all sections
- [x] Backend: main_category_null filter for products without main category
- [x] Flutter tests: BasePriceDialog (9 tests), PriceEditIcon (6 tests), ProductsFilter (5 tests)
- [x] Flutter tests: Test helpers — MockPricingApiService, MockProductsApiService, MockSuppliersApiService, TestData factory

## 2026-02-18

- [x] Django Admin: Force Delete action for Suppliers — bypasses PROTECT constraints via Collector patching + raw SQL deletion
- [x] Django Admin: 13 unit tests for force_delete_suppliers (products, orders, invoices, depots, full chain, rollback, isolation, PROTECT restoration)
- [x] Django Admin: ProductInlineMixin — filter unit FK in Barcode/QRCode/ProductPrice inlines to current product only
- [x] Back Office: Supplier Settings screen redesign with Material Design 3 (ListTile, SwitchListTile, ExpansionTile)
- [x] Back Office: Clients list screen improvements
- [x] Back Office: Addresses section copy-to-clipboard enhancements + localization (EN/DE/FR/IT)
- [x] Backend: ClientPhone/ClientEmail serializers + viewsets with auto-primary logic
- [x] Backend: phones_data/emails_data on client detail response
- [x] Flutter: SupplierPhonesSection/SupplierEmailsSection in Supplier Settings with CRUD dialogs
- [x] Flutter: Supplier phone/email models, API services, Riverpod providers
- [x] Flutter: Clients list — copy buttons next to Name, #ID, Bexio ID; Invoicing↔User column swap

## 2026-02-17

- [x] Client form UX: SegmentedButton for type, merged Industry+Description into Basic Info, group dividers, red asterisks for required, "Optional" helper text, ID in header subtitle, secondary headers for settings sections
- [x] Client form: moved logo to inline 128px avatar with camera picker (reuses Profile avatar pattern) next to Company Name / Official Name fields
- [x] Bexio CSV Import: backend service (BexioCsvImportService) with Bexio ID matching, name+postal manual review, duplicate email detection
- [x] Bexio CSV Import: BexioImportLog model + ReadOnly ViewSet + list/detail serializers
- [x] Bexio CSV Import: Phone/Email models, salutation, industries M2M, data migration
- [x] Bexio CSV Import: Flutter import dialog (file picker, preview, dry run, progress, results)
- [x] Bexio CSV Import: Flutter import history screen with expandable cards + detail drill-down
- [x] Bexio CSV Import: Email Report button (mailto: with formatted import results)
- [x] Bexio CSV Import: management command `import_bexio_csv` for CLI
- [x] Clients list: copy address button next to map pin, Bexio ID display next to client ID
- [x] AddressesSection widget: copy address button with localized tooltip (EN/DE/FR/IT)
- [x] Client form: fixed sync section parsing (external_identifiers/sync_logs nested response)
- [x] Backend: added bexio_id to ClientListSerializer via GenericRelation + prefetch
- [x] Backend: RLA access control on ClientPhone/ClientEmail models (rla_roles_filter)
- [x] Backend: ClientPhoneSerializer/ClientEmailSerializer with auto-primary logic
- [x] Backend: ClientPhoneViewSet/ClientEmailViewSet with perform_destroy auto-primary promotion
- [x] Backend: phones_data/emails_data added to ClientListSerializer for list view
- [x] Backend: BexioImportLog → DataImportLog rename + ROLLED_BACK status + rollback service
- [x] Flutter: ClientPhonesSection/ClientEmailsSection — M3 list items with CRUD
- [x] Flutter: ClientPhoneDialog/ClientEmailDialog — add/edit with type picker, label, primary toggle
- [x] Flutter: Swiss phone formatter (+41 (0) 79 745-33-22 format)
- [x] Flutter: "+N more" contacts chip in clients list with popup showing all phones/emails
- [x] Flutter: Phones/emails sections side-by-side in client edit form
- [x] Flutter: Delivery address column — zone left, lat/lon + map icon right-aligned
- [x] Flutter: Address form header with client name + IDs
- [x] Flutter: Business Information section moved above Contact Information
- [x] Flutter: clientAddressesProvider now passes supplierId for delivery zone display
- [x] Flutter: Bexio import → Data import rename (screens, providers, router)
- [x] Backend tests: ClientPhone/Email CRUD + auto-primary (8+7 tests), RLA isolation (4 tests), serializer phones_data/emails_data (4 tests), DataImportRollback (6 tests)
- [x] Backend tests: BexioCsvClassifier — all 8 Col6 classification types (19 tests)
- [x] Backend tests: BexioCsvImport — normalize_country, get_field, CSV parsing, client creation pipeline (16 tests)
- [x] Flutter tests: ClientModel, ClientAddressModel (with extensions), ClientUserLinkModel, OrderItemModel, DeliverySlotModel (5 model test files)
- [x] Flutter tests: phone_formatter (Swiss formats), color_utils (hex parsing), formatters (currency/weight/distance/duration)
- [x] Test coverage analysis saved to CURRENT_TASK_Test_Coverage.md

## 2026-02-16

- [x] Created shared `userProfileProvider` in shared_core (state, notifier, provider with auth listener)
- [x] Created `ProfilePersonalTab` in shared_ui (avatar, name auto-save, change password, language, logout)
- [x] Created `SharedProfileScreen` shell in shared_ui (title, tabs, desktop responsive, leading widget)
- [x] Created `ProfilePersonalLabels` model for localization
- [x] Integrated profile screen in Back Office (new screen + /profile route)
- [x] Integrated profile screen in Fleet Planner (new screen + /profile route)
- [x] Integrated profile screen in Driver Pro (new screen + /profile route + bottom nav)
- [x] Replaced DocuFlow profile with shared version, removed custom DioService and UserProvider
- [x] Refactored Supply Now profile to use SharedProfileScreen with 4 tabs
- [x] Back Office: removed language switcher, email, popup menu from dashboard header; avatar now navigates directly to /profile
- [x] Fleet Planner: removed language switcher from dashboard header; fixed avatar to show user image
- [x] Added back arrow (leading widget) to Back Office and Fleet Planner profile screens
- [ ] Full localization verification across all apps (arb files)
- [ ] Manual end-to-end testing (avatar upload, change password, language switch, logout)
