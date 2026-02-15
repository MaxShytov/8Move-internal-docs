# BE-01: Full Task Details

> :material-arrow-left: Back to [summary card](../BE-01.md)

## Task Definition

### Original Request

У меня была идея, что один покупатель (пользователь с одним e-mail) может работать одновременно с несколькими поставщиками, через таблицу Linked Suppliers (ClientSupplierLink).
Теперь нужно это перепроверить, по идее это должно быть видно в приложении:
1. Supply Now - тут пользователь должен видеть всех поставщиков, с которыми он может быть связан. Их может быть любое количество от 0 до бесконечности.
2. BackOffice - это приложение для поставщика. Соответственно поставщик, может видеть только своих клиентов и не может видеть других поставщиков клиента.
3. Admin панель - это приложение для администраторов платформы. Тут для каждого клиента можем видеть поставщиков, с которым он связан. И для каждого поставщика можем видеть всех клиентов, с которым он связан.

Нужно спланировать проверку этого функционала. Подготовить список пользователей для проверки.

### Clarifying Questions
1. Какой тип проверки нужен? — **Оба варианта**: ручное тестирование через UI (чек-лист) + автоматические тесты (Django TestCase)
2. Нужно ли создавать тестовых пользователей? — В тестовой базе есть seed пользователи, если их недостаточно — подправить Seed
3. Какие сценарии беспокоят? — **Все**: изоляция данных, множественные связи, блокировка связей

### Similar Implementations (Benchmarks)

- **ClientSupplierLink model**: `app/actors/models.py:290-313` — ключевая модель many-to-many связи клиентов и поставщиков
- **RLA filtering (Supplier)**: `app/actors/models.py:105-115` — фильтрация поставщиков: через SupplierUserLink + ClientSupplierLink
- **RLA filtering (Client)**: `app/actors/models.py:159-173` — фильтрация клиентов: через ClientUserLink + SupplierUserLink (через ClientSupplierLink)
- **RLA filtering (Product)**: `app/products/models.py:111-112` — использует `rla_default_by_supplier_field`
- **RLA filtering (Order)**: `app/orders/models.py:171-179` — фильтрация ТОЛЬКО по supplier links (потенциальная проблема для client users!)
- **Admin inlines**: `app/actors/admin.py:185-202` (ClientInSupplierInline), `app/actors/admin.py:328-351` (SupplierInClientInline)
- **Seed data**: `app/common/management/commands/seed030_user_links_with_role.py:97-128` — создание ClientSupplierLink записей
- **Base RLA methods**: `app/common/models.py:65-114` — rla_default_by_client_field, rla_default_by_supplier_field
- **RLA Manager**: `app/common/managers.py:4-17` — superuser и platform admin видят всё

### Refined Task Description

**Task Title**: Multi-Supplier Client Access Verification

**Description**:
Проверить, что архитектура multi-supplier (один клиент — множество поставщиков через ClientSupplierLink) корректно работает во всех трёх приложениях: Supply Now (мобильное для клиентов), BackOffice (мобильное для поставщиков), Admin (Django admin для платформы). Проверка включает изоляцию данных, множественные связи и механизм блокировки.

**Use Cases**:
1. **Клиент видит всех своих поставщиков (Supply Now)**: Пользователь-клиент (например, Hôtel Edelweiss) открывает Supply Now и видит список всех 5 поставщиков, с которыми связан
2. **Поставщик видит только своих клиентов (BackOffice)**: Пользователь-поставщик (Alpine Frais) входит в BackOffice и видит только своих 6 клиентов, не видит клиентов TechnoLivraison
3. **Изоляция продуктов и заказов**: Продукты поставщика A не видны поставщику B; заказы фильтруются по поставщику
4. **Блокировка связи**: Если поставщик заблокировал клиента (is_blocked), клиент не может делать заказы
5. **Платформенный админ видит всё**: В Django admin видны все связи, все клиенты, все поставщики

**Technical Considerations**:
- `ClientSupplierLink` НЕ имеет собственного `rla_roles_filter()` — возвращает пустой queryset по умолчанию (потенциальная проблема)
- `Order.rla_roles_filter` фильтрует ТОЛЬКО по `supplier__supplieruserlink__user` — клиенты могут не видеть свои заказы
- RLA Manager даёт полный доступ superuser и platform admin (обход фильтров)
- Блокировка реализована в двух моделях: `ClientSettingsSupplierControlled.is_blocked` (поставщик блокирует клиента) и `SupplierSettingsClientControlled.is_blocked` (клиент блокирует поставщика)

### Complexity Assessment

**Complexity**: Medium
**Estimated effort**: 4-6 hours

**Risk factors**:
- Risk 1: Order RLA может не показывать заказы клиентским пользователям (нужна проверка)
- Risk 2: ClientSupplierLink без rla_roles_filter — API endpoint может возвращать пустые данные для неадминов
- Risk 3: Блокировка is_blocked может не учитываться во всех местах (заказы, продукты, UI)

---

## Test Data Matrix

### Seed ClientSupplierLink (кто с кем связан)

| Клиент | Alpine Frais | TechnoLivraison | Café Mont Léman | Le Glaçons Suiss | Swiss Timepieces |
|---|---|---|---|---|---|
| Le Petit Délice | :material-check: | | :material-check: | :material-check: | |
| Auberge du Lac | :material-check: | | :material-check: | :material-check: | |
| **Hôtel Edelweiss** | :material-check: | :material-check: | :material-check: | :material-check: | :material-check: |
| ElectroTop | | :material-check: | | | |
| École Romande | :material-check: | :material-check: | :material-check: | :material-check: | |
| Julien Morel | :material-check: | | :material-check: | :material-check: | |
| Sophie Bernard | :material-check: | | :material-check: | :material-check: | |

### Key Test Users

#### Supplier Users (для BackOffice)

| User | Email | Supplier | Role |
|---|---|---|---|
| Marc Dupont | marc.dupont_alpine_frais_sarl@trident.software | Alpine Frais Sàrl | admin |
| Etienne Lambert | etienne.lambert_techno_livraison_sa@trident.software | TechnoLivraison SA | admin |
| Julien Favre | julien.favre_8move@trident.software | Café Mont Léman Sàrl | admin |
| Milan Thomas | milan@leglaconsuisse.ch | Le Glaçons Suiss SA | admin |

#### Client Users (для Supply Now)

| User | Email | Client | Role | Linked Suppliers |
|---|---|---|---|---|
| **Aline Dupuis** | aline.dupuis_edelweiss-hotel@trident.software | **Hôtel Edelweiss** | admin | **5 поставщиков** (макс.) |
| Luca Meier | info_lepetitdelice@trident.software | Le Petit Délice | admin | 3 поставщика |
| Luc Blanc | luc.blanc_electrotop@trident.software | ElectroTop | admin | 1 поставщик (мин.) |
| Julien Ziegler | julien_proton@trident.software | Julien Morel (private) | admin | 3 поставщика |

#### Platform Users (для Admin)

| User | Email | Role |
|---|---|---|
| Alice Anderson | alice.admin_8move@trident.software | platform admin |
| admin | admin | superuser |

### Missing Test Scenario: Client with 0 Suppliers
**В seed нет клиента без поставщиков!** Нужно добавить в seed или создать вручную.

---

## Quick Launch Commands

!!! info "Пароль для ВСЕХ пользователей"
    `Password123`

    Скопируйте нужную команду и вставьте в терминал. Приложение откроется в Chrome и автоматически залогинится под выбранным пользователем.

### Supply Now (Client App) — порт 8084

**Запускать из:** `cd mobile/apps/supply_now`

#### Hôtel Edelweiss — 5 поставщиков (максимум)
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=aline.dupuis_edelweiss-hotel@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### Hôtel Edelweiss — другой сотрудник (Mathieu Perrin)
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=mathieu.perrin_edelweiss-hotel@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### Le Petit Délice — 3 поставщика
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=info_lepetitdelice@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### ElectroTop — 1 поставщик (минимум)
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=luc.blanc_electrotop@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### Julien Morel — 3 поставщика (частное лицо)
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=julien_proton@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### École Romande — 4 поставщика
```bash
flutter run -d chrome --web-port 8084 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=julie.renaud_ecoleromande@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

### BackOffice (Supplier App) — порт 8080

**Запускать из:** `cd mobile/apps/back_office`

#### Alpine Frais Sàrl — 6 клиентов
```bash
flutter run -d chrome --web-port 8080 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=marc.dupont_alpine_frais_sarl@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### TechnoLivraison SA — 3 клиента
```bash
flutter run -d chrome --web-port 8080 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=etienne.lambert_techno_livraison_sa@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### Café Mont Léman Sàrl
```bash
flutter run -d chrome --web-port 8080 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=julien.favre_8move@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

#### Le Glaçons Suiss SA
```bash
flutter run -d chrome --web-port 8080 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=milan@leglaconsuisse.ch \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

### Admin Panel (Django Admin)

- URL: http://localhost:8000/admin/
- Superuser: `admin` / `Password123`
- Platform Admin: `alice.admin_8move@trident.software` / `Password123`

---

## Manual Test Checklist

### A. Supply Now (Mobile — Client App)

#### A1. Множественные поставщики
- [ ] Логин: `aline.dupuis_edelweiss-hotel@trident.software` (Hôtel Edelweiss)
- [ ] Видны ровно 5 поставщиков: Alpine Frais, TechnoLivraison, Café Mont Léman, Le Glaçons Suiss, Swiss Timepieces
- [ ] Каждого поставщика можно выбрать
- [ ] Продукты отображаются для каждого поставщика отдельно

#### A2. Один поставщик
- [ ] Логин: `luc.blanc_electrotop@trident.software` (ElectroTop)
- [ ] Виден ровно 1 поставщик: TechnoLivraison
- [ ] Нет доступа к продуктам других поставщиков

#### A3. Три поставщика (частное лицо)
- [ ] Логин: `julien_proton@trident.software` (Julien Morel — private)
- [ ] Видны ровно 3 поставщика: Alpine Frais, Café Mont Léman, Le Glaçons Suiss

#### A4. Ноль поставщиков (edge case)
- [ ] **Нужен тест-юзер без связей** (добавить в seed)
- [ ] Корректное сообщение "нет доступных поставщиков" или пустой список

### B. BackOffice (Mobile — Supplier App)

#### B1. Alpine Frais — видит своих клиентов
- [ ] Логин: `marc.dupont_alpine_frais_sarl@trident.software`
- [ ] Видны 6 клиентов: Julien Morel, Le Petit Délice, Auberge du Lac, Hôtel Edelweiss, École Romande, Sophie Bernard
- [ ] НЕ видны клиенты других поставщиков (ElectroTop)

#### B2. TechnoLivraison — видит своих клиентов
- [ ] Логин: `etienne.lambert_techno_livraison_sa@trident.software`
- [ ] Видны 3 клиента: Hôtel Edelweiss, ElectroTop, École Romande
- [ ] НЕ видны: Le Petit Délice, Auberge du Lac, Julien Morel, Sophie Bernard

#### B3. Изоляция продуктов
- [ ] Продукты Alpine Frais НЕ видны в BackOffice TechnoLivraison
- [ ] Продукты TechnoLivraison НЕ видны в BackOffice Alpine Frais

#### B4. Изоляция заказов
- [ ] Заказы от Hôtel Edelweiss для Alpine Frais НЕ видны в BackOffice TechnoLivraison
- [ ] Каждый поставщик видит только свои заказы

#### B5. Поставщик НЕ видит других поставщиков клиента
- [ ] В карточке Hôtel Edelweiss (BackOffice Alpine Frais) НЕ показаны другие поставщики этого клиента (TechnoLivraison, Café Mont Léman, etc.)

### C. Admin Panel (Django Admin)

#### C1. ClientSupplierLink — полный обзор
- [ ] Логин: `admin` / `Password123` (superuser)
- [ ] В разделе ClientSupplierLink видны ВСЕ связи
- [ ] Можно фильтровать по client и supplier
- [ ] Можно искать по имени

#### C2. Supplier detail — inline клиенты
- [ ] Открыть Alpine Frais Sàrl → видны inline ClientSupplierLink записи
- [ ] Показаны 6 связанных клиентов
- [ ] Видно поле is_blocked_by_client

#### C3. Client detail — inline поставщики
- [ ] Открыть Hôtel Edelweiss → видны inline ClientSupplierLink записи
- [ ] Показаны 5 связанных поставщиков
- [ ] Видны настройки: period, weekly_day, monthly_day

#### C4. Platform admin (не superuser)
- [ ] Логин: `alice.admin_8move@trident.software`
- [ ] Также видит всё (platform admin bypass в RLA Manager)

### D. Blocking (is_blocked)

#### D1. Поставщик блокирует клиента
- [ ] В Admin: для связи Alpine Frais ↔ Hôtel Edelweiss установить is_blocked = True (в ClientSettingsSupplierControlled)
- [ ] В Supply Now (Aline Dupuis): проверить, виден ли Alpine Frais в списке поставщиков
- [ ] Ожидание: Alpine Frais не должен быть доступен для заказов
- [ ] Вернуть is_blocked = False

#### D2. Клиент блокирует поставщика
- [ ] В Admin: для связи Alpine Frais ↔ Hôtel Edelweiss установить is_blocked = True (в SupplierSettingsClientControlled)
- [ ] В Supply Now (Aline Dupuis): проверить доступность Alpine Frais
- [ ] Ожидание: Alpine Frais не должен быть доступен для заказов
- [ ] Вернуть is_blocked = False

---

## Automated Tests Plan (Django TestCase)

### Test File: `app/actors/tests/test_rla_multi_supplier.py`

```python
class TestMultiSupplierRLA(TestCase):
    """Tests for multi-supplier client access via RLA filtering."""

    @classmethod
    def setUpTestData(cls):
        # Create: 3 suppliers (S1, S2, S3)
        # Create: 3 clients (C1 with 3 suppliers, C2 with 1 supplier, C3 with 0 suppliers)
        # Create: Users for each supplier and client
        # Create: ClientSupplierLink records

    # --- Supplier RLA Tests ---

    def test_supplier_user_sees_only_own_supplier(self):
        """Supplier user (S1) should see only S1, not S2 or S3."""

    def test_client_user_sees_all_linked_suppliers(self):
        """Client user (C1) should see S1, S2, S3 (all linked suppliers)."""

    def test_client_user_with_one_supplier_sees_only_one(self):
        """Client user (C2) should see only S2."""

    def test_client_user_with_no_suppliers_sees_none(self):
        """Client user (C3) should see no suppliers."""

    # --- Client RLA Tests ---

    def test_supplier_user_sees_only_linked_clients(self):
        """S1 user should see C1 (linked) but not C2, C3."""

    def test_supplier_user_does_not_see_other_supplier_clients(self):
        """S1 user should not see clients exclusively linked to S2."""

    def test_client_user_sees_own_client(self):
        """C1 user should see C1."""

    # --- Product RLA Tests ---

    def test_supplier_products_isolated(self):
        """S1 products not visible to S2 user."""

    def test_client_sees_linked_supplier_products(self):
        """C1 user should see products from S1, S2, S3."""

    # --- Order RLA Tests ---

    def test_supplier_sees_only_own_orders(self):
        """S1 user sees only orders where supplier=S1."""

    def test_order_isolation_between_suppliers(self):
        """S1 user cannot see S2 orders even for shared client."""

    # --- Superuser & Platform Admin Tests ---

    def test_superuser_sees_all_suppliers(self):
        """Superuser bypasses RLA."""

    def test_platform_admin_sees_all_clients(self):
        """Platform admin bypasses RLA."""

    # --- Blocking Tests ---

    def test_blocked_by_supplier_still_in_supplier_list(self):
        """is_blocked should not affect RLA queryset (only ordering ability)."""

    def test_blocked_by_client_still_in_client_list(self):
        """is_blocked should not affect RLA queryset (only ordering ability)."""
```

### Potential Issues Found During Analysis

1. **`ClientSupplierLink` не имеет `rla_roles_filter()`** — API endpoint `/api/client_supplier_link/` вернёт пустой queryset для неадминов
2. **`Order.rla_roles_filter` фильтрует ТОЛЬКО по supplier** — клиентские пользователи могут НЕ видеть свои заказы через API
3. **`Swiss Timepieces Group SA` не имеет пользователей в seed** — нельзя залогиниться в BackOffice этого поставщика
