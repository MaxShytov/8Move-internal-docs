# Run Commands

Команды для быстрого запуска всех приложений с авто-логином.

!!! info "Пароль для ВСЕХ пользователей"
    `Password123`

---

## Backend

```bash
cd django_project
./run-dev.sh
```

Сервер запускается на `http://localhost:8000`. Скрипт автоматически:

- Создаёт/активирует виртуальное окружение
- Запускает Docker (PostgreSQL, Redis)
- Применяет миграции
- Стартует Django dev server

---

## Port Allocation

| Port | App | User |
|------|-----|------|
| 8000 | Backend | — |
| 8080 | Back Office | max@trident.software |
| 8081 | Fleet Planner | max@trident.software |
| 8082 | Driver Pro | dvbn777drc@gmail.com |
| 8090 | Supply Now | Claire (Auberge du Lac, multi-client) |
| 8091 | DocuFlow | max@trident.software |

---

## Back Office

Приложение для поставщиков (Supplier).

```bash
cd mobile/apps/back_office
```

```bash
flutter run -d chrome --web-port 8080 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=max@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

---

## Supply Now

Приложение для клиентов (Client).

```bash
cd mobile/apps/supply_now
```

```bash
flutter run -d chrome --web-port 8090 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=claire_aubergedulac@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

> Claire — admin в Auberge du Lac, manager в Le Petit Délice и Hôtel Edelweiss. Для тестирования multi-client.

---

## DocuFlow

Приложение для обработки документов и чеков.

```bash
cd mobile/apps/docuflow
```

```bash
flutter run -d chrome --web-port 8091 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=max@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

> Больше пользователей для Supply Now и Back Office: [Quick Launch Commands](current/details/BE-01-full.md#quick-launch-commands)

---

## Fleet Planner

Приложение для диспетчеров маршрутов (Delivery Manager).

```bash
cd mobile/apps/fleet_planner
```

```bash
flutter run -d chrome --web-port 8081 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=max@trident.software \
  --dart-define=AUTO_LOGIN_PASSWORD=Password123
```

---

## Driver Pro

Приложение для водителей (Driver).

```bash
cd mobile/apps/driver_pro
```

```bash
flutter run -d chrome --web-port 8082 \
  --dart-define=API_BASE_URL=http://localhost:8000 \
  --dart-define=AUTO_LOGIN_EMAIL=dvbn777drc@gmail.com \
  --dart-define=AUTO_LOGIN_PASSWORD=password123
```

---

## Запуск нескольких приложений

Для одновременного запуска откройте отдельный терминал для каждого приложения. Порты не конфликтуют:

```
Backend:       http://localhost:8000
Back Office:   http://localhost:8080
Fleet Planner: http://localhost:8081
Driver Pro:    http://localhost:8082
Supply Now:    http://localhost:8090
DocuFlow:      http://localhost:8091
```
