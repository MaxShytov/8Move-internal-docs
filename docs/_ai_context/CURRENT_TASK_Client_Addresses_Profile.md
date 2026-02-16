## Task Definition

### Original Request
В Profile хочу:
1. Вывести список адресов клиента
2. Дать возможность добавлять адреса
3. Дать возможность админу клиента редактировать адрес. Если на этот адрес ранее были оформлены заказы — не давать редактировать, выводить сообщение с причиной и предложением задать другой адрес и выбрать его как основной.
4. Дать возможность удалять адрес, с такими же условиями по наличию заказов.
5. Дать возможность в компании выбирать Primary address (адрес доставки по умолчанию).

Также нужен удобный механизм ввода адресов с автокомплитом (Google Places), страна по умолчанию — Switzerland.

### Clarifying Questions
1. **Роли и доступ** — Admin клиента (role=manager) может добавлять/редактировать/удалять адреса. Остальные пользователи компании — только просмотр.
2. **Primary address** — Дать редактировать Primary address в настройках компании. Billing/Registered address — отдельная задача (→ backlog).
3. **Проверка заказов** — `Order.delivery_address` → FK к `ClientAddress` с `on_delete=PROTECT` (`app/orders/models.py:70-71`). Если на адрес есть заказы — запрет редактирования и удаления.
4. **UI расположение** — Отдельный таб "Addresses" в Profile Screen (4-й таб после Personal, Companies, Suppliers).
5. **Геокодинг** — Обязателен. Backend уже умеет: `GoogleMapsGeocoder` в `app/common/utils/geocoding.py`. Координаты нужны для доставки.
6. **Автокомплит адреса** — Google Places Autocomplete. Страна по умолчанию Switzerland. При вводе "Rue de l'Industrie 23" должно автоматически подставляться "1950 Sion".

### Similar Implementations (Benchmarks)

- **Back Office Client Address Form** (`mobile/apps/back_office/lib/features/clients/screens/client_address_form_screen.dart`):
  Полная CRUD-форма с полями: address_line, address_line2, city, postal_code, state, country (default CH), comment, label, lat/lon. Purpose selection через FilterChips (primary/billing/registered/other). Delivery zone display (read-only). Можно переиспользовать структуру формы и валидацию.

- **ClientsAddressesViewSet** (`app/actors/views.py:332-398`):
  REST API уже готов: GET/POST/PUT/DELETE + `set-purpose`/`clear-purpose` actions. Фильтрация по `owner`, RLA, аннотация delivery zones. Нужно добавить endpoint для проверки наличия заказов.

- **ClientsAddressesSerializer** (`app/actors/serializers.py:406-422`):
  Сериализатор с полями: все address fields + geocoding + delivery_zone (annotated). Purpose вычисляется через `get_purpose()`.

- **DeliveryAddress Dart Model** (`mobile/apps/supply_now/lib/core/data/data_models/address/delivery_address.dart`):
  Freezed-модель для Flutter. Поля: id, label, addressLine, addressLine2, comment, postalCode, city, state, lat, lon, isClientDefault, isUserDefault.

- **Address Widgets** (`mobile/apps/supply_now/lib/shared/address/`):
  `DeliveryAddressListWidget` — список адресов с выбором. `DeliveryAddressItemWidget` — карточка адреса с MapPreviewWidget. Уже используются в checkout. Можно переиспользовать.

- **Profile Screen tabs** (`mobile/apps/supply_now/lib/features/profile/view/profile_screen.dart:56, 515-536`):
  3 таба (Personal, Companies, Suppliers). TabController с length=3. Для добавления 4-го таба: изменить length на 4, добавить Tab и builder.

- **GoogleMapsGeocoder** (`app/common/utils/geocoding.py`):
  Backend geocoding готов. `geocode_address()` и `reverse_geocode()`. Мульти-тенантность: ключи из `SupplierSettingsSelfControlled`.

### Refined Task Description

**Task Title**: Client Addresses Tab in Supply Now Profile

**Description**:
Добавить таб "Addresses" в Profile Screen приложения Supply Now. Таб отображает список адресов доставки клиентской компании. Admin (role=manager) может добавлять новые адреса, редактировать и удалять существующие. Адреса, на которые были оформлены заказы, защищены от редактирования и удаления. В настройках компании можно выбрать Primary address. Ввод адреса — с автокомплитом через Google Places API (страна по умолчанию — Switzerland).

**Use Cases**:
1. **Просмотр адресов**: Любой пользователь компании открывает Profile → Addresses и видит список всех адресов компании с превью карты.
2. **Добавление адреса**: Admin вводит адрес с автокомплитом Google Places → система автоматически заполняет city, postal_code, state, country, lat/lon → сохранение.
3. **Редактирование адреса**: Admin нажимает на адрес → если нет заказов — открывается форма редактирования. Если есть заказы — показывается сообщение: "Этот адрес нельзя изменить, так как на него оформлены заказы. Создайте новый адрес и выберите его как основной в настройках компании."
4. **Удаление адреса**: Admin нажимает удалить → если нет заказов — подтверждение и удаление. Если есть заказы — аналогичное сообщение с объяснением.
5. **Выбор Primary address**: В настройках компании (Companies tab) admin может выбрать адрес доставки по умолчанию из списка адресов.

**Scope**:
- In scope:
  - Новый таб "Addresses" в Profile Screen
  - Список адресов клиентской компании с map preview
  - Форма добавления адреса с Google Places autocomplete
  - Форма редактирования адреса (с проверкой заказов)
  - Удаление адреса (с проверкой заказов)
  - Backend endpoint для проверки наличия заказов у адреса
  - Выбор Primary address в настройках компании
  - Геокодинг при создании/редактировании
  - Страна по умолчанию — Switzerland (CH)
  - Локализация (en, de, fr, it)

- Out of scope (not in this task):
  - Редактирование Billing address и Registered address (→ backlog)
  - Редактирование адреса на стороне Supplier
  - Карта с выбором точки на карте (pin drop)
  - Per-user default address (`ClientUserSettingsSelfControlled.delivery_address`)
  - Delivery zones management

**Success Criteria**:
- [ ] Таб "Addresses" отображается в Profile Screen (4-й таб)
- [ ] Список адресов загружается через API с map preview
- [ ] Admin может добавить новый адрес с Google Places autocomplete
- [ ] При вводе адреса автоматически подставляются city, postal_code, coordinates
- [ ] Страна по умолчанию — Switzerland
- [ ] Admin может редактировать адрес без заказов
- [ ] Admin НЕ может редактировать адрес с заказами (показывается сообщение)
- [ ] Admin может удалить адрес без заказов
- [ ] Admin НЕ может удалить адрес с заказами (показывается сообщение)
- [ ] Обычные пользователи видят адреса, но не могут редактировать/удалять/добавлять
- [ ] Admin может выбрать Primary address в настройках компании
- [ ] Все строки локализованы (en, de, fr, it)

**Technical Considerations**:
- Google Places API требует отдельный API key с включённым Places API (проверить настройки)
- Places Autocomplete нужно ограничить по стране (CH по умолчанию) для релевантных результатов
- Flutter пакет для Google Places: рассмотреть `google_places_autocomplete_text_field` или кастомную реализацию через HTTP API
- Backend уже имеет auto-geocoding при сохранении Address — нужно проверить, что координаты из Places API не перезаписываются
- Проверка заказов должна быть на backend (не только на клиенте) для безопасности
- RLA (Row-Level Access) уже настроен в ViewSet — адреса фильтруются по клиенту пользователя

### Complexity Assessment

**Complexity**: Medium-High
**Estimated effort**: 12-18 hours

**Risk factors**:
- Google Places API setup: Может потребоваться настройка API keys, включение Places API в Google Cloud Console
- Autocomplete UX: Правильная работа debounce, выбор результата, автозаполнение полей — требует тщательной проработки
- Order check logic: Нужно корректно обрабатывать edge cases (заказы в разных статусах)
- Multi-company: Пользователь может иметь доступ к нескольким компаниям — нужно корректно показывать адреса активной компании

### Components to Modify

**Django (Backend)**:
- Views: `app/actors/views.py` — добавить action `check_orders` в `ClientsAddressesViewSet` для проверки наличия заказов
- Views: `app/actors/views.py` — добавить валидацию в `perform_update`/`perform_destroy` для проверки заказов
- Serializers: `app/actors/serializers.py` — добавить поле `has_orders` / `order_count` в `ClientsAddressesSerializer`

**Flutter (Supply Now)**:
- Profile Screen: `mobile/apps/supply_now/lib/features/profile/view/profile_screen.dart` — добавить 4-й таб "Addresses"
- New: Address List Tab widget (в features/profile/view/ или widgets/)
- New: Address Form Screen (создание/редактирование с Google Places autocomplete)
- New: Addresses Provider (Riverpod notifier для CRUD операций)
- Existing: Переиспользовать `DeliveryAddressItemWidget`, `MapPreviewWidget` из `shared/address/`
- Localization: `app_en.arb`, `app_de.arb`, `app_fr.arb`, `app_it.arb` — новые строки

**Database**:
- Migrations: Не ожидаются (используем существующую модель ClientAddress)
- New tables/fields: Нет

### Dependencies

**Depends on**:
- Google Maps API key с включённым Places API
- Существующий `ClientsAddressesViewSet` API
- Существующая модель `ClientAddress`
- `Order.delivery_address` FK для проверки заказов
- `DeliveryAddress` Dart model
- Shared address widgets (`DeliveryAddressItemWidget`, `MapPreviewWidget`)

**Will affect**:
- Profile Screen (добавление таба)
- Company settings в Profile (выбор Primary address)
- Checkout flow (новые адреса будут доступны для выбора)
- Delivery zone assignment (автоматически при создании адреса)

### Recommended Approach

1. **Backend: Добавить проверку заказов**
   - Добавить annotated field `order_count` в `ClientsAddressesSerializer`
   - Добавить валидацию в `perform_update()` и `perform_destroy()` — reject если есть заказы
   - Возвращать понятное сообщение об ошибке

2. **Flutter: Google Places Autocomplete**
   - Добавить пакет или реализовать HTTP-вызовы к Google Places API
   - Создать переиспользуемый виджет `AddressAutocompleteField`
   - Ограничить поиск по стране (CH по умолчанию)
   - При выборе результата — автозаполнение всех полей + координаты

3. **Flutter: Address Form Screen**
   - Взять за основу `client_address_form_screen.dart` из back_office
   - Адаптировать под Supply Now стилистику (зелёные акценты, Raleway шрифт)
   - Интегрировать Google Places autocomplete
   - Добавить проверку `has_orders` — блокировать форму если есть заказы

4. **Flutter: Addresses Tab**
   - Добавить 4-й таб в Profile Screen
   - Переиспользовать `DeliveryAddressListWidget` / `DeliveryAddressItemWidget`
   - Добавить FAB "+" для создания (только для admin)
   - Добавить swipe-to-delete или контекстное меню (только для admin)

5. **Flutter: Primary Address Selection**
   - В Companies tab добавить dropdown/selector для Primary address
   - Использовать существующий `set-purpose` API endpoint

6. **Локализация**
   - Добавить все новые строки в arb-файлы (en, de, fr, it)

7. **Тестирование**
   - Backend: тесты на проверку заказов, валидацию update/delete
   - Manual: проверить autocomplete, CRUD, permissions, multi-company
