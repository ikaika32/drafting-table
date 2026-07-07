# Drafting Table — Idea & Punch List Tracker

A standalone IK Corp app for capturing project ideas and tracking the
pieces you need to add or adjust on projects you're already building.

## Files

- `index.html` — the whole app (UI, logic, Supabase Auth gate)
- `manifest.json` — PWA manifest
- `service-worker.js` — offline app-shell caching
- `icon-192.png`, `icon-512.png` — home screen icons

## Setup

### 1. Supabase anon key — already set
The real anon key for your shared Supabase project is already baked into
`index.html`. Nothing to do here.

### 2. The `app_data` table
This app reads/writes two rows in your shared `app_data` table:

- `app_name = 'ideastracker'`, `key = 'items'`
- `app_name = 'ideastracker'`, `key = 'projects'`

Schema (already exists in your project, reused as-is):

```sql
create table app_data (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null default auth.uid() references auth.users(id),
  app_name text not null,
  key text not null,
  value jsonb not null,
  updated_at timestamptz not null default now(),
  unique (user_id, app_name, key)
);
```

Row Level Security policy: owners can fully access rows where
`auth.uid() = user_id`. Since `user_id` defaults to `auth.uid()`, you
don't need to set it manually — it's handled automatically once you're
signed in.

### 3. Sign in
This app reuses your existing IK Corp login — no new Supabase user
needed. Just sign in with your usual email/password once it's deployed.
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
