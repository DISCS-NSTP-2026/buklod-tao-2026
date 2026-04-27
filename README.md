# NSTP Mapping Portal (DISCS x OSCI): Buklod Tao + SEEDS

## Project Status
This repository is a **static frontend web app** (HTML/CSS/JS) using Firebase Auth + Cloud Firestore directly from the browser.  
There is **no custom backend server** in this codebase.

This handoff should be documented as:

> **Local Execution / GitHub Handoff / Optional Static Hosting**

Do **not** describe this repo as having production deployment infrastructure.

---

## 1) Overview

This project contains:
- A portal/front entry page: [index.html](index.html)
- Buklod Tao mapping app: [app_buklod-tao/index.html](app_buklod-tao/index.html)
- SEEDS mapping app: [app_seeds/index.html](app_seeds/index.html)
- Shared Firebase/data utilities: [js/firestore_UNIV.js](js/firestore_UNIV.js)
- Shared auth utilities: [js/auth.js](js/auth.js)
- Shared map/utilities: [js/index_UNIV.js](js/index_UNIV.js)
- Filter rule definitions: [js/ruleEngines.js](js/ruleEngines.js)
- Shared search component: [components/SearchBar.js](components/SearchBar.js)

---

## 2) Architecture (Actual Runtime)

### Frontend-only flow
1. Browser loads static pages and JS modules.
2. Firebase config is loaded from `js/secrets.json`.
3. Auth is handled in [`auth.signIn`](js/auth.js) / [`auth.signOutUser`](js/auth.js).
4. Firestore calls are made directly from browser code via [`firestore_UNIV.setCollection`](js/firestore_UNIV.js), [`firestore_UNIV.getCollection`](js/firestore_UNIV.js), [`firestore_UNIV.addEntry`](js/firestore_UNIV.js), [`firestore_UNIV.editEntry`](js/firestore_UNIV.js), etc.
5. Map rendering uses Leaflet + OpenStreetMap tile endpoints.

### Important constraints
- No Node backend API in this repo.
- No custom server-side access layer.
- Security and data permissions depend on Firebase Auth + Firestore Rules configuration in Firebase.

---

## 3) Prerequisites

- Modern browser (Chrome/Edge/Firefox)
- A local static web server (required)
  - VS Code Live Server **or**
  - `python -m http.server 5500` **or**
  - equivalent static host
- Access to Firebase project credentials (from project lead / Firebase owner)
- Firebase project with:
  - Authentication enabled
  - Cloud Firestore enabled
  - correct Firestore rules deployed

---

## 4) Secrets Setup (`js/secrets.json`)

The app fetches Firebase config at runtime from:
- [js/firestore_UNIV.js](js/firestore_UNIV.js) (`/js/secrets.json`)
- [js/auth.js](js/auth.js) (`../js/secrets.json`)

### Required file
Create:

- `js/secrets.json` (real credentials, private; not committed)
- `js/secrets.json.example` (sanitized placeholder template)

`js/secrets.json` **must be obtained privately** from the project lead/Firebase owner.

`js/secrets.json.example` is a **sanitized placeholder template** for setup guidance only.

### Security note
Real `js/secrets.json` is intentionally not committed to Git.

---

## 5) Local Run Instructions

From repository root, run a static server and open via localhost:

- VS Code Live Server (recommended), or
- `python -m http.server 5500` then open `http://localhost:5500`

### Do not use `file://`
This project uses ES modules and `fetch()` for secrets, so opening HTML directly via `file://` will break runtime behavior.

---

## 6) Firebase / Auth / Firestore Requirements

- Firebase web app credentials must match the target project.
- Firestore collections must exist and contain expected fields.
- Firestore security rules must permit intended reads/writes for authenticated users.
- Auth users/roles must be provisioned before use.

---

## 7) Current Firestore Collections (from code)

Defined in [js/firestore_UNIV.js](js/firestore_UNIV.js), `DB_RULES_AND_DATA`:

- `buklod-official`
- `buklod-official-TEST`
- `seeds-official`
- `seeds-official-TEST`

---

## 8) Collection Switching Notes

Collection context is set through [`firestore_UNIV.setCollection`](js/firestore_UNIV.js).

Current app-level usage:
- Buklod app uses `buklod-official` in [app_buklod-tao/js/firestore.js](app_buklod-tao/js/firestore.js)
- SEEDS app map logic uses `seeds-official` in [app_seeds/js/firestore.js](app_seeds/js/firestore.js)
- SEEDS add form currently sets `seeds-official-TEST` in [app_seeds/html/addloc.html](app_seeds/html/addloc.html)

⚠ This is a mixed prod/test setup and should be reviewed before formal launch.

---

## 9) Troubleshooting

### App does not load Firebase data
- Check `js/secrets.json` exists and is valid JSON.
- Verify browser console for `fetch`/module errors.
- Confirm the app is opened via `http://localhost...`, not `file://`.

### Auth problems
- Verify Firebase Auth is enabled and user exists.
- Check console logs in [js/auth.js](js/auth.js).

### Firestore read/write failures
- Check Firestore Rules in Firebase console.
- Confirm selected collection is correct.
- Validate payload with [`firestore_UNIV.validateData`](js/firestore_UNIV.js).

### Data appears in wrong environment
- Confirm collection target (`*-official` vs `*-official-TEST`) in:
  - [app_buklod-tao/js/firestore.js](app_buklod-tao/js/firestore.js)
  - [app_seeds/js/firestore.js](app_seeds/js/firestore.js)
  - [app_seeds/html/addloc.html](app_seeds/html/addloc.html)

---

## 10) Handoff Notes

- This repo is currently suitable for **local execution and organizational handoff**.
- Optional static hosting can be added later (manually), but is not codified here.
- There is currently **no declared production deployment config** in this README:
  - no CI/CD pipeline
  - no Docker setup
  - no `firebase.json`-based hosting workflow documented
  - no custom backend service
- Before any production claim, complete:
  1. Firestore security rules documentation
  2. environment/config management documentation
  3. explicit deploy process + rollback procedure
  4. test/prod collection strategy cleanup