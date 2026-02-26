# Backlog

Ideas and planned tasks that are not yet in active development. When a task moves to active work, create a task card in [Current Tasks](current/index.md) and remove it from here.

!!! info "Last updated: 2026-02-26"


---

## :material-monitor-dashboard: Back Office

| # | Task | Notes |
|---|------|-------|
| 1 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |
| 2 | Profile / Suppliers: вывести информацию о поставщике в профиль + выбор текущего поставщика | По аналогии с DocuFlow (информация о покупателе в профиле). Админ может редактировать основные параметры поставщика, остальные — только просмотр. Выбор текущего поставщика аналогично выбору основного покупателя в DocuFlow. Заменяет текущий переключатель в хедере. |
| 3 | Добавить функционал пересчёта зон доставки | Продумать в каких местах и по каким триггерам запускать пересчёт. Затрагивает Backend, Fleet Manager и Back Office. |
| 4 | User Invitation Flow: менеджер приглашает пользователя клиента через email, покупатель регистрируется в Supply Now | High priority. Менеджер в Back Office находит клиента → нажимает "Invite" → вводит email → система создаёт неактивного пользователя, привязанного к клиенту, и отправляет email-приглашение. Покупатель получает письмо, скачивает Supply Now, завершает регистрацию → пользователь активируется. Менеджер видит статусы приглашений (sent, accepted, expired и т.д.). Требуется: backend (модель приглашения, отправка email, endpoint регистрации) + Back Office UI (кнопка invite, отображение статусов) + Supply Now (регистрация по приглашению). |
| 5 | Error reporting system: автоматическая и ручная отправка ошибок разработчикам | Два режима: 1) Кнопка "Send error report" в UI при ошибке 2) Auto-report в background (opt-in настройка в профиле). Отправлять максимум данных: error message, stack trace, DioException details, request/response, screen name, user context, app version. Канал доставки — минимум Email, возможно Sentry/Slack (продумать). Нужен backend endpoint для приёма reports. |
| 6 | Notification system overhaul: multi-recipient order notifications с per-user channel preferences | Сейчас `order_notification_recipient` — один FK на SupplierUserLink. Нужно: 1) M2M вместо FK — множественный выбор получателей в Settings, 2) Per-user notification preferences: email, in-app (колокольчик в системе), browser push — каждый юзер настраивает свои каналы, 3) In-app notification center (bell icon + unread count), 4) Browser push via Web Push API / FCM. Backend: переделать `preferences.models`, `notifications/signals.py`, `NotificationService`. Flutter: UI настроек в Settings, notification bell widget. Текущий механизм работает только через email и только на одного получателя. |
| 7 | Добавить разделы в Supplier Settings: Зоны доставки, Типы цен, Единицы измерения, Индустрии, Автомобили, Водители | 6 новых CRUD-разделов в настройках поставщика. Каждый раздел — управление справочником (список, создание, редактирование, удаление). |
| 8 | Delivery zone assignment map — интерактивная карта назначения зон доставки клиентам | На форме редактирования зон доставки показывать карту с клиентами: зелёные — с установленной зоной, красные — без зоны. Дать возможность назначать/менять зону прямо из карты (клик по клиенту → выбор зоны). |
| 9 | Supplier default currency: добавить выбор валюты поставщика в Settings | UI для выбора валюты в настройках поставщика. Валюта должна использоваться по умолчанию при создании цен, заказов, инвойсов. Требует backend-задачу (см. Backend #9). |
| 10 | Address geocoding & normalization: кнопка пересчёта адреса в форме | Кнопка "Пересчитать адрес" в форме адреса клиента/поставщика — по введённым данным через Google Maps Geocoding API автозаполнение city, canton, postal code, formatted address, координат. Массовый пересчёт существующих адресов. Требует backend (см. Backend #10). |
| 11 | Row click → detail side panel вместо прямого перехода в edit | Клик по строке в таблице (Products, Clients, Orders) открывает боковую панель (drawer справа) с просмотром информации + кнопка "Edit" для перехода в форму редактирования. Объём информации зависит от сущности: Products — краткая сводка, Orders — больше деталей и т.д. Затрагивает все основные списки. |
| 12 | Создание заказа из списка Products: кнопка "Add to Order" рядом с ценой клиента | Пользователь выбирает клиента в списке товаров → рядом с ценой клиента появляется кнопка "Add to Order". При нажатии: 1) если есть открытый заказ для этого клиента — добавляем товар в него, 2) если нет — создаём новый заказ. Использует существующий механизм выбора клиента в Products list. |
| 13 | Unit order step/multiplier: кратность заказа для единиц измерения | UI для задания и отображения кратности (order_step) на единице измерения товара. Например: шины кратно 2 или 4. При заказе количество округляется/валидируется до ближайшего кратного. Отображение кратности в форме товара и при создании заказа. Требует backend (см. Backend #11). |
| 14 | Unified Actor edit form: единая переиспользуемая форма для Suppliers и Clients | Сейчас формы Supplier и Client — отдельные экраны. Нужно создать один универсальный компонент формы Actor, который переиспользуется для обоих типов. Детали полей и секций будут уточнены отдельно. |
| 15 | Multilingual data audit: определить какие поля поставщик должен вводить на нескольких языках | Провести анализ моделей — какие данные должны поддерживать мультиязычный ввод (Supplier.description, Product.name, и т.д.). Результат — список полей + подход к реализации (django-modeltranslation, JSON field, отдельные таблицы). См. Backend #12 для реализации на бэкенде. |
| 16 | Аудит и доработка экрана Profile: проанализировать текущий экран, добавить недостающие поля, полноценное редактирование | Только фронтенд — работать с полями, уже доступными в API. **Основные поля:** `id` (int, PK), `email` (string?, уникальный), `username` (string?, уникальный), `first_name` (string?), `last_name` (string?), `phone` (string?, уникальный), `avatar` (string?, URL S3), `description` (string?, max 256), `is_active` (bool), `is_superuser` (bool), `date_joined` (datetime), `current_client` (int?, FK), `preferred_language` (string, default 'en'). **Вложенный объект driver_profile:** `id` (int), `supplier_id` (int), `supplier_name` (string), `driver_license_number` (string?), `is_active` (bool). **Write-only при создании:** supplier, client. **Не экспонируются через API (не включать):** timezone, default_exhibition, email_signature, is_staff, groups, user_permissions. |
| 17 | Support all fulfillment schemes in Orders (beyond "Ship Now, Pay Later") | Сейчас в Back Office обработана только схема "Ship Now, Pay Later". Нужно добавить поддержку остальных схем: разные наборы статусов, переходы, Kanban-колонки, фильтры. Backend уже поддерживает все схемы — доработка только на стороне Flutter. |
| 22 | Onboarding flow for suppliers: welcome screens, step-by-step profile setup wizard, and contextual tooltips | 3 компонента: 1) Welcome carousel при первом входе, 2) Пошаговая настройка (company info, logo, settings), 3) Контекстные подсказки по ключевым функциям (заказы, товары, клиенты). Backend: флаг onboarding_completed на модели user/supplier. |
| 18 | Реализовать систему свойств и вариантов товаров (properties & variants) | Расширение карточки товара: добавить свойства (цвет, размер и т.д.) и варианты. Фильтрация товаров по свойствам. Требует backend-работы (модели, API) + UI в Back Office. |
| 19 | Добавить страну производства (country of origin) в карточку товара | Поле "Страна производства" в форме товара, отображение в списке и фильтрация. Требует backend (модель, API). |
| 20 | Добавить поле SKU (артикул) в карточку товара | Поле SKU в форме товара, отображение в списке, поиск/фильтрация по SKU. Требует backend (модель, API). |
| 21 | Учёт остатков / доступности товара на складе (stock management) | Хранение количества товара на складе или как минимум статуса доступности (в наличии / нет / под заказ). Отображение в карточке товара и списке, фильтрация. Требует backend (модели, API) + UI в Back Office. |
| 23 | Анимация логотипа 8Move на кнопку главной страницы | Добавить анимацию логотипа 8Move на кнопку навигации к главной странице (dashboard). |

---

## :material-cellphone: Supply Now

| # | Task | Notes |
|---|------|-------|
| 1 | Client Company Edit (self-service) | Client admin может редактировать данные своей компании. Supplier получает уведомление об изменениях. У Supplier могут быть свои (переопределённые) названия для клиента — нужно решить, как совмещать. |
| 2 | Billing & Registered Address editing | Дать возможность client admin редактировать Billing address и Registered address компании в Profile. Аналогично Primary address — с проверкой на наличие заказов. Зависит от задачи Client Addresses Profile. |
| 3 | Address Form — проверка и доработка | Проверить и донастроить форму добавления/редактирования адреса (address_form_screen.dart): валидация полей, Google Places autocomplete, корректное заполнение координат, обработка ошибок, UX при редактировании существующего адреса. |
| 4 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |
| 5 | Регистрация на сайте Supply Now | Детали будут описаны позже. Нет зависимостей от других задач. |
| 6 | Включить нотификации для пользователя — push, in-app и email | Три канала уведомлений: push-уведомления, in-app нотификации (bell icon + unread count), email. Требует бэкенд-инфраструктуру для отправки и настройки предпочтений пользователя по каналам. Связано с Back Office #6 (Notification system overhaul). |
| 7 | Протестировать функционал Share на экране товара | Проверить работу кнопки Share (share_plus) на Android и iOS: корректность текста (название, цена, поставщик), отображение WhatsApp и других мессенджеров в системном диалоге, поведение на разных версиях ОС. |
| 8 | Onboarding flow for clients: welcome screens, step-by-step profile setup wizard, and contextual tooltips | 3 компонента: 1) Welcome carousel при первом входе, 2) Пошаговая настройка (company info, delivery address, preferences), 3) Контекстные подсказки по ключевым функциям (каталог, корзина, заказы). Backend: флаг onboarding_completed на модели user/client. |
| 9 | Управление пользователями клиента: список, приглашение, активация/деактивация | Client admin видит список пользователей своей компании. Возможность приглашать новых пользователей (email), активировать/деактивировать существующих. Связано с Back Office #4 (User Invitation Flow). Требует backend API. |
| 10 | Пересмотреть механизм редактирования адресов: смена тега (delivery, registered, invoicing) | Проверить и доработать возможность изменения типа/тега адреса (delivery, registered, invoicing) на существующем адресе. Убедиться что логика корректна при смене тега, включая валидацию и ограничения. |
| 11 | Протестировать работу с несколькими компаниями (multi-company) | Проверить переключение между компаниями клиента: корректность данных (каталог, цены, заказы, адреса), UI переключателя, edge cases (пользователь привязан к нескольким клиентам). |
| 12 | Протестировать сценарий нескольких покупателей (Company) у одного пользователя | Отдельный сценарий: один пользователь привязан к нескольким компаниям-покупателям. Проверить корректность работы, переключение, данные. Детали сценария будут описаны при работе с задачей. |
| 13 | История доставок: отображение route stop деталей (failure reason, delivery notes) | В истории заказов клиент должен видеть детали доставки из route stop: причина неудачной доставки (failure reason), заметки водителя (delivery notes). Проверить что данные приходят с API и корректно отображаются. |

---

## :material-truck: Fleet Manager

| # | Task | Notes |
|---|------|-------|
| 1 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |
| 2 | Profile / Suppliers + Depo: информация о поставщике в профиль, выбор текущего, просмотр и редактирование Depo | По аналогии с DocuFlow: информация о покупателе → информация о поставщике. Админ редактирует, остальные просматривают. Выбор текущего поставщика. Плюс экран Depo (аналог Адресов в DocuFlow) — просмотр и редактирование. |

---

## :material-steering: Driver Pro

| # | Task | Notes |
|---|------|-------|
| 1 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |

---

## :material-file-document: DocuFlow

| # | Task | Notes |
|---|------|-------|
| 1 | Сжатие изображений чеков при загрузке | При загрузке (фотографировании) чеков они сохраняются в базу очень большими. Нужно сжимать до минимального размера, достаточного для просмотра, печати и распознавания (OCR). |
| 2 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |

!!! bug "Known bugs for this app → [Bug Tracker](bugs/index.md)"

---

## :material-package-variant: Shared Packages (shared_ui / shared_core)

| # | Task | Notes |
|---|------|-------|
| 1 | Унифицировать экраны авторизации (Login, Register, Forgot/Reset Password) в shared_ui для всех Flutter-приложений | Единый дизайн, логика, переводы. Каждое приложение передаёт свой логотип/иконку. Бэкенд: убедиться что все приложения используют одни API-эндпоинты авторизации. Перед началом — проанализировать текущие реализации во всех приложениях и выбрать лучшую как основу. |
| 2 | Привести FrontEnd всех Flutter-приложений в соответствие с дизайном Figma | Аудит всех 5 приложений (Back Office, Supply Now, Fleet Manager, Driver Pro, DocuFlow) — сравнить текущий UI с макетами Figma, выявить расхождения и привести в соответствие. Ссылка на Figma будет добавлена позже. |
| 3 | Унифицировать экран Profile во всех Flutter-приложениях: единый layout и стиль | Привести экран Profile к единому виду во всех 5 приложениях (Back Office, Supply Now, Fleet Manager, Driver Pro, DocuFlow). Единый layout, стиль, расположение полей, кнопки, отступы. Создать shared виджет/шаблон Profile в shared_ui и переиспользовать во всех приложениях. Связано с Back Office #16 (аудит экрана Profile). |

---

## :material-server-network: Backend / Infrastructure

| # | Task | Notes |
|---|------|-------|
| 1 | Remove all UIBakery-related code from the Django project (authentication classes, settings, URLs, middleware, references) | Ранее пробовали подключить UIBakery, но отказались. Нужно полностью вычистить все упоминания — auth classes, settings, urls, imports, middleware. Чистый cleanup. |
| 2 | Сделать `preferred_language` source of truth на сервере: изменить `default=''` в модели User, при первом логине записывать язык устройства на сервер, при повторном — брать с сервера | Связано с `CURRENT_TASK_Shared_Profile_Module`. Backend: `app/identity/models.py` — `preferred_language default='' blank=True`. Flutter: логика в shared `locale_provider` — если `preferred_language` пустой → system locale → PATCH на сервер. Все 5 приложений. |
| 3 | Exhibitor: перенести email, phone и industry из отдельных полей в отдельные таблицы | Аналогично миграции в Clients (Actors/Contacts). Email и phone — из полей таблиц в отдельную таблицу. Industry — тоже вынести в отдельную таблицу. Связано с задачей Bexio CSV Import. |
| 4 | Добавить валидацию и форматирование Phone, Email, Website во всех формах всех приложений | Валидация на backend (Django) и frontend (Flutter) одновременно. Phone: международный формат, автоформатирование (e.g. +41 79 123 45 67). Email: RFC-валидация. Website: проверка URL формата. Применить везде где эти поля редактируются: клиенты, контакты, поставщики, пользователи и т.д. |
| 5 | Продумать и реализовать безопасное хранение API-ключей клиентов (Google Maps и др.) | Исследовать подходы: vault/secrets manager, шифрование в БД, env per-client. Сейчас ключи хранятся небезопасно — нужно проверить текущее состояние и спроектировать решение. |
| 6 | Локализация Django Admin на все 5 языков (en, fr, de, it, es) | Переключение языка per user, перевод model labels, help texts, admin actions. Приоритет: очень низкий |
| 7 | Автоматический поиск и загрузка логотипов клиентов по домену/названию компании | По аналогии с HubSpot. Варианты: Clearbit Logo API (бесплатный, по домену), Google Places API, favicon fallback. Backend: сервис обогащения данных клиента — при создании/редактировании клиента с website автоматически подтягивать логотип и сохранять в S3. Низкий приоритет. |
| 8 | Оптимизация изображений: thumbnails и resize при загрузке в S3 | Сейчас изображения загружаются as-is (после ImagePicker compress ~300-500KB) и отдаются в полном размере везде — даже для 44px аватарок в списках. ~100x overhead по трафику. Нужно: генерация thumbnails при загрузке (e.g. 100px, 300px, original), serve через CloudFront/CDN с автоматическим выбором размера. Варианты: Lambda@Edge resize on-the-fly, предгенерация при upload, или imgproxy. |
| 9 | Supplier default currency: добавить поле валюты в модель поставщика | Добавить `currency` (FK → Currency) в Supplier / SupplierSettings. Валюта поставщика используется по умолчанию при создании цен, заказов, инвойсов. Отдавать через API. См. Back Office #9 для UI-части. |
| 10 | Address geocoding & normalization: сервис пересчёта и нормализации адресов | Сервис geocoding через Google Maps Geocoding API — по введённому адресу автозаполнение/корректировка city, canton/state, postal code, formatted address, координат. Endpoint для пересчёта одного адреса + management command для массового пересчёта существующих. См. Back Office #10 для UI-части. |
| 11 | Unit order step/multiplier: добавить поле кратности в модель Unit | Добавить поле `order_step` (PositiveInteger, default=1) в модель Unit. Валидация: количество в заказе должно быть кратно order_step. Отдавать через API. См. Back Office #13 для UI-части. |
| 12 | Multilingual data support: мультиязычный ввод данных поставщиком | Реализовать хранение и API для мультиязычных полей (Supplier.description, Product.name и др.). Подход определить после аудита (django-modeltranslation, JSON field, отдельные таблицы). Языки: en, de, fr, it. См. Back Office #15 для UI-части и аудита полей. |
| 13 | Multi-backend архитектура: отдельные инстансы Django для разных поставщиков | Каждый поставщик работает на своём отдельном инстансе Django (отдельная БД, отдельный деплой). Затрагивает инфраструктуру, деплой, маршрутизацию запросов. |

---

## :material-dots-horizontal: Common

| # | Task | Notes |
|---|------|-------|
| 1 | Интеграция docs_internal с Jira: двусторонняя синхронизация задач | При добавлении/обновлении задачи в docs_internal (backlog.md, current tasks) — автоматическое создание и обновление Jira issues через GitHub Actions + Jira REST API. В markdown-документах — ссылка на Jira issue со статусом и worklogs. **Открытые вопросы:** 1) Механизм двусторонней синхронизации — как обрабатывать изменения со стороны Jira (webhook → PR в docs_internal? или только docs→Jira?), 2) Структура Jira — один проект на всё или отдельный проект на каждое приложение (Back Office, Supply Now, Fleet Manager, Driver Pro, DocuFlow), 3) Маппинг статусов Jira ↔ docs_internal (backlog → To Do, current task → In Progress, done → Done?), 4) Как хранить Jira issue ID в markdown — в YAML frontmatter или inline. **Реализация:** GitHub Actions триггер на push в docs_internal, парсинг diff в backlog.md/current tasks, Jira REST API для CRUD операций. Jira API token хранить в GitHub Secrets. |
| 2 | Browser navigation (back/forward) audit для всех Flutter web-приложений | Проверить и починить работу кнопок "назад/вперёд" в браузере во всех web-приложениях (Back Office, Fleet Manager). Включая сохранение и восстановление состояния фильтров при навигации. Затрагивает go_router и URL query parameters. |

---

## How to add a backlog item

Add a row to the appropriate app table:

```markdown
| 1 | Short task description | Any context, links, or priority notes |
```

When the task moves to active development:

1. Create a full task file in `docs/_ai_context/CURRENT_TASK_*.md`
2. Create a summary card in `tasks/current/<app>/XX-NN.md`
3. Add it to [Current Tasks](current/index.md)
4. Remove the row from this page
