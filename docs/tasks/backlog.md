# Backlog

Ideas and planned tasks that are not yet in active development. When a task moves to active work, create a task card in [Current Tasks](current/index.md) and remove it from here.

!!! info "Last updated: 2026-02-18"


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

---

## :material-cellphone: Supply Now

| # | Task | Notes |
|---|------|-------|
| 1 | Client Company Edit (self-service) | Client admin может редактировать данные своей компании. Supplier получает уведомление об изменениях. У Supplier могут быть свои (переопределённые) названия для клиента — нужно решить, как совмещать. |
| 2 | Billing & Registered Address editing | Дать возможность client admin редактировать Billing address и Registered address компании в Profile. Аналогично Primary address — с проверкой на наличие заказов. Зависит от задачи Client Addresses Profile. |
| 3 | Address Form — проверка и доработка | Проверить и донастроить форму добавления/редактирования адреса (address_form_screen.dart): валидация полей, Google Places autocomplete, корректное заполнение координат, обработка ошибок, UX при редактировании существующего адреса. |
| 4 | Пересмотреть UI и перевести на Atomic Design System (atoms → molecules → organisms) | Полный аудит текущих виджетов, расширить shared_ui package по принципам Atomic Design с соблюдением Flutter best practices. Заменить существующие компоненты на атомарные. Средний приоритет. |
| 5 | Регистрация на сайте Supply Now | Детали будут описаны позже. Нет зависимостей от других задач. |

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
