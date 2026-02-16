## 2026-02-16

- [x] Change Password dialog with validation (client-side + server-side)
- [x] Auto-save profile: debounced name fields, instant avatar save
- [x] RLA filters: ClientSupplierLink, Order — multi-supplier data isolation
- [x] Seed data: Swiss Timepieces Group SA, Standalone Boutique
- [x] Client Company Profile: real data from API (name, official name, VAT, etc.)
- [x] Company logo editing: admin can upload/change via camera/gallery, S3 upload
- [x] Swiss-style language selectors: abbreviations (EN/DE/FR/IT) instead of flags
- [x] Company blocking management: admin self-block toggle with confirmation dialogs
- [x] Backend absolute logo URLs for client and supplier my-settings endpoints
- [x] Full l10n for all new features (EN, DE, FR, IT)

### SN-03: Client Addresses in Profile
- Requirements gathered — scope defined (see notes below)

---

Несколько вопросов, чтобы точно определить scope:

Роли и доступ: Редактировать/удалять/добавлять адреса может только admin клиента (role=manager), или любой пользователь клиента тоже?
Редактировать/удалять/добавлять адреса может только admin клиента
Другие пользователи компании могут просматривать.

Выбор адреса доставки (п.5): Это значит установить default_delivery_address в настройках компании (ClientSettingsSelfControlled)? Или это per-user настройка (ClientUserSettingsSelfControlled.delivery_address)?

У клиента есть это:
Addresses
Primary address:
Billing address:
Registered address:
Нужно дать редактировать Primary address.
Редактирование других адресов - отсудим отдельно. Добавь эту задачу в Backlog
https://maxshytov.github.io/8Move-internal-docs/tasks/backlog/

Проверка заказов: По какой модели проверять наличие заказов? Есть ли уже связь Order → ClientAddress (или delivery_address)? Как называется модель заказа?
Найди сам это.

UI расположение: Список адресов будет отдельной вкладкой/секцией в Profile Screen, или отдельным экраном, на который переходим из профиля?
Это должен быть отдельный таб в. Profile.

Геокодинг: При добавлении/редактировании адреса нужно ли автоматически геокодировать (получать координаты)? Или это только backend-задача и на мобилке просто вводим текстовые поля?
Да, геокодинг обязательно нужен. Кажется в адресах даже есть lat/Lon и он нужен для доставки.

Также у пользователей должен быть удобный механизм ввода адресов.
Всегда по умолчанию страна должна быть Switzerland
Например, я ввожу Rue de l'Industrie 23, - автоматически должны подставляться 1950 Sion,
Возьми Bestpractice для подобных задач и реализу это.
