# Working Notes


1. Supply Now — Страница товара (Product Screen)
Файлы: product_screen.dart, product_action_button.dart, product_provider.dart, providers.dart, product_model.dart

Галерея изображений — если у товара несколько изображений, показывается ProductImageGallery (свайп) вместо одиночной картинки
Две цены — отображаются цена за базовую единицу (Fr 35.00/kg) и цена за единицу заказа (Fr 350.00/pkg). Анимированная стрелка указывает на активную цену
Выбор единицы измерения — под quantity selector показываются чипсы с orderable units. Если несколько — ChoiceChip для переключения
Минимальное количество — кнопка "−" не позволяет опуститься ниже минимума для выбранной единицы
Сумма — под кнопкой "Add to bag" отображается итог (price × conversion × qty)
Orderable units из API — бэкенд теперь отдаёт orderable_units прямо в продукте, Flutter парсит их в ProductModel
productsApiProvider — новый провайдер для ProductsApiService
selectedUnitIdProvider — shared state между экраном и action button
2. Supply Now — Главная страница
Файл: home_content_screen.dart

Убрана ссылка "See all" рядом с "Categories" (была нерабочая)
3. Supply Now — Заказы (Orders)
Файлы: orders_screen.dart, orders_provider.dart, order_status_badge.dart, client_order_model.dart, status_filter_chips.dart (новый)

Фильтрация по статусу — новый виджет StatusFilterChips с горизонтальными чипсами для фильтрации заказов по статусу
Расширены статусы — добавлены Ordered, CLIENT_CONFIRMATION_REQUIRED, Invoiced, Paid, Completed с цветами и переводами
Обновлена логика "active" — активные заказы теперь включают Ordered, SUPPLIER_MANAGER_REVIEW, CLIENT_CONFIRMATION_REQUIRED, In_Fulfillment (вместо In_Delivery)
Рефакторинг — _getStatusConfig вынесен в глобальную функцию getStatusConfig() для переиспользования в чипсах
4. Backend — Заказы
Файлы: orders/views.py, orders/serializers.py, orders/models.py

Удаление из корзины — UiAppCartViewSet.destroy() теперь пересчитывает total_sum и total_weight заказа после удаления товара
Fix calc_total_sum — исправлен баг с walrus operator (new_total := ...), когда total=0 считался falsy
Обновлён фильтр active — бэкенд тоже использует новый набор статусов для status_type=active
Fix unpacking — *_ заменён на *_rest в нескольких местах (избегает конфликт с gettext)
5. Backend — Продукты
Файл: products/serializers.py

orderable_units — новое поле в UiAppProductSerializer, возвращает список orderable units прямо в продукте
product_images — новое поле, возвращает все URL изображений товара (отсортированные по primary + sort_order)
6. Back Office — DataTable
Файл: data_table_widget.dart

Fix crash — замена IntrinsicHeight + OverflowBox на UnconstrainedBox + SizedBox (исправляет RenderBox assertion failure)
Разделитель — вместо отдельного Container(width:1) между frozen/scrollable колонками используется Border(right:) на frozen контейнере
7. Shared Core
Файл: dio_client.dart

Accept-Language — всегда отправляется header, дефолт 'en' если локаль ещё не загрузилась
8. Локализация
Файлы: app_en.arb, app_de.arb, app_fr.arb, app_it.arb + generated files

Новые ключи: statusOrdered, statusClientConfirmation, statusInvoiced, statusPaid, statusCompleted
9. Новые файлы
status_filter_chips.dart — виджет горизонтальных чипсов для фильтрации заказов
product_image_gallery.dart — галерея изображений товара со свайпом
seed305_james_pub_orders.py — seed-скрипт для тестовых данных

## 2026-02-25

- [x] Backend: LegalDocument model in common app (document_type unique, title/content translated EN/DE/FR/IT via modeltranslation)
- [x] Backend: LegalDocumentAdmin with CKEditor 5 + TabbedTranslationAdmin for multilingual HTML editing
- [x] Backend: LegalDocumentViewSet (AllowAny, lookup by document_type) + LegalDocumentSerializer
- [x] Backend: /api/common/legal/ endpoint (list + retrieve by type: terms, privacy)
- [x] Backend: seed_legal_documents management command — seeds T&C and Privacy Policy in all 4 languages
- [x] Supply Now: LegalDocumentModel (freezed) + legalDocumentProvider (FutureProvider.family)
- [x] Supply Now: Rewrite terms_conditions_screen and privacy_policy_screen — API-loaded HTML via flutter_widget_from_html_core, hardcoded fallback
- [x] Supply Now: Settings screen redesign — info cards (company, developer), "Powered by 8move" section with link
- [x] Supply Now: Trident Software logo in settings (SVG asset)
- [x] Supply Now: Navigation bar — 8move logo replaces home icon (colored inactive, white active)
- [x] Supply Now: ConvexAppBar icon sizing — OverflowBox for colored logo (42px), FractionallySizedBox for white logo (80%)
- [x] Supply Now: AppTab class extended with activeIcon and skipInactiveColorFilter fields
- [x] Supply Now: Product favorites — ProductFavorite model, toggle endpoint, FavoriteIconButton widget, favorites tab
- [x] Supply Now: Product grid/list items — favorite button, image border radius, price formatting improvements
- [x] Supply Now: New color tokens — surfaceContainerHighest, onSurfaceVariant, outlineVariant (light + dark)
- [x] Supply Now: New text styles — titleLarge, labelLarge (light + dark)
- [x] Supply Now: Localization (EN/DE/FR/IT) — legal documents, favorites, settings, powered by

## 2026-02-24

- [x] Supply Now: Product detail M3 redesign — SliverAppBar, pricing card, specs table, unit toggle, availability chip, bottom action bar
- [x] Supply Now: Share button (share_plus), favorite toggle, M3 color tokens, Outfit + DM Sans fonts
- [x] Supply Now: All units display — SegmentedButton shows orderable + non-orderable units, snackbar for blocked units
- [x] Supply Now: Next delivery date in availability chip (dispatch slots + delivery zone lookup)
- [x] Supply Now: Quantity selector flicker fix — replaced sync-in-build with ref.listen
- [x] Supply Now: Price defaults to first orderable unit when price unit is non-orderable
- [x] Backend: all_units field in UiAppProductSerializer (all units, not just orderable)
- [x] Backend: next_delivery_date field — delivery zone lookup with address fallback (primary → billing → registered)
- [x] Backend: ProductFavorite model + toggle-favorite endpoint + is_favorite on product response
- [x] Supply Now: Localization (EN/DE/FR/IT) — specifications, availability, unitNotOrderable, nextDelivery, share, etc.
- [x] Backend: Change active orders filter — Ordered, SUPPLIER_MANAGER_REVIEW, CLIENT_CONFIRMATION_REQUIRED, In_Fulfillment
- [x] Backend: Fix UnboundLocalError in CartOrderProductsSerializer — `*_` → `*_rest` (Python 3.12 scoping)
- [x] Backend: seed305 James Pub orders (Ordered, SUPPLIER_MANAGER_REVIEW, In_Fulfillment, Invoiced, Paid)
- [x] Supply Now: Update isActive extension to match new active statuses
- [x] Supply Now: Add status filter chips — multi-select, dynamic, reset, color-coded
- [x] Supply Now: Order status badge — all statuses with distinct colors (Ordered, CLIENT_CONFIRMATION_REQUIRED, Invoiced, Paid, Completed)
- [x] Supply Now: Localization (EN/FR/DE/IT) — statusOrdered, statusClientConfirmation, statusInvoiced, statusPaid, statusCompleted
- [x] Back Office: No-access blocking screen for users without SupplierUserLink
- [x] Back Office: Router — splash-based loading guard (prevent dashboard flash while suppliers load)
- [x] Back Office: Localization (EN/FR/DE/IT) — noAccess* strings (9 keys)

## 2026-02-23

- [x] Back Office: Orders Kanban board — kanban_board_widget, kanban_order_card, kanbanOrdersProvider, view switcher
- [x] Back Office: Supplier users list redesign + new supplier_user_edit_screen + avatar support
- [x] Backend: SupplierUserLinkSerializer — user_avatar field (S3 URL), select_related optimization
- [x] Back Office: Supplier users router — /users/:id/edit route
- [x] Back Office: Fix supplier persistence — restoreFromSuppliers + immediate check in DashboardLayout
- [x] Back Office: Localization updates (EN/DE/FR/IT) for kanban board strings
- [x] Back Office: Orders — fulfillment scheme filter locked to "Ship Now, Pay Later"
- [x] Back Office: Orders — kanban columns ordered by scheme's status workflow (statusOrders sorted by sortOrder)
- [x] Back Office: Orders — multi-select status filter (Set<String>, status__in API lookup)
- [x] Back Office: Orders — persistent view mode + filters via PreferencesStorage (SharedPreferences)
- [x] Back Office: Orders — auto-refresh data on page navigation (invalidate providers in initState)
- [x] Back Office: Orders — kanban card redesign (avatar, zone/address, copy/map, dispatch slot, ETA/ATA)
- [x] Back Office: Orders — kanban column headers (name+count, weight+sum)
- [x] Backend: Orders API — added fulfillment_scheme to filterset_fields, status supports in lookup
- [x] Supply Now: Changed app theme from green to Swiss red color palette (light_colors, dark_colors + 19 files with hardcoded colors)
- [x] Backend: UiAppProductSerializer — min_order_info with unit conversion, prefetch units__classifier
- [x] Supply Now: MinOrderInfoModel + minOrderFormat utility for product cards
- [x] Supply Now: Category item widget and product item widget UI improvements
- [x] Back Office: Product units section widget + product table cell redesign
- [x] Django i18n: German (DE) translations 100% — 1124/1124 entries filled, .mo compiled
- [x] Django i18n: Italian (IT) translations 100% — 1123/1123 entries filled, .mo compiled
- [x] Supply Now: New l10n keys (minOrder, perUnit, minimumOrder)
- [x] Back Office: Fix product list crash — removed LayoutBuilder from ProductTableCell (incompatible with IntrinsicHeight in frozen table rows)

## 2026-02-22

- [x] Backend: Make fulfillment_scheme read-only in SupplierSettingsSelfControlledSerializer and ClientSettingsSupplierControlledSerializer
- [x] Backend: Default new suppliers to "Ship Now, Pay Later" scheme in signals.py
- [x] Backend: Add CurrentUserMiddleware and SimpleJWTAuthenticationWithThreadLocal for audit trail
- [x] Backend: Refactor order number generation, add price unit conversion to OrderProductsSerializer
- [x] Backend: Add supplier filtering and search to ClientUserLink API
- [x] Back Office: Redesign dashboard with collapsible sidebar navigation (NavigationRail pattern)
- [x] Back Office: Add settings tab to profile with supplier picker and multi-supplier toggle
- [x] Back Office: Expand supplier settings form with branding (logo, company details, fiscal/VAT numbers)
- [x] Back Office: Remove fulfillment scheme selector from supplier settings and client form
- [x] Back Office: Restructure user management into supplier_users and client_users
- [x] Back Office: Redesign order products card with flex layout, show article and category
- [x] Back Office: Add 8move branding assets (icon, logo SVG)
- [x] Back Office: Add multi-supplier mode provider and preferences storage
- [x] Back Office: Add fiscalNumber and vatNumber to SupplierModel
- [x] Back Office: Add client users list screen and filters
- [x] Back Office: Order Products Bulk Edit dialog — compound ProductTableCell column (image + name + article + category)
- [x] Back Office: Order Products Bulk Edit dialog — client name + price category badge + individual prices count in header
- [x] Back Office: Order Products Bulk Edit dialog — unit warnings with color-coded badges and tooltips
- [x] Back Office: Order Products Bulk Edit dialog — orderable unit switcher dropdown (loads units per product in parallel)
- [x] Back Office: Order Products Bulk Edit dialog — enhanced price cell with individual/strikethrough, min order info
- [x] Back Office: Currency formatting — consistent "521.40 CHF" format everywhere (CurrencyDisplay.formatCurrency)
- [x] Back Office: Fixed model extensions (OrderProduct, BackOfficeOrder, ProductPrice, IndividualPrice) to use currency code
- [x] Back Office: Order summary card, delivery tab, optional card, form header — all use currencyCode now
- [x] Backend: ClientViewSet.get_serializer_class() — retrieve action now uses ClientListSerializer (returns price_category_name)
- [x] Back Office: New reusable widgets — ProductTableCell, AddressTableCell
- [x] Back Office: Bulk edit dialog — min order quantity enforcement (auto-increase + snackbar on Enter/Tab, blur, unit switch)
- [x] Back Office: Products list — pin/unpin category panel (sidebar ↔ dropdown chip with searchable dialog)
- [x] Back Office: Bulk edit dialog — fix total calculation for non-base units (unit conversion rate ratio)

## 2026-02-21

- [x] Backend: ReassignDeliveryZonesService — reuses admin panel assign_delivery_zone() logic
- [x] Backend: DataImportLog.operation_type field (bexio_csv_import / reassign_zones) + migration
- [x] Backend: ClientAddressDeliveryZone.is_manual field + migration
- [x] Backend: Zone reassignment rollback in DataImportRollbackService
- [x] Backend: POST /api/actors/clients/reassign-delivery-zones/ endpoint
- [x] Back Office: "Import" → "Bulk" rename with 3 menu items (Import CSV, Reassign Zones, History)
- [x] Back Office: Reassign zones dialog with include_manual checkbox, results dialog
- [x] Back Office: History screen — operation type badges, context-aware row log and rollback
- [x] Back Office: 20 new localization strings in EN/DE/FR/IT for bulk zone operations
- [x] Shared Core: reassignDeliveryZones() API method in clients_api_service.dart
- [x] Backend: get_price() extended to return (price, currency, actual_unit, is_individual, regular_price)
- [x] Backend: _get_regular_price() helper for strikethrough display when individual pricing applies
- [x] Backend: New product filters — has_image, has_base_price, base_unit_null, price_category, price_category_null
- [x] Backend: Updated order serializers for new get_price() return format
- [x] Back Office: Products list — frozen Name column with subtle divider for horizontal scrolling
- [x] Back Office: Products list — client price column with individual/regular price strikethrough display
- [x] Back Office: Products list — ClientPriceDialog for inline individual price editing
- [x] Back Office: Products list — advanced filter sheet (image, base price, base unit, price category)
- [x] Back Office: Category tree — "No category" filter with uncategorized products count badge
- [x] Back Office: Orders list — redesigned compact columns (status, client+#, address+zone+maps, slot, date, total+weight)
- [x] Back Office: Orders list — auto-filter by current supplier, removed debug prints
- [x] Shared UI: MapsConfigError enum + context-aware Google Maps error messages (no supplier link / no keys / fetch error)
- [x] Back Office: Localization updates in DE/EN/FR/IT for new filter labels and client price strings
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
