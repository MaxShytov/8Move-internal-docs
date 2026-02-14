# Getting Started — Flutter Apps

## Prerequisites

- **Flutter SDK** 3.9.2+ (with Dart 3.9.2+)
- **Git**
- **Django backend** running locally (see [Backend Setup](../backend/getting-started.md))

For platform-specific builds:

| Platform | Requirements |
|----------|-------------|
| **Web** | Chrome browser |
| **macOS** | Xcode 13+ |
| **Android** | Android Studio + Android SDK (API 21+) |
| **iOS** | macOS + Xcode 13+ + CocoaPods |

---

## Project Structure

The Flutter code lives in `django_project/mobile/` as a **monorepo**:

```
mobile/
├── apps/                          # Flutter applications
│   ├── back_office/               # Internal management (macOS/Web)
│   ├── docuflow/                  # Document processing & OCR
│   ├── driver_pro/                # Driver mobile app
│   ├── fleet_planner/             # Route optimization for HoReCa
│   └── supply_now/                # B2B supply management
├── packages/                      # Shared libraries
│   ├── shared_core/               # API clients, models, business logic
│   ├── shared_ui/                 # UI components, themes
│   └── workflow_editor/           # Workflow editor component
└── README.md
```

---

## Install Flutter

### macOS (recommended)

```bash
# Download and install
cd ~/development
wget https://storage.googleapis.com/flutter_infra_release/releases/stable/macos/flutter_macos_3.9.2-stable.zip
unzip flutter_macos_3.9.2-stable.zip

# Add to PATH (add to ~/.zshrc for persistence)
export PATH="$PATH:$HOME/development/flutter/bin"

# Verify installation
flutter --version
flutter doctor
```

### Other platforms

See the [Flutter installation guide](https://docs.flutter.dev/get-started/install).

---

## Setup

### 1. Install dependencies for shared packages first

Shared packages must be installed **before** apps, since apps depend on them:

```bash
cd django_project/mobile

# Shared packages (order matters)
flutter pub get -C packages/shared_core
flutter pub get -C packages/shared_ui
flutter pub get -C packages/workflow_editor
```

### 2. Install dependencies for the app you want to run

```bash
# Pick one:
flutter pub get -C apps/back_office
flutter pub get -C apps/docuflow
flutter pub get -C apps/driver_pro
flutter pub get -C apps/fleet_planner
flutter pub get -C apps/supply_now
```

### 3. Configure API URL

Each app needs a `config.json` to know where the backend is.

**For Web:** Copy the example config and set the local API URL:

```bash
cd apps/<app_name>/web
cp config.example.json config.json
```

Edit `config.json`:

```json
{
  "api_base_url": "http://localhost:8000"
}
```

**For macOS/mobile:** Create `assets/config.json` in the app directory:

```bash
cd apps/<app_name>
mkdir -p assets
cat > assets/config.json << 'EOF'
{
  "api_base_url": "http://localhost:8000"
}
EOF
```

!!! tip
    Some apps already have `assets/config.json` pointing to `localhost:8000`. Check before creating.

### 4. Run code generation

Some packages use code generation (Freezed, Retrofit, Riverpod). Run it after the first `pub get`:

```bash
# For shared_core
cd packages/shared_core
dart run build_runner build --delete-conflicting-outputs

# For the app
cd apps/<app_name>
dart run build_runner build --delete-conflicting-outputs
```

---

## Running Apps

### Make sure the backend is running

```bash
# In a separate terminal
cd django_project
./run-dev.sh
```

### Run a Flutter app

```bash
cd mobile/apps/<app_name>

# Web (works for all apps)
flutter run -d chrome

# macOS desktop
flutter run -d macos

# List available devices
flutter devices
```

### App-specific notes

| App | Best platforms | Notes |
|-----|---------------|-------|
| **back_office** | macOS, Web | Desktop-first management app |
| **docuflow** | macOS, Web | Document processing with OCR |
| **driver_pro** | Android, iOS | Mobile-first driver app |
| **fleet_planner** | macOS, Web | Route optimization dashboard |
| **supply_now** | Web, Android | B2B supply management |

---

## Development Workflow

### Hot Reload

While the app is running, press:

- **`r`** — Hot reload (fast, keeps state)
- **`R`** — Hot restart (full restart)
- **`q`** — Quit

### Code Generation (watch mode)

Auto-regenerate code when files change:

```bash
cd apps/<app_name>
dart run build_runner watch --delete-conflicting-outputs
```

### Running Tests

```bash
# All tests for an app
cd apps/<app_name>
flutter test

# Specific test file
flutter test test/features/auth/auth_test.dart

# With coverage
flutter test --coverage
```

### Code Quality

```bash
# Static analysis
flutter analyze

# Format code
dart format .
```

---

## Troubleshooting

### "Waiting for another flutter command to release the startup lock"

```bash
killall -9 dart
flutter pub get
```

### pub get fails with dependency errors

```bash
# Clear cache and retry
flutter pub cache repair
rm pubspec.lock
flutter pub get
```

### iOS build fails

```bash
cd ios
rm -rf Pods Podfile.lock
pod install
cd ..
flutter run
```

### Android Gradle build fails

```bash
cd android
./gradlew clean
cd ..
flutter pub get
flutter run
```

### 401 Unauthorized errors at runtime

The backend is not running or the auth token expired. Make sure Django is running at `http://localhost:8000` and try logging in again.
