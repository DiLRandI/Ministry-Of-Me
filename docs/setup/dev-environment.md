# Development Environment

## Target Matrix

- Primary desktop target: Ubuntu 22.04 LTS and 24.04 LTS
- Secondary desktop target: Debian 12
- Mobile target: Android API 24+
- Architecture targets for desktop packaging later: x64 first, arm64 later if needed

## Required Core Tooling

- `git`
- `Flutter` stable SDK
- Android Studio
- Android SDK platform tools
- Java runtime required by current Android Studio and Flutter Android tooling

## Linux Desktop Tooling

Flutter's Linux setup docs currently list these Ubuntu packages:

- `clang`
- `cmake`
- `ninja-build`
- `pkg-config`
- `libgtk-3-dev`
- `libstdc++-12-dev`

Suggested Ubuntu install command:

```bash
sudo apt-get install -y clang cmake ninja-build pkg-config libgtk-3-dev libstdc++-12-dev
```

## Android Tooling

Install through Android Studio:

- Android SDK Build-Tools
- Android SDK Command-line Tools
- Android Emulator
- Android SDK Platform-Tools
- CMake
- NDK (Side by side)

Then accept licenses:

```bash
flutter doctor --android-licenses
```

## Flutter Validation

Run these before generating app code:

```bash
flutter doctor
flutter config --enable-linux-desktop
flutter devices
```

Expected result:

- Android toolchain healthy
- Linux desktop support enabled
- at least one Android device or emulator available for mobile testing

## Implementation Defaults

- Keep the first generated Flutter app under `app/`.
- Use the stable Flutter channel unless a package constraint forces otherwise.
- Keep Android min SDK at `24` unless a future package forces a higher floor.
- Defer release packaging decisions until the app can build and sync locally on both platforms.

## First Verification Pass Once Scaffolding Exists

- app builds on Linux desktop in debug mode
- app builds on Android emulator or device in debug mode
- a temporary database can be created in the correct platform data directory
- secure storage works on both platforms
- Google auth can be tested separately from finance features

## Source References

- Flutter supported platforms: <https://docs.flutter.dev/reference/supported-platforms>
- Flutter Linux setup: <https://docs.flutter.dev/platform-integration/linux/setup>
- Flutter Android setup: <https://docs.flutter.dev/platform-integration/android/setup>
