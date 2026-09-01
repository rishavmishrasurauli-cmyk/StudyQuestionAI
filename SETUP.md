# Setup: from this folder to a Play Store listing

This app becomes a real Android app using a **Trusted Web Activity (TWA)** — Google's official,
Play-Store-approved way to wrap a hosted website/PWA in a native Android shell with no browser
address bar. This is the same technique used by many published Play Store apps.

There are three stages. Stages 1 and 2 you do once; stage 3 you repeat for every update.

---

## Stage 1 — Host the PWA on HTTPS

The Android app loads your site live, so it must be hosted on a real HTTPS domain (not
`localhost`). Any static host works, for example:

- **Firebase Hosting** (free tier, simple CLI) — `npm install -g firebase-tools`, `firebase init hosting`,
  point the public directory at this `pwa/` folder, then `firebase deploy`.
- **Netlify** — drag-and-drop the `pwa/` folder in the Netlify dashboard, or `netlify deploy --prod`.
- **GitHub Pages** — push `pwa/` to a repo and enable Pages on the `main` branch.

After deploying you should be able to open:
- `https://YOUR_DOMAIN/index.html` — the app
- `https://YOUR_DOMAIN/manifest.json`
- `https://YOUR_DOMAIN/privacy-policy.html`

Keep this URL — you'll need it below.

---

## Stage 2 — Generate the Android project with Bubblewrap (recommended)

Bubblewrap is Google's official CLI that reads your `manifest.json` and produces a correct,
buildable Android Studio project automatically (keystore generation included). This is faster and
less error-prone than hand-editing the `android-twa/` reference files in this folder.

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest="https://YOUR_DOMAIN/manifest.json"
```

The wizard will ask for:
- **Package name** — e.g. `com.yourcompany.studyquestionai` (see PLAY_STORE_RELEASE.md — choose
  carefully, this cannot change after publishing)
- **App name**, **display mode** (`standalone`), **status bar color** — it will suggest sensible
  defaults pulled straight from `manifest.json`
- Whether to create a new signing key — say yes if you don't have one yet; **back up the generated
  `.keystore` file and its passwords somewhere safe**, you cannot recover a lost key

Then build:

```bash
bubblewrap build
```

This produces `app-release-signed.apk` and, more importantly for Play Store, you can generate the
App Bundle with:

```bash
bubblewrap build --release  # or open the generated project in Android Studio and run
                              # ./gradlew bundleRelease
```

The `android-twa/` folder in this deliverable mirrors what Bubblewrap generates, so you can read it
to understand what's happening, or hand-edit it directly instead of using Bubblewrap if you prefer
full manual control — just remember to keep `compileSdk`/`targetSdk` at 36+ and the `applicationId`
consistent everywhere.

---

## Stage 3 — Digital Asset Links (removes the browser address bar)

For the Android app to open your site **without** a Chrome address bar, you must prove you own both
the domain and the app. This uses a file hosted on your domain plus your app's signing certificate.

1. Get your release keystore's SHA-256 fingerprint:
   ```bash
   keytool -list -v -keystore your-release-key.keystore -alias your-key-alias
   ```
   Copy the `SHA256:` value (remove the colons, or keep them — either format is accepted).

2. Fill in `android-twa/assetlinks-template.json` with your real `package_name` and
   `sha256_cert_fingerprints`, then host it at:
   ```
   https://YOUR_DOMAIN/.well-known/assetlinks.json
   ```
   (Bubblewrap can also generate/validate this file for you: `bubblewrap validate`.)

3. Update every `YOUR_DOMAIN` placeholder in `android-twa/app/src/main/AndroidManifest.xml` and
   `strings.xml` to your real domain, and the `applicationId`/`namespace` in `app/build.gradle` to
   your chosen package name.

4. Reinstall the app and confirm no address bar appears at the top.

---

## Stage 4 — Build the signed release bundle

Whether you used Bubblewrap or the manual `android-twa/` project:

```bash
cd android-twa       # or the Bubblewrap-generated folder
./gradlew bundleRelease
```

The signed `.aab` appears under `app/build/outputs/bundle/release/`. This is the file you upload to
Play Console. Never commit the keystore file or its passwords to Git — keep them outside the repo
and pass them via environment variables (the reference `app/build.gradle` already reads
`SQAI_KEYSTORE_PATH`, `SQAI_KEYSTORE_PASSWORD`, `SQAI_KEY_ALIAS`, `SQAI_KEY_PASSWORD`).

---

## Connecting a real AI backend later

Right now `index.html` generates questions locally with templates (Mock Mode) — no server needed.
When you're ready to use a real AI provider:

1. Build a small backend (Cloud Function, or any server) that accepts
   `{ board, classLevel, subject, topic, language, difficulty, qtype, count }`, calls your AI
   provider with a strict-JSON system prompt, validates the response, and returns questions in the
   same shape the app already uses.
2. In `index.html`, replace the body of `generateQuestions()` with a `fetch()` call to that backend.
3. Keep your AI provider's API key only on the server — never in this HTML file, since anything in
   the client is visible to anyone who opens dev tools.
4. Add rate limiting and request validation on the backend to control cost.

See `README.md` for the exact JSON shape each question object should use.
