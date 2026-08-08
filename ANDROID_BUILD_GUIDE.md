# Android App Bundle & APK Build Guide for Consista-v6

## Overview
This guide explains how to build an Android App Bundle (.aab) and APK file for the Google Play Store.

## Prerequisites
1. **Java Development Kit (JDK)** - Version 11 or higher
2. **Android SDK** - Install via Android Studio
3. **Node.js & npm** - For dependency management
4. **Git** - Version control

## Option 1: Using Capacitor (Recommended)

Capacitor is the most straightforward way to convert a PWA to a native Android app.

### Installation Steps

#### 1. Create a Capacitor project structure
```bash
# Initialize npm project (if not already done)
npm init -y

# Install Capacitor
npm install @capacitor/core @capacitor/cli

# Initialize Capacitor
npx cap init

# When prompted, enter:
# - App name: Consista
# - App Package (bundle ID): com.consista.app
# - Web dir: . (current directory, since index.html is at root)
```

#### 2. Add Android Platform
```bash
npm install @capacitor/android
npx cap add android
```

This creates an `android/` folder with a complete Android Studio project.

#### 3. Build for Android

**Build the Android App Bundle (.aab):**
```bash
cd android
./gradlew bundleRelease
```

Output location: `android/app/build/outputs/bundle/release/app-release.aab`

**Build the APK file (for testing):**
```bash
cd android
./gradlew assembleRelease
```

Output location: `android/app/build/outputs/apk/release/app-release.apk`

## Option 2: Using Apache Cordova

If you prefer Cordova, follow these steps:

```bash
# Install Cordova globally
npm install -g cordova

# Create project
cordova create consista-app com.consista.app "Consista"
cd consista-app

# Copy your web files
cp ../index.html www/
cp ../sw.js www/
cp ../manifest.json www/
cp ../icon-*.png www/res/

# Add Android platform
cordova platform add android

# Build
cordova build android --release
```

Output files will be in: `platforms/android/build/outputs/`

## Building Debug vs Release

### Debug APK (Testing)
```bash
cd android
./gradlew assembleDebug
```

### Release APK (Google Play Store)
```bash
cd android
./gradlew assembleRelease
```

This requires signing keys (see Signing section below).

## Signing Your App

For Google Play Store submission, you must sign your app with a private key.

### Generate Keystore
```bash
keytool -genkey -v -keystore consista.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias consista

# When prompted, enter your details:
# - Keystore password: [enter strong password]
# - Key password: [same as keystore]
# - Your names, etc.
```

### Configure Signing in Android Studio

Edit `android/app/build.gradle`:

```gradle
signingConfigs {
    release {
        keyAlias 'consista'
        keyPassword 'your_password'
        storeFile file('consista.keystore')
        storePassword 'your_password'
    }
}

buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
```

## Important: Update App Configuration

### Update `capacitor.config.json` (for Capacitor) or `config.xml` (for Cordova)

**Capacitor example:**
```json
{
  "appId": "com.consista.app",
  "appName": "Consista",
  "webDir": ".",
  "server": {
    "androidScheme": "https"
  },
  "plugins": {
    "SplashScreen": {
      "launchAutoHide": true
    }
  }
}
```

## Google Play Store Requirements

Before uploading to Google Play Store:

1. ✅ **Sign the app** with your release keystore
2. ✅ **Update version number** in `android/app/build.gradle`:
   ```gradle
   versionCode 1
   versionName "1.0.0"
   ```

3. ✅ **Create app icon** (192x192 and 512x512 minimum)
   - Icons already available: `icon-192.png` and `icon-512.png`

4. ✅ **Update `manifest.json`** with correct app name and icons

5. ✅ **Test on Android device** before uploading

6. ✅ **Create a Privacy Policy** and Terms of Service URLs

## File Locations

After building:
- **App Bundle (.aab)**: `android/app/build/outputs/bundle/release/app-release.aab`
- **APK (Release)**: `android/app/build/outputs/apk/release/app-release.apk`
- **APK (Debug)**: `android/app/build/outputs/apk/debug/app-debug.apk`

## Next Steps

1. Create a Google Play Developer account ($25 one-time fee)
2. Create a new app listing
3. Upload the `.aab` file to Google Play Console
4. Complete store listing information (screenshots, description, etc.)
5. Submit for review

## Troubleshooting

### Build fails with "Java not found"
- Install JDK 11+
- Set JAVA_HOME environment variable

### Gradle sync issues
- Delete `android/.gradle` folder
- Run `./gradlew clean` in the android folder

### App crashes on startup
- Check `logcat` in Android Studio
- Ensure manifest.json is correctly formatted
- Verify index.html is loading correctly

---

For more help: [Capacitor Docs](https://capacitorjs.com/docs) or [Cordova Docs](https://cordova.apache.org/)
