# Tindahan POS — Build a Real Offline APK

This project bundles the entire app (HTML/CSS/JS + the QR scanner library)
directly into the Android app's files. There is no live URL involved at
runtime — no browser redirect, no dependency on cached pages. It just runs.

There are two ways to turn this into a real `.apk`. **Option A needs
nothing installed on your computer.** Option B is for if you already have
Android Studio and want to build locally.

---

## Option A — Build it in the cloud with GitHub Actions (recommended, zero installs)

You only need a free GitHub account. No Node, no Android Studio, no SDKs on
your machine — GitHub's servers do the actual build.

### 1. Create a new repository
Go to **github.com** → **New repository** → name it something like
`tindahan-pos` → keep it **Public** (Public repos get unlimited free
Actions minutes; Private also works, just with a monthly minute cap) →
Create repository.

### 2. Upload these files
On the new repo's page, click **"Add file" → "Upload files"**, then drag
in **everything from this folder** (`www/`, `.github/`, `package.json`,
`capacitor.config.json` — the whole `capacitor-project` folder contents,
keeping the same structure). Commit directly to the `main` branch.

> Important: make sure the `.github/workflows/build-apk.yml` file actually
> lands at that exact path in your repo — GitHub's drag-and-drop upload
> preserves folder structure, but double check the `.github` folder isn't
> silently skipped (it sometimes is, since it starts with a dot — if so,
> create the file manually via "Add file → Create new file" and paste its
> contents in, using the same path).

### 3. Let it build
As soon as you commit, GitHub Actions kicks off automatically (you can also
trigger it manually from the **Actions** tab → your workflow →
**"Run workflow"**). It will:
- Add the Android platform
- Copy your web app into it
- Add the camera permission
- Compile a real, installable `.apk`

This takes about 3–5 minutes. Watch progress under the **Actions** tab.

### 4. Download your APK
Once the run finishes with a green checkmark, open that run, scroll to
**Artifacts**, and download `tindahan-pos-debug-apk`. Unzip it — inside is
`app-debug.apk`.

### 5. Install it on your phone
Transfer the `.apk` to your Android phone any way you like (Google Drive,
email, USB, Telegram to yourself). Tap it to install — Android will ask you
to allow installs from that source once. Then open the app from your home
screen.

### 6. Confirm it's truly offline
Turn on Airplane Mode, open the app from the home screen icon (not a
browser), and use it normally — checkout, inventory, and camera scanning
should all work exactly the same with zero connectivity.

---

## Option B — Build locally with Android Studio

If you'd rather build on your own machine (useful if you want to keep
iterating on the app quickly):

```bash
npm install @capacitor/core @capacitor/android
npx cap add android
npx cap sync android
```

Then open `android/app/src/main/AndroidManifest.xml` and add, inside
`<manifest>`:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" android:required="false" />
```

Then:

```bash
npx cap open android
```

In Android Studio: **Build > Build App Bundle(s)/APK(s) > Build APK(s)**.
Find the result at `android/app/build/outputs/apk/debug/app-debug.apk`.

---

## Publishing to the Play Store later

Both options above produce a **debug** APK, fine for sideloading but not
for the Play Store. To publish, you'd generate a **signed release build**
(`./gradlew assembleRelease` with a signing key, or Android Studio's
"Generate Signed Bundle" wizard) — happy to walk through that when you're
ready to publish.
