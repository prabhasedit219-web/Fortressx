# Fortress X

Android security review app. Kotlin, Jetpack Compose (Material 3), MVVM + Clean-style layering, Hilt, Room, DataStore, Coroutines/StateFlow, VpnService, Android Keystore. Min SDK 29 (Android 10), target SDK 35.

## Build
1. Open the folder in Android Studio (Ladybug or newer, JDK 17). Let it sync; it creates the Gradle wrapper (Gradle 8.9).
2. Run the `app` configuration, or `./gradlew assembleDebug` / `./gradlew testDebugUnitTest`.

## Modules
| Package | What it does |
|---|---|
| `scanner` | Install source via PackageManager (`getInstallSourceInfo` on API 30+), system vs user apps, sideload classification |
| `permissions` | Granted/requested sensitive permissions, per-category exposure, deep links to Android App Settings |
| `firewall` | `VpnService` local DNS filter: only the virtual DNS IP is routed in, blocked names get NXDOMAIN, others are forwarded via `protect()`ed sockets. Built-in categories, custom block/allow, list import, stats in Room, blocked-domain log encrypted with a Keystore AES-GCM key |
| `privacy` | Camera availability callback, audio-recording callback, location-services state; alerts and history |
| `security` | `RiskEngine` (explainable permission/combination heuristics), `SecurityEngine` (findings + recommended actions), `DeviceChecks` (screen lock, patch age, ADB, accessibility services, notification listeners, device admins, Private DNS) |
| `dashboard` | Findings, firewall status, privacy status, sideload and permission summaries |

## Honest limits (Android sandbox)
- No file-level malware scanning, no root, no changing other apps' permissions (links to Settings instead).
- Android does not reveal which app uses the camera/mic; the monitor reports that they are in use, and points to the system Privacy dashboard.
- Firewall filters DNS only. No HTTPS interception. Apps using hardcoded IPs, own DoH, or strict Private DNS can bypass it.
- `QUERY_ALL_PACKAGES` is required to audit all apps; Google Play requires a policy declaration for it.
