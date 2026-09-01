# Play Store release checklist

## Before you build

- [ ] Choose your **final package name / applicationId** now (e.g. `com.yourcompany.studyquestionai`).
      This cannot be changed after your first publish without creating a brand-new listing.
- [ ] Update the package name in: Bubblewrap init (or `android-twa/app/build.gradle` `namespace` +
      `applicationId`), `android-twa/assetlinks-template.json`, and your app name in
      `manifest.json` / `strings.xml` if you're renaming from "StudyQuestion AI".
- [ ] Host `index.html`, `manifest.json`, `service-worker.js`, `icons/`, and `privacy-policy.html`
      on your final HTTPS domain (see SETUP.md Stage 1).
- [ ] Publish `.well-known/assetlinks.json` on that same domain (see SETUP.md Stage 3).
- [ ] Generate and safely back up your release keystore — losing it means you can never update
      the app again under the same listing.

## App behavior

- [ ] No login required — confirmed, the app works fully without an account.
- [ ] Only the `INTERNET` permission is requested (already set in `AndroidManifest.xml`).
- [ ] No advertising SDKs, no advertising identifiers collected.
- [ ] History, Saved, and Settings stored locally on-device only (`localStorage`).
- [ ] AI Disclaimer is visible in Settings and shown near the Generate button.
- [ ] "Report Question" available on every generated question.
- [ ] `compileSdk` / `targetSdk` set to 36 or higher.
- [ ] Release build produces a signed `.aab` (not `.apk`) via `./gradlew bundleRelease`.

## Store listing

- [ ] App name, short description, full description written.
- [ ] Feature graphic (1024×500) and at least 2 phone screenshots prepared — take these from the
      running app (light and dark mode look distinct and are good screenshot material).
- [ ] App icon uploaded (512×512) — use `icons/icon-512.png` as your starting point, or replace it
      with your own branding before publishing.
- [ ] Privacy Policy URL set in Play Console → your hosted `privacy-policy.html` URL.
- [ ] Category set to Education.
- [ ] Content rating questionnaire completed honestly (no user-generated public content, no chat,
      no ads, no purchases in this MVP).

## Data Safety section (Play Console)

Answer consistently with what the app actually does:

- [ ] Data collected: none stored on your servers tied to a user identity (education inputs are
      sent to the AI provider only to generate a response — declare this if your backend logs
      requests at all).
- [ ] Data shared with third parties: your AI provider, for the sole purpose of generating
      questions — declare this.
- [ ] Data deleted on request / account deletion: not applicable — no accounts exist.
- [ ] Encryption in transit: yes (HTTPS only, enforced by hosting + TWA).
- [ ] Target audience / Families: review Google's Families Policy requirements if you intend to
      opt into the Families program; this MVP was built conservatively (no ads, no data sale, no
      unnecessary permissions) to make that path realistic, but you must complete the actual
      declarations yourself in Play Console.

## Final checks

- [ ] Uninstall/reinstall the app on a real device and confirm it opens full-screen with **no**
      browser address bar (this confirms Digital Asset Links are working).
- [ ] Test Light, Dark and System theme.
- [ ] Test English, Hindi, and Hindi+English question generation.
- [ ] Test Quiz Mode scoring and Practice Mode navigation.
- [ ] Test History, Saved, and Clear History.
- [ ] Test with the device offline — previously saved sets should still open; a clear offline
      message should appear if you try to generate new questions.
