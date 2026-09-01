# StudyQuestion AI

A bilingual (English / Hindi / Hindi+English) practice-question app for students. Enter any topic
and generate MCQs, short/long answers, true-false, fill-in-the-blank or numerical questions, then
study them in Results, Quiz or Practice mode.

## What's in this folder

```
pwa/
  index.html            The entire app (React, no build step required)
  manifest.json          PWA manifest — name, icons, colors, install behavior
  service-worker.js       Offline app-shell caching
  privacy-policy.html     Hosted privacy policy page (required by Google Play)
  icons/                  App icons (192, 512, maskable 512, favicon)
  android-twa/            Android wrapper project (Trusted Web Activity)
    build.gradle, settings.gradle, gradle.properties
    app/build.gradle
    app/src/main/AndroidManifest.xml
    app/src/main/res/values/strings.xml, colors.xml
    assetlinks-template.json
README.md                 This file
SETUP.md                  Hosting + Android build + Play Store steps
PLAY_STORE_RELEASE.md      Pre-publish checklist
```

## How the app currently generates questions

`index.html` includes a self-contained **mock question generator** (see the `generateQuestions()`
function). It builds plausible English/Hindi/bilingual MCQ, true-false, fill-in-the-blank, short,
long and numerical questions from templates, with session-level duplicate detection — no AI API key
or backend required. This lets you test and publish a working v1 immediately.

To swap in a real AI backend later: replace the body of `generateQuestions()` with a `fetch()` call
to your own secure backend (Cloud Function / server) that calls an AI provider and returns questions
in the same JSON shape already used throughout the app (`{ id, type, difficulty, questionEnglish,
questionHindi, options?, correctAnswer?, answerEnglish, answerHindi, explanationEnglish,
explanationHindi }`). Never call an AI API directly from the client with an embedded API key.

## Running it locally

No build tools needed for the web app itself:

```bash
cd pwa
python3 -m http.server 8080
# open http://localhost:8080/index.html
```

(A plain static file server is enough — any will do.)

## Turning it into an installable Android app

See **SETUP.md** for the full path: hosting the PWA on HTTPS, generating the Android project with
Bubblewrap, verifying Digital Asset Links, and producing a signed `.aab` for Google Play.

## Data & privacy

No login required. History, Saved questions, and Settings are stored in the browser's `localStorage`
on the user's own device. See `privacy-policy.html` for the full policy text — host this file and
link it from the Play Console before submitting for review.
