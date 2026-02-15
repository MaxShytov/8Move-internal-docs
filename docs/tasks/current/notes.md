# Working Notes

## Completed (2026-02-16)

- [x] Supply Now: change password dialog on Profile screen
- [x] Supply Now: auto-save profile (debounced name fields + instant avatar save)
- [x] Supply Now: improved snackbar notifications (floating, icons)
- [x] Supply Now: supplier count in tab label
- [x] API: `POST /api/identity/users/me/change-password/`
- [x] Localization: `currentPassword`, `passwordChangedSuccess` in EN/DE/FR/IT
- [x] RLA: `ClientSupplierLink.rla_roles_filter()` — supplier+client users can see their links
- [x] RLA: `Order.rla_roles_filter()` — fixed to include client user filtering
- [x] Tests: 13 automated RLA multi-supplier tests (all passing)
- [x] Seed: Swiss Timepieces Group SA — 2 users (admin + manager)
- [x] Seed: Standalone Boutique — client with 0 suppliers (edge case)
- [x] Supply Now: client company profile tab with real data

## Completed (2026-02-15)

- [x] Описание функционала для 6 групп пользователей → [Users & Roles](../../users/index.md)
- [x] Реорганизация задач по приложениям (Back Office, Supply Now, etc.)
- [x] Задача BO-01 для Back Office
- [x] Supply Now: реальные данные поставщиков на экране Профиль
- [x] Supply Now: блокировка/разблокировка поставщиков (admin)
- [x] Supply Now: индикаторы блокировки в селекторе поставщиков
- [x] API: `GET /api/actors/suppliers/my-settings/`
- [x] API: `POST /api/actors/suppliers/{id}/toggle-client-block/`
