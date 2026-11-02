# JOHAN AI — Android Studio project

JOHAN AI is a native Kotlin/Jetpack Compose assistant starter project (package `com.johanai.assistant`, min Android 8 / API 26). It includes Gemini chat, local Room chat history, DataStore configuration, speech recognition/TTS, app launching and YouTube/Google search intents, a coding prompt screen, and an opt-in AccessibilityService declaration.

## Important build note
This project has been generated and structurally reviewed, but **has not been compiled in this environment** because an Android SDK/Gradle build environment is not available here. Resolve any SDK or dependency issues reported by your local Gradle build before relying on it. No wrapper JAR is bundled; use the system Gradle installed in Termux (the included `gradlew` script delegates to it).

## Requirements
- JDK 17 (required by Android Gradle Plugin 8.7.3)
- Gradle 8.9
- Android SDK Platform 35 and Build Tools 35.x
- Android SDK command-line tools and platform-tools
- Internet connection for Gradle dependencies and Gemini requests

## Build in Termux (Android phone)
Termux package availability varies by device/repository. Install a JDK and Gradle:
```sh
pkg update && pkg upgrade
pkg install openjdk-17 gradle unzip wget
java -version
gradle --version
```
Install Android command-line tools and SDK components using Google's official command-line tools. The SDK is not included in this ZIP. If you have an SDK directory, set:
```sh
export ANDROID_HOME="$HOME/android-sdk"
export ANDROID_SDK_ROOT="$ANDROID_HOME"
export PATH="$PATH:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools"
```
Install required SDK packages (after placing `sdkmanager` in the path):
```sh
sdkmanager --sdk_root="$ANDROID_HOME" "platform-tools" "platforms;android-35" "build-tools;35.0.0"
yes | sdkmanager --licenses
```
Create `local.properties` in the project root:
```properties
sdk.dir=/data/data/com.termux/files/home/android-sdk
```
Change that path to your actual SDK location.

Extract and build:
```sh
unzip JOHAN_AI_Android_Project.zip
cd JOHAN_AI_Android_Project
chmod +x gradlew
./gradlew assembleDebug
```
Expected APK:
`app/build/outputs/apk/debug/app-debug.apk`

Install with ADB (USB debugging / wireless debugging configured):
```sh
adb install -r app/build/outputs/apk/debug/app-debug.apk
```
Or copy the APK to Downloads and open it in Android's package installer.

### Termux caveat
Building modern Android apps directly on-device is not officially supported by every Android SDK/Gradle component. Some SDK tools may require a Linux host or emulation and can fail on Android/ARM. If SDK tools fail in Termux, build the same project on a Linux/Windows/macOS computer or cloud CI. Gradle alone does not install the Android SDK.

## Configure Gemini
1. Install and open JOHAN AI.
2. Open Settings.
3. Add your Gemini API key and model (default `gemini-2.0-flash`).
4. Save, then choose Test Gemini connection.
5. Chat from the Chat tab.

Gemini API key: obtain one from Google AI Studio. Never commit it to source control. This app stores it in local DataStore preferences, which are not encrypted secret storage. A rooted device, backup, or compromised app can expose it. For production, put Gemini calls behind a server you control.

## Feature notes / Android restrictions
- Speech recognition uses Android's installed recognition service and asks for microphone permission. Language availability depends on the device's speech provider. TTS uses an India-English locale where available; voice gender depends on installed engine voices and device settings.
- There is no always-on wake word. Android background microphone restrictions and battery policies make a reliable always-listening wake word unsuitable for this basic app.
- App launching searches common package IDs and then installed app labels. Android package visibility policies vary by OS and distribution channel.
- Accessibility is opt-in and must be enabled by the user in system settings. The included service offers limited accessible-node helper actions; it does not continuously scrape screens, bypass security, or automate arbitrary apps. It is not yet wired to expose a general remote-control UI.
- YouTube playback opens YouTube search results; playback and availability are controlled by YouTube.
- Weather requires a weather provider/API and is not included. Reminders, notes, calculator, flashlight, battery status, and broad device automation are not included in this first build; they are not represented as working controls in the UI.
- The app requires internet for Gemini. Chat history is stored locally in Room. Clear history removes saved chat messages.

## Architecture
`ui/` Compose screens and ViewModel · `data/` Room, DataStore and Gemini HTTP repository · `voice/` Android speech/TTS · `accessibility/` opt-in service · `util/` Android intents.
