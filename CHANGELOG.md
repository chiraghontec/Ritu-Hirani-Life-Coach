# Ritu Hirani Ops Dashboard — Changelog

Dashboard URL: https://chiraghontec.github.io/Ritu-Hirani-Life-Coach/
Repo: https://github.com/chiraghontec/Ritu-Hirani-Life-Coach

---

## 2026-06-08

### [4c9c222] Two-way Google Tasks sync
**Requested by Chirag**
- **Create:** Tasks added in dashboard POST to Google Tasks API — appear in Google Tasks app immediately; `gid` + `glid` stored on task for future ops
- **Complete/uncomplete:** Toggling a task in dashboard PATCHes `status:'completed'` or `status:'needsAction'` on Google Tasks
- **Delete:** Deleting a task in dashboard DELETEs it from Google Tasks API
- **`fetchGT()` updated:** Now stores `glid` (list ID) on each synced task; backfills `glid` on existing tasks; populates `gtDefaultListId` from real first-list ID on sync (`@default` fallback before first sync)
- New helpers: `gtCreate()`, `gtPatch()`, `gtDelete()`
- Tasks without `gid` (local-only) are unaffected

### [pending] Fix sign-in button not working
**Reported by Chirag**
- **Root cause:** GIS (`accounts.google.com/gsi/client`) was loaded dynamically inside the click handler via `sc.onload` — `requestAccessToken()` called from async callback, outside user gesture call stack → browser silently blocked the OAuth popup
- **Fix:** GIS script now loaded statically in `<head async>` — ready before any user interaction
- `startGoogleSignIn()` simplified: calls `initTokenClient` + `requestAccessToken` directly on click (synchronous user gesture chain preserved)
- `_requestGoogleToken()` (silent re-auth on reload): polls for `google.accounts` readiness at 150ms intervals, 8s timeout — no popup needed so no gesture requirement
- Removed unused `_gisLoaded` flag

---

## 2026-06-06

### [pending] Fix week slots bleeding across weeks (date-keyed schedule)
**Reported by Chirag**
- **Root cause:** `S.schedule` keyed by day-of-week (`'sat'`, `'fri'`) not ISO date — every Saturday showed the same booked slot regardless of which week was displayed
- **Fix:** All schedule reads/writes now use ISO date string (`'2026-06-13'`) as key instead of day-of-week abbreviation
- Affects: week grid render, slot edit modal, save slot, clear slot, today view session blocks
- Also fixed: "Sessions This Week" stat now counts only current week's dates, not all stored slots
- **Note:** Any slots saved before this fix used old keys — those need to be re-entered

### [pending] Fix Google Tasks & Calendar "Not connected" after page reload
**Reported by Chirag**
- **Root cause:** OAuth access tokens expire in ~1hr; stale token was persisted in localStorage, and on reload `checkAuth()` skipped re-auth (sessionStorage had email), leaving `S.gtoken` null
- **Fix 1:** `loadLocal()` now clears `gtoken` on every page load — expired tokens no longer silently break API calls
- **Fix 2:** Added `_requestGoogleToken(cb)` — standalone token-acquisition function reusable for reconnects
- **Fix 3:** `doSync()` (sidebar Sync button) now calls `_requestGoogleToken` instead of redirecting to Setup when no token — click Sync → Google picker → synced
- **Fix 4:** `checkAuth()` auto-requests a fresh token on load when user is already authenticated via sessionStorage
- **Fix 5:** Setup tab now shows a "Connect Google Tasks & Calendar" button when not connected

### [0203a9c] Blocked time slots — dashboard + Google Calendar detection
**Requested by Chirag**
- **New slot type "Personal / Blocked"** — Ritu can select this in the slot editor, enter an event name (e.g. "Coffee with friend"), save; slot turns orange and creates a Google Calendar event (Tangerine color)
- **External calendar detection** — any timed Google Calendar event Ritu creates directly (not via dashboard) that overlaps an office-hours zone (prep/block1/break/block2) is shown as orange 🔒 Blocked in the week grid automatically on sync
- **Conflict warning** — if a session is already scheduled in a slot that has an external calendar block, an orange ⚠ conflict badge appears below it
- **Slot edit modal warning** — opening a slot with an external calendar conflict shows an orange warning message
- **Today view** — both session blocks in Today view also show orange blocked state when relevant
- All-day events (birthdays, reminders) are intentionally ignored — only timed events block slots
- Dashboard-created session/consultation events are never treated as blocks (distinguished by stored `calEventId`)

### [173411e] Sessions progress bar + Task description + New categories
**Requested by Chirag**
- **Clients view** — Active clients now show a Sessions progress bar (purple, X/Y format) below the Enrollment bar, matching the same visual style
- **Add Task modal** — Added Description textarea field (optional); description saved to task data and displayed below task title in all task lists
- **Task categories** — Added 3 new categories with color-coded tags:
  - Activity Reminder (pink tag)
  - Corporate (green tag)
  - Finance / Payments (teal tag)

### [c232a8d] Mentorship packages added
**Requested by Chirag**
- Added **Mentorship Essentials** package: 28 sessions (24 coaching + 4 Impact), 12 months, ₹2,00,000
- Added **Mentorship Plus** package: 46 sessions (40 coaching + 4 Impact + 2 VIP Strategy), 12 months, ₹3,25,000
- Both packages available in Add Client modal and session tracker

### [f9a3859] Mobile responsive layout + Edit buttons
**Requested by Chirag**
- Full mobile responsive layout with hamburger menu (sidebar slides in/out)
- Mobile overlay closes sidebar on tap
- Session edit buttons (pencil icon ✎) added to Sessions view table rows
- Grid layouts collapse to single column on mobile

---

## 2026-06-05

### [2658409] Session time fix + client seed fix
- Fixed session block times to 90 min (3:00–4:30 PM and 6:00–7:30 PM)
- Fixed client seed logic to merge new clients instead of skipping if any exist

### [119c6ee] Today-view day mapping + Calendar real-time sync + 9 clients seeded
- Fixed day-of-week mapping bug in Today view (was off by one)
- Added Google Calendar real-time sync: 60-second polling interval
- Tab visibility change triggers immediate calendar refresh
- Seeded 9 initial clients: Kiran, Aanya, Chandrashekhar, Dhruv, Vikram, Jeet, Karuna, Sutapa, Yamini

### [69e5c35] Auto-connect Firebase + Google on sign-in
- Firebase auto-connects on page load (no manual setup step)
- Single Google Sign-In covers auth + Tasks + Calendar scopes (no separate Connect button)
- Setup config forms removed — config hardcoded in `FB_CFG` constant
- Auth state persists in `sessionStorage.rh_auth_email` (clears on tab close)

### [d86f84c] Google Sign-In auth gate
- Auth overlay on dashboard load — blocks access until sign-in
- Email allowlist (hardcoded): `team.rituhirani@gmail.com`, `lifecoach.rituhirani@gmail.com`, `counselorsdesk05@gmail.com`
- Unauthorized emails get clear error message
- `_gisLoaded` flag prevents duplicate GIS script injection

### [45065ac] Firebase + Google Calendar integration (initial)
- Firebase Realtime Database: live sync across devices (project: `ritu-ops`, region: `asia-southeast1`)
- Google Tasks API: syncs existing task lists
- Google Calendar API: creates/updates/deletes events when session slots saved (IST timezone)
- No backend — all data in Firebase; localStorage as fallback
- Google OAuth Client ID: `572717103960-a1ibp9q8cdqlc6tptg6lu5htm4p2nfjj.apps.googleusercontent.com`

---

## Dashboard Feature Summary (current state)

| Feature | Status |
|---|---|
| Auth gate (Google Sign-In, 3-email allowlist) | ✅ Live |
| Firebase real-time sync | ✅ Live |
| Google Tasks sync | ✅ Live |
| Google Calendar sync (create/update/delete events) | ✅ Live |
| Today view (tasks, sessions, pending actions, stats) | ✅ Live |
| Week view (Tue–Sun grid, editable slots, calendar events inline) | ✅ Live |
| Clients view (5-stage pipeline, enrollment bar, sessions bar) | ✅ Live |
| Sessions view (progress bars, expiry countdown, +Session button) | ✅ Live |
| Google Tasks view | ✅ Live |
| Setup view (connection status, sign out) | ✅ Live |
| Mobile responsive (hamburger menu, single-column grids) | ✅ Live |
| Task description field | ✅ Live |
| Task categories: Admin, Session, Enrollment, Content, Follow-up, Activity Reminder, Corporate, Finance | ✅ Live |
| Packages: Starter, Growth, Transformation, Relationship, Personality Dev, Impact, Mentorship Essentials, Mentorship Plus | ✅ Live |

---

## Known Issues / To-Do (open)

- E-contract template Section 12 says "45-day package" — placeholder, fix with Ritu before use
- Contact Information Form missing "How did you hear about us?" field
- Onboarding Form missing: therapy/coaching history, health disclosure, preferred session timing, lead source
- Feedback Form missing: NPS score (1–10), continuation/upsell interest question
