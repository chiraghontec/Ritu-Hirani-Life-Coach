# Ritu Hirani | Life Coach — Ops Dashboard

A single-file HTML operations dashboard for managing clients, sessions, scheduling, and tasks for **Ritu Hirani | Life Coach**. Built for Chirag (Operations Manager) and Ritu to monitor the coaching practice in one place.

---

## Features

- **Today view** — tasks due today, session blocks (3–5 PM & 6–8 PM), and auto-generated pending actions
- **Weekly schedule** — visual grid (Tue–Sun) with editable session slots; navigate forward/back by week
- **Client pipeline** — 5-stage kanban (Lead → Consultation → Enrolled → Active → Offboarded) with payment badges and enrollment checklist progress
- **Session tracker** — per-client progress bars, package expiry countdown, and one-click session completion
- **Google Tasks sync** — connects to your existing Google Task lists via OAuth 2.0
- All data stored in browser `localStorage` — no backend required
- Fully hosted as a static site on GitHub Pages

---

## Hosting on GitHub Pages

### Step 1 — Create a GitHub repository

1. Go to [github.com](https://github.com) and sign in
2. Click **New repository**
3. Name it something like `ritu-ops-dashboard`
4. Set it to **Private** (recommended — this contains client data logic)
5. Click **Create repository**

### Step 2 — Upload the dashboard file

1. In your new repo, click **Add file → Upload files**
2. Upload `ritu-hirani-ops-dashboard.html`
3. **Rename it to `index.html`** before uploading (or rename after via the GitHub interface)
4. Commit the file with a message like `Initial dashboard upload`

### Step 3 — Enable GitHub Pages

1. Go to your repo **Settings**
2. Scroll to **Pages** in the left sidebar
3. Under **Source**, select `Deploy from a branch`
4. Set branch to `main` and folder to `/ (root)`
5. Click **Save**
6. Wait 1–2 minutes — your dashboard will be live at:

```
https://your-username.github.io/ritu-ops-dashboard/
```

GitHub will show you the exact URL in the Pages settings once it's deployed.

---

## Setting Up Google Tasks Sync

Google Tasks sync requires a one-time setup in Google Cloud Console. This gives the dashboard permission to read your task lists.

### Step 1 — Create a Google Cloud project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click **Select a project → New Project**
3. Name it `Ritu Ops Dashboard` and click **Create**

### Step 2 — Enable the Google Tasks API

1. In the left menu go to **APIs & Services → Library**
2. Search for **Google Tasks API**
3. Click on it and click **Enable**

### Step 3 — Create OAuth 2.0 credentials

1. Go to **APIs & Services → Credentials**
2. Click **Create Credentials → OAuth client ID**
3. If prompted, configure the **OAuth consent screen** first:
   - User type: **External**
   - App name: `Ritu Ops Dashboard`
   - Add your email as a test user
4. Back in Credentials → Create OAuth client ID:
   - Application type: **Web application**
   - Name: `Ritu Ops Dashboard`
   - Under **Authorised JavaScript origins**, add your GitHub Pages URL:
     ```
     https://your-username.github.io
     ```
   - Click **Create**
5. Copy the **Client ID** (looks like `123456789-xxx.apps.googleusercontent.com`)

### Step 4 — Connect in the dashboard

1. Open your dashboard URL
2. Click **Google Tasks** in the left sidebar
3. Paste your Client ID into the input field
4. Click **Connect Google Tasks**
5. A Google sign-in popup will appear — sign in and allow access
6. Your task lists will sync automatically

> **Note:** Google Tasks sync only works when the dashboard is accessed via the hosted URL (not a local file). The authorised origin you added must match exactly.

---

## Using the Dashboard

### Adding clients
Click **+ Add Client** in the top right. Fill in name, package, stage, and payment status. Package end date is calculated automatically from the start date.

### Managing the weekly schedule
Go to **This Week** and click any slot to assign a session, consultation, content block, or admin time. Session blocks at 3–5 PM and 6–8 PM are pre-highlighted.

### Tracking enrollment
Click any client card in the **Clients** view to open their detail panel. Tick off each of the 7 enrollment steps as they are completed.

### Marking sessions done
In the **Sessions** view, click **+ Session** next to any active client to increment their completed session count.

### Syncing Google Tasks
Click **↻ Sync Google Tasks** in the sidebar footer at any time to pull the latest tasks from Google.

---

## Updating the Dashboard

To deploy an updated version:

1. Edit `ritu-hirani-ops-dashboard.html` locally
2. Go to your GitHub repo
3. Click on `index.html` → **Edit (pencil icon)**
4. Paste the updated file content
5. Commit the change — GitHub Pages redeploys automatically within 1–2 minutes

Alternatively, use the [GitHub Desktop app](https://desktop.github.com/) to manage files locally and push changes.

---

## Data & Privacy

- All client data is stored in your **browser's localStorage** on the device you use to access the dashboard
- Data does **not** sync between devices automatically
- To back up data: open browser DevTools → Application → Local Storage → copy the `rh_ops` key value
- To restore: paste it back into the same key on a new device

> **Recommendation:** Use the dashboard consistently from one primary device (e.g. your laptop). If you need multi-device access, a backend database would be required — raise this when the trial period is complete.

---

## Files in This Repository

| File | Description |
|------|-------------|
| `index.html` | The ops dashboard (renamed from `ritu-hirani-ops-dashboard.html`) |
| `README.md` | This file |

---

## Support

Built and maintained by **Chirag, Operations Manager — Ritu Hirani | Life Coach**  
Contact: chiragds0117@gmail.com
