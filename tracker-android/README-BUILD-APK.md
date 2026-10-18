# TRACKER — building your installable Android APK

## What's in here
- `www/index.html` — your TRACKER app, unchanged in data/logic, with Android-readiness added (safe-area padding for the status bar, no bounce/overscroll, hardware back-button support, native status-bar color sync, splash-screen hide call). All of this code is guarded so it's a no-op if you ever open the file in a normal browser.
- `capacitor.config.json`, `package.json` — the Capacitor project config.
- `android-project/` — the custom icon, splash screen, and dark theme resources to drop into the generated Android project (see step 3).
- `assets/` — the source icon/splash PNGs at full resolution, in case you want to tweak them later.

## Why this isn't already a finished .apk
Building an APK requires the Android SDK, Gradle, and a live connection to npm/Google's Maven repositories to download Capacitor's Android runtime — none of which are available in this sandboxed environment (it has no internet access). Everything above is fully prepared; you just need to run the standard Capacitor build on your own machine, which takes about 10 minutes.

## What you need on your computer
- [Node.js](https://nodejs.org) (v18+)
- [Android Studio](https://developer.android.com/studio) (installs the Android SDK + a bundled JDK)

## Steps

**1. Unzip the project and install Capacitor**
```
cd tracker-android
npm install
```

**2. Add the Android platform**
```
npx cap add android
```
This generates the full `android/` folder (Gradle wrapper, `AndroidManifest.xml`, `MainActivity.java`, default theme) from Capacitor's official template.

**3. Copy in the custom icon, splash screen, and dark theme**
```
cp -r android-project/app/src/main/res/* android/app/src/main/res/
```
This overwrites the default Capacitor icon/splash with TRACKER's launcher icon, splash screen, and dark status-bar/nav-bar theme (`colors.xml`, `styles.xml`).

**4. Sync the web app into the native project**
```
npx cap sync android
```

**5. Build the APK**
- Easiest: open in Android Studio and click Run/Build → Build APK:
  ```
  npx cap open android
  ```
  Then **Build → Build Bundle(s) / APK(s) → Build APK(s)**. The file lands in `android/app/build/outputs/apk/debug/app-debug.apk`.
- Or from the command line:
  ```
  cd android
  ./gradlew assembleDebug
  ```
  Same output path.

**6. Install it on your phone**
Copy `app-debug.apk` to your phone (USB, email, Drive — anything) and open it. You'll need to allow "install unknown apps" for whichever app you use to open it, since it's not from the Play Store. No further setup needed — it works fully offline, same as the original HTML file.

## Notes
- App ID is `com.tracker.nutrition`, app name `TRACKER` — both set in `capacitor.config.json`, change there if you'd like different values.
- This is a debug build (fine for personal installs). If Android Studio ever asks you to sign a release build instead, you can skip that — `assembleDebug` needs no signing key.
- All your nutrition data lives in the WebView's `localStorage`, exactly like it does in the browser today — nothing about the data layer changed.
