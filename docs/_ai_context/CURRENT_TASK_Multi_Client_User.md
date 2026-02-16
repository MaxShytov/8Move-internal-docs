## Task Definition

### Original Request
Один пользователь - несколько клиентов.

### Clarifying Questions
1. **Текущая архитектура** — Связь User↔Client уже M2M через `ClientUserLink` (app/actors/models.py:350-382). У User есть `current_client` FK (app/identity/models.py:61), но он автоматически ставится только при первом создании ClientUserLink и не обновляется после логина.
2. **Переключение клиентов** — В Profile/Companies должен быть выбор основного Client. Переключаться можно только на незаблокированных покупателей.
3. **Post-login flow** — После логина, если у пользователя несколько клиентов, должно открываться окно: "Подтвердите основную компанию, от которой вы будете делать заказы" + сообщение "Вы всегда можете изменить основного покупателя в Profile". В списке — только незаблокированные покупатели.
4. **Роли** — Пользователь может быть Admin в одной компании и Manager в другой. Роль хранится per-link в `ClientUserLink.role`.
5. **Контекст заказов** — Заказ оформляется от основной компании (`User.current_client`).
6. **Приложение** — Supply Now (Flutter) в первую очередь + Django backend (изменения для поддержки переключения).

### Similar Implementations (Benchmarks)

- **ClientUserLink model** (`app/actors/models.py:350-382`):
  M2M через промежуточную модель. Поля: `user`, `client`, `role` (ADMIN/MANAGER). При создании первого link — автоматически ставит `user.current_client`. Это основа — нужно добавить endpoint для смены `current_client`.

- **User.current_client** (`app/identity/models.py:61`):
  FK к Client, nullable. Уже хранит "основного клиента". Нужно: endpoint PATCH для смены, валидация что новый client есть в ClientUserLink и не заблокирован.

- **my-settings endpoint** (`app/actors/views.py:181-250`):
  `GET /api/actors/clients/my-settings/` — возвращает список компаний пользователя с ролями и настройками. Уже используется в Supply Now для Companies tab.

- **ClientCompanyModel (Flutter)** (`mobile/apps/supply_now/lib/core/data/data_models/clients/client_company_model.dart`):
  Freezed-модель: id, name, officialName, vatNumber, logo, email, primaryAddress, isBlockedByPlatform, selfSettings (isBlocked, preferredLanguage, defaultDeliveryAddress), role. Нужно добавить поле `isCurrent` или использовать отдельный state.

- **ClientCompaniesProvider** (`mobile/apps/supply_now/lib/features/profile/providers/client_companies_provider.dart`):
  Загружает список компаний через API. Поддерживает toggle self-block, update logo, update language. Нужно добавить: `switchCurrentClient(clientId)`.

- **Blocking system** (`app/preferences/models.py:87-267`):
  3 уровня блокировки: Platform (ClientControlledByPlatform), Self (ClientSettingsSelfControlled.is_blocked), Supplier (ClientSettingsSupplierControlled.is_blocked). При выборе основного клиента — фильтровать заблокированных.

- **Profile Screen tabs** (`mobile/apps/supply_now/lib/features/profile/view/profile_screen.dart`):
  3 таба: Personal, Companies, Suppliers. Companies tab показывает список компаний. Нужно добавить UI для выбора основной компании (radio/select).

- **Order model** (`app/orders/models.py:19-25`):
  `Order.client` — FK к Client с PROTECT. Заказ привязан к конкретному клиенту. При создании заказа используется `user.current_client`.

### Refined Task Description

**Task Title**: Multi-Client User Support in Supply Now

**Description**:
Реализовать полноценную поддержку множественных клиентов (компаний) для одного пользователя в Supply Now. Архитектура M2M (User↔Client через ClientUserLink) уже существует на backend, но UI не поддерживает переключение между клиентами. Нужно:
1. Добавить возможность выбора основной компании в Profile/Companies.
2. После логина показывать экран выбора компании (если у пользователя несколько незаблокированных клиентов).
3. Обновить backend endpoint для смены `current_client` с валидацией.
4. Убедиться, что все данные в приложении (заказы, адреса, поставщики) фильтруются по выбранному клиенту.

**Use Cases**:
1. **Первый логин с несколькими компаниями**: Пользователь логинится → видит экран "Выберите основную компанию" со списком незаблокированных клиентов → выбирает → попадает на главный экран.
2. **Первый логин с одной компанией**: Пользователь логинится → автоматически выбирается единственная компания → попадает на главный экран (без промежуточного экрана).
3. **Переключение компании в Profile**: Пользователь открывает Profile → Companies → нажимает "Сделать основной" у другой компании → подтверждение → данные приложения обновляются для нового клиента.
4. **Заблокированная компания**: Пользователь видит заблокированную компанию в списке, но не может выбрать её как основную. Если текущая компания заблокирована — предложить выбрать другую.
5. **Заказ от основной компании**: Пользователь создаёт заказ → заказ автоматически привязывается к `current_client`.

**Scope**:
- In scope:
  - Backend: endpoint PATCH для смены `current_client` с валидацией (client в ClientUserLink, не заблокирован)
  - Backend: поле `is_current` в ответе `my-settings` для идентификации основного клиента
  - Flutter: экран выбора компании после логина (Client Selection Screen)
  - Flutter: возможность переключения основной компании в Profile/Companies tab
  - Flutter: обновление данных приложения при смене клиента (заказы, адреса, поставщики)
  - Flutter: визуальная индикация текущего клиента в Companies tab
  - Flutter: фильтрация заблокированных клиентов при выборе
  - Локализация (en, de, fr, it)

- Out of scope (not in this task):
  - Создание новых ClientUserLink (приглашение пользователей в компании)
  - Управление ролями (смена ADMIN↔MANAGER)
  - Удаление/отвязка пользователя от компании
  - Back Office приложение
  - Изменение архитектуры M2M (уже работает)

**Success Criteria**:
- [ ] Backend: endpoint `PATCH /api/actors/clients/switch/` (или аналог) для смены current_client
- [ ] Backend: валидация — клиент должен быть в ClientUserLink пользователя и не заблокирован
- [ ] Backend: `my-settings` возвращает `is_current: true/false` для каждого клиента
- [ ] Flutter: после логина при >1 незаблокированном клиенте — показывается экран выбора компании
- [ ] Flutter: при 1 незаблокированном клиенте — автоматический выбор без экрана
- [ ] Flutter: в Profile/Companies есть кнопка/индикатор для смены основной компании
- [ ] Flutter: заблокированные компании нельзя выбрать как основные
- [ ] Flutter: при смене компании обновляются данные (поставщики, адреса, корзина)
- [ ] Flutter: текущая компания визуально выделена в списке
- [ ] Flutter: сообщение "Вы всегда можете изменить основного покупателя в настройках Profile"
- [ ] Все строки локализованы (en, de, fr, it)

**Technical Considerations**:
- `User.current_client` уже существует — не нужно менять модель, только добавить endpoint и валидацию
- При смене клиента нужно инвалидировать кеш/state всех зависимых данных (suppliers, addresses, orders, cart)
- Проверка блокировки: `ClientControlledByPlatform.is_blocked` OR `ClientSettingsSelfControlled.is_blocked` (и тот и другой)
- Для self-block: Admin пользователь всё ещё может войти, но non-admin не может
- Роль пользователя разная для разных компаний — UI должен отображать правильные permissions после переключения
- JWT токен не содержит client_id — клиент определяется из `user.current_client` на backend

### Complexity Assessment

**Complexity**: Medium
**Estimated effort**: 8-12 hours

**Risk factors**:
- State invalidation при смене клиента: нужно корректно обновить все Riverpod providers
- Edge cases с блокировками: пользователь может быть Admin (видит self-blocked компанию) но не Manager
- Корзина: что делать с товарами в корзине при смене компании? (очистить? сохранить per-client?)
- Race condition: если два устройства меняют current_client одновременно

### Components to Modify

**Django (Backend)**:
- Models: Без изменений (User.current_client и ClientUserLink уже есть)
- Views: `app/actors/views.py` — добавить action `switch_client` в ClientViewSet
- Views: `app/actors/views.py` — добавить `is_current` аннотацию в `my-settings`
- Serializers: `app/actors/serializers.py` — добавить `is_current` поле

**Flutter (Supply Now)**:
- Screens: New — `ClientSelectionScreen` (post-login выбор компании)
- Screens: Modify — `profile_screen.dart` Companies tab (добавить switch client UI)
- Providers: Modify — `client_companies_provider.dart` (добавить switchClient method)
- Providers: New or Modify — provider для current client state
- Models: Modify — `client_company_model.dart` (добавить isCurrent field)
- Auth flow: Modify — post-login navigation (добавить проверку количества клиентов)
- Localization: `app_en.arb`, `app_de.arb`, `app_fr.arb`, `app_it.arb`

**Database**:
- Migrations: Не ожидаются
- New tables/fields: Нет

### Dependencies

**Depends on**:
- `ClientUserLink` M2M model (уже существует)
- `User.current_client` FK (уже существует)
- `my-settings` API endpoint (уже существует)
- Blocking system (ClientControlledByPlatform, ClientSettingsSelfControlled)
- Auth/JWT flow (SimpleJWT)

**Will affect**:
- Login flow (добавляется промежуточный экран)
- Profile/Companies tab (добавляется UI переключения)
- Order creation (использует current_client)
- Suppliers list (фильтруется по current_client's ClientSupplierLinks)
- Addresses (фильтруются по current_client)
- Cart/Basket (может потребовать очистки при смене)

### Recommended Approach

1. **Backend: Add `switch_client` endpoint**
   - Action в ClientViewSet: `POST /api/actors/clients/{id}/switch/`
   - Валидация: client в ClientUserLink, не заблокирован (platform + self для non-admin)
   - Обновление `user.current_client`
   - Возврат обновлённого списка клиентов

2. **Backend: Add `is_current` to my-settings**
   - Аннотировать каждого клиента в ответе: `is_current = (client.id == user.current_client_id)`
   - Добавить `is_blocked` (combined platform + self) для удобства Flutter

3. **Flutter: Client Selection Screen**
   - Новый экран после логина
   - Список незаблокированных компаний с radio selection
   - Кнопка "Confirm" / "Continue"
   - Сообщение: "Вы можете изменить основную компанию в Profile → Companies"
   - Skip если только 1 незаблокированный клиент

4. **Flutter: Update Companies Tab**
   - Добавить визуальную индикацию текущей компании (checkmark, badge)
   - Кнопка "Make Primary" / "Switch to" для других компаний
   - Disabled state для заблокированных

5. **Flutter: State management при смене клиента**
   - Invalidate suppliers provider
   - Invalidate addresses provider
   - Clear cart (или confirm with user)
   - Reload relevant data

6. **Flutter: Auth flow integration**
   - После успешного логина → проверить количество клиентов
   - >1 незаблокированных → ClientSelectionScreen
   - 1 → auto-select → Home
   - 0 → показать сообщение "Нет доступных компаний"

7. **Локализация**
   - Добавить строки для всех новых UI элементов
