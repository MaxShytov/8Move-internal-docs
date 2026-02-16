# Backlog

Ideas and planned tasks that are not yet in active development. When a task moves to active work, create a task card in [Current Tasks](current/index.md) and remove it from here.

!!! info "Last updated: 2026-02-16"

---

## :material-monitor-dashboard: Back Office

| # | Task | Notes |
|---|------|-------|
| — | — | No backlog items yet |

---

## :material-cellphone: Supply Now

| # | Task | Notes |
|---|------|-------|
| 1 | Client Company Edit (self-service) | Client admin может редактировать данные своей компании. Supplier получает уведомление об изменениях. У Supplier могут быть свои (переопределённые) названия для клиента — нужно решить, как совмещать. |
| 2 | Billing & Registered Address editing | Дать возможность client admin редактировать Billing address и Registered address компании в Profile. Аналогично Primary address — с проверкой на наличие заказов. Зависит от задачи Client Addresses Profile. |
| 3 | Address Form — проверка и доработка | Проверить и донастроить форму добавления/редактирования адреса (address_form_screen.dart): валидация полей, Google Places autocomplete, корректное заполнение координат, обработка ошибок, UX при редактировании существующего адреса. |

---

## :material-truck: Fleet Manager

| # | Task | Notes |
|---|------|-------|
| — | — | No backlog items yet |

---

## :material-steering: Driver Pro

| # | Task | Notes |
|---|------|-------|
| — | — | No backlog items yet |

---

## :material-file-document: DocuFlow

| # | Task | Notes |
|---|------|-------|
| 1 | Сжатие изображений чеков при загрузке | При загрузке (фотографировании) чеков они сохраняются в базу очень большими. Нужно сжимать до минимального размера, достаточного для просмотра, печати и распознавания (OCR). |

!!! bug "Known bugs for this app → [Bug Tracker](bugs/index.md)"

---

## :material-server-network: Backend / Infrastructure

| # | Task | Notes |
|---|------|-------|
| 1 | Remove all UIBakery-related code from the Django project (authentication classes, settings, URLs, middleware, references) | Ранее пробовали подключить UIBakery, но отказались. Нужно полностью вычистить все упоминания — auth classes, settings, urls, imports, middleware. Чистый cleanup. |

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
