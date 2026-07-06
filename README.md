# Drafting Table — Idea & Punch List Tracker

A standalone IK Corp app for capturing project ideas and tracking the
pieces you need to add or adjust on projects you're already building.

## Files

- `index.html` — the whole app (UI, logic, Supabase Auth gate)
- `manifest.json` — PWA manifest
- `service-worker.js` — offline app-shell caching
- `icon-192.png`, `icon-512.png` — home screen icons

## Setup

### 1. Add your Supabase anon key
Open `index.html`, find this line near the top of the `<script>` block:

```js
const SUPABASE_ANON_KEY = "PASTE_YOUR_SUPABASE_ANON_KEY_HERE";
```

Replace the placeholder with your real anon key (same one used in your
other IK Corp apps — pull it from another app's file or the Supabase
dashboard under Project Settings → API).

The Supabase URL is already set to your shared project:
`https://nxdycfgkbrmxbdzqeuua.supabase.co`

### 2. Confirm the `app_data` table
This app reads/writes two rows in your existing shared `app_data` table:

- `app_name = 'ideastracker'`, `key = 'items'`
- `app_name = 'ideastracker'`, `key = 'projects'`

It expects columns `app_name`, `key`, `value` (jsonb), with a unique
constraint on `(app_name, key)` so upserts work. If your table schema
is named differently, edit the `.from("app_data")` calls in
`index.html` accordingly (search for `app_data` — there are two spots:
`loadState()` and `doSave()`).

### 3. Confirm Row Level Security
The app signs users in via Supabase Auth (email/password) before
touching the database, so your RLS policy on `app_data` just needs to
allow the `authenticated` role to select/insert/update rows. If your
other apps already have this policy in place, you're set.

### 4. Create your login
Either:
- Add a user manually in Supabase Dashboard → Authentication → Users, or
- Open the app once deployed and use "Need an account? Create one"

### 5. Push to GitHub + Vercel
```bash
git init
git add .
git commit -m "Initial commit: Drafting Table"
gh repo create ikaika32/drafting-table --public --source=. --push
```

Then in Vercel:
- New Project → import the repo
- Framework Preset: **Other**
- No build command, no environment variables needed (all client-side)
- Deploy

### 6. Install on iPhone
Once live on Vercel, open the URL in Safari → Share → Add to Home
Screen. It'll launch full-screen using the manifest and icons above.

## Using the app

- **Idea** = a new project concept, not yet started. Lives in the Inbox
  tab until you promote it into a project or leave it as a note.
- **Punch Item** = something to add or adjust on a project you're
  already building. Lives in that project's punch list, numbered like
  revision tags (Rev. 01, 02...).
- Add new projects either from the capture bar's project dropdown
  ("+ New project…") or from the Projects tab ("+ New Project" card).
