# Lewden Support Hub — setup & deployment

Everything runs as static files (no build step). You'll do this once: create a Supabase
project, run the schema, put the files on GitHub Pages, then connect and sign in.

Files in this bundle:

```
index.html              ← the app (must keep this exact name for GitHub Pages)
schema.sql              ← run once in Supabase to create tables + load all 648 chargers
manifest.webmanifest    ← makes it installable on a phone
service-worker.js       ← offline app shell
icons/                  ← app icons (icon-192, icon-512, apple-touch-icon, favicon)
SETUP.md                ← this file
```

---

## 1. Create the Supabase project

1. Go to supabase.com → **New project**.
2. Name it (e.g. `lewden-support-hub`), set a database password, choose region
   **West EU (London)**, and create it. Give it a minute to provision.

## 2. Build the database

1. In the project, open **SQL Editor → New query**.
2. Open `schema.sql`, copy **all** of it, paste into the editor, and click **Run**.
   It creates the 13 tables, security rules, and loads the 22 error codes plus all
   648 chargers. It's safe to re-run if needed (it won't duplicate data).

## 3. Turn on email logins and add your team

1. **Authentication → Providers → Email**: make sure it's enabled.
2. For an internal tool, the simplest path is to skip email confirmation:
   **Authentication → Providers → Email → "Confirm email"** → turn **off**.
   *(Optional — only do this for a trusted internal team.)*
3. Add each engineer: **Authentication → Users → Add user → Create new user**.
   Enter their email and a password, and tick "Auto Confirm User". Do this for
   yourself and anyone else who needs access. A profile is created automatically on
   first sign-in, so their name shows up on tickets and visits.

> Everyone you add shares the same tickets / customers / chargers / visits queue.
> Tasks, notes, and personal appearance settings stay private to each account.

## 4. Get your connection keys

1. **Project Settings → API.**
2. Copy two values, you'll paste them into the app on first run:
   - **Project URL** — looks like `https://abcdefgh.supabase.co`
   - **anon public** key — the long `eyJ…` string (the *public* one, **not** `service_role`).

The anon key is safe to use in a browser app; the database rules (RLS) only allow
signed-in users to read or write.

## 5. Put it on GitHub Pages

1. Create a new GitHub repo (e.g. `lewden-support-hub`).
2. Upload **all** the files, keeping the folder layout — `index.html`,
   `manifest.webmanifest`, `service-worker.js`, and the `icons/` folder, at the repo
   root. (Use **Add file → Upload files** in the web UI; you can drag the `icons`
   folder in.)
3. **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   pick `main` and `/ (root)`, **Save**.
4. Wait ~1 minute, then open the URL it gives you
   (`https://<you>.github.io/lewden-support-hub/`).

## 6. Connect and sign in

1. On first open you'll see **Connect your database**. Paste the Project URL and the
   anon key from step 4, then **Connect**. (These are stored only in your browser.)
2. Sign in with the email/password you created in step 3.
3. You're in — the full charger database and shared queue load automatically.

## 7. Install on your phone

1. Open the Pages URL on your phone.
2. **iPhone (Safari):** Share → **Add to Home Screen**.
   **Android (Chrome):** menu → **Install app / Add to Home screen**.
3. It launches full-screen like a native app, with the EV-teal icon.

---

### Notes

- **Each device connects once.** The URL + anon key are saved per browser. To switch
  or clear it, use **Settings → Account & data → Disconnect**, or **Sign out** to just
  log out.
- **Updating the app later:** edit `index.html` in the repo (or upload a new one).
  Pages redeploys automatically. If a phone shows a stale version, close and reopen the
  installed app, or bump `CACHE` in `service-worker.js` (e.g. `lewden-hub-v2`).
- **Adding chargers/customers later** happens inside the app; you don't touch SQL again.
  If you ever want to bulk-load more chargers, you can paste extra `insert into chargers …`
  rows in the SQL editor.
- **Backups:** Supabase keeps automatic backups; you can also export any table as CSV
  from the Table editor.
