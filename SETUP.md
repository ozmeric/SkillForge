# TrainFlow — Setup Guide

TrainFlow is a training assignment, quiz, and reward-points PWA for factories and
teams. Same stack as BuildFlow: single HTML file (React via CDN) + Google Sheets
backend (via Apps Script) + Cloudflare Worker proxy. No build step, no hosting bill.

## What's included
- `index.html` — the app itself (deploy as-is to GitHub Pages)
- `apps-script.gs` — backend, paste into a Google Sheet's Apps Script editor
- `cloudflare-worker.js` — proxy that hides your Apps Script URL from the browser
- `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png` — makes it an installable app

## 1. Google Sheet + Apps Script
1. Create a new Google Sheet. Copy its ID from the URL
   (`.../spreadsheets/d/THIS_PART/edit`).
2. In the Sheet: Extensions → Apps Script. Delete the default code, paste in
   `apps-script.gs`.
3. Set `SHEET_ID` at the top to your Sheet's ID.
4. Change `ADMIN_PIN` to something only you and managers know (this is the PIN
   used to log into the Admin panel).
5. Deploy → New deployment → type: **Web app** → Execute as: **Me** →
   Who has access: **Anyone**. Copy the deployment URL.
6. All the Sheet tabs (JobDescriptions, Trainings, Employees, Progress, Alerts,
   Suggestions, Settings) are created automatically the first time each is used
   — you don't need to set them up by hand.

## 2. Cloudflare Worker (hides your Apps Script URL)
1. Cloudflare Dashboard → Workers & Pages → Create Worker → "Start with Hello
   World" → replace the code with `cloudflare-worker.js`.
2. Paste your Apps Script deployment URL into `APPS_SCRIPT_URL`.
3. Deploy. Copy the worker URL (looks like `https://trainflow-xyz.workers.dev`).

## 3. Configure the app
In `index.html`, near the top of the script:
- Set `API_URL` to your Cloudflare Worker URL.
- Edit the `CONFIG` block: `companyName`, `companyShort`, `primaryColor`,
  `accentColor`, `logoText`. `trainingTypes` and `documentFormats` can be
  customized per client too.

## 4. Deploy the frontend
Upload `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png` to
a GitHub Pages repo (all in the same folder). Employees can open the URL on
their phone and "Add to Home Screen" to install it like a native app — or you
can leave a browser tab open on a shared factory kiosk. Both work with the
same login flow (personal PIN), so you can decide kiosk vs. phone-only later
without changing anything.

## 5. First-time use
1. Open the app → **Admin Login** → enter your `ADMIN_PIN`.
2. Go to **Job Descriptions** → add your roles (Operator, Material Handler,
   Q&A Inspector, etc.) — or go to **Master Training Plan** and click
   **"Seed GT R94 Example Plan"** to load the palletizing example plan from
   your document as a starting point (it auto-creates the "GT R94 - Manual
   Palletizing Operator" job description and its full phase-by-phase plan).
3. Add trainings under **Master Training Plan**: name, phase, type (Safety/
   Quality/Production/HR), format, a content link (wherever you host the
   video/presentation — YouTube unlisted, Google Drive, Loom, etc.), reward
   points, and an optional quiz.
4. Add employees under **Employees**, assigning each to a job description.
   TrainFlow auto-assigns every training for that job description and shows
   you a PIN to give the employee — that's their login.
5. Employees log in with their PIN, see trainings grouped by phase, open the
   content link, take the quiz if there is one, and earn points. Admins track
   completion in **Skill Matrix**, broadcast urgent bulletins in **Alerts**,
   and review employee ideas in **Suggestions**.

## What this doesn't do (yet)
The "AI auto-generates a training video from an SOP + a person's voice" idea
in your original notes is a real video-generation/voice-cloning pipeline —
a separate integration, not something built into this app. For now, content
links point to videos/presentations you (or any AI video tool of your choice)
have already created and hosted somewhere.

## Reusing for a new client
Duplicate the Sheet, redeploy Apps Script with the new Sheet ID and a new PIN,
redeploy a new Worker, and update `CONFIG` + `API_URL` in a fresh copy of
`index.html`. Same pattern as BuildFlow.
