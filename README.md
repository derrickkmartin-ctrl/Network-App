# Ebony SF Trip — Networking App

A mobile-first field tool for Ebony Twilley Martin's San Francisco funder trip. Syncs contact notes directly to the RLL BD Tracker in Google Sheets.

---

## What this does

- Instant lookup of all 20 SF trip contacts by name or org
- Conversation angles, watch-outs, and Counterstream pitch — always one tap away
- Post-conversation note capture with warmth rating (warm / lukewarm / cold)
- One-tap sync to Google Sheets BD Pipeline and Nurture List
- Follow-up tracker with push-to-Sheets from the follow-up queue
- Notes persist across sessions via localStorage — survives closing the browser

---

## Repository structure

```
ebony-sf-app/
├── index.html      ← The app (deploy via GitHub Pages)
├── Code.gs         ← Google Apps Script (deploy inside Google Sheets)
└── README.md       ← This file
```

---

## Setup: two steps

### Step 1 — Deploy the app via GitHub Pages

1. Create a new GitHub repository (can be private or public)
2. Upload `index.html` and `README.md` to the repo root
3. Go to **Settings → Pages**
4. Under **Source**, select `Deploy from a branch`
5. Choose `main` branch, `/ (root)` folder → click **Save**
6. Your app will be live at: `https://yourusername.github.io/your-repo-name`

Share that URL with Ebony. On iPhone: open in Safari → tap Share → **Add to Home Screen**. It will behave like a native app.

---

### Step 2 — Deploy the Google Apps Script

This connects the app to Ebony's Google Sheet (the RLL BD Tracker).

#### 2a. Get your Sheet ID

Open the Google Sheet. The URL looks like:
```
https://docs.google.com/spreadsheets/d/SHEET_ID_IS_HERE/edit
```
Copy everything between `/d/` and `/edit`. That's your Sheet ID.

#### 2b. Add the script to the Sheet

1. In Google Sheets: go to **Extensions → Apps Script**
2. Delete any existing code in the editor
3. Paste the entire contents of `Code.gs`
4. On line 8, replace `'YOUR_GOOGLE_SHEET_ID_HERE'` with your actual Sheet ID:
   ```javascript
   const SHEET_ID = 'your-actual-sheet-id-here';
   ```
5. Click **Save** (the floppy disk icon or Ctrl+S)

#### 2c. Deploy as a web app

1. Click **Deploy → New deployment**
2. Click the gear icon next to "Select type" → choose **Web app**
3. Set:
   - **Description**: `RLL SF Trip Receiver`
   - **Execute as**: `Me`
   - **Who has access**: `Anyone`
4. Click **Deploy**
5. If prompted, click **Authorize access** and follow the Google sign-in flow
6. Copy the **Web app URL** — it starts with `https://script.google.com/macros/s/...`

#### 2d. Connect the app to the script

**Option A (recommended for production):** Open `index.html`, find line:
```javascript
const DEFAULT_SCRIPT_URL = ''; // ← Paste your URL here after deploying
```
Paste your deployment URL between the quotes. Push the updated file to GitHub.

**Option B (no code edit needed):** Leave `DEFAULT_SCRIPT_URL` empty. In the app, tap the ⚙ gear icon → paste the URL → tap **Save & test connection**. The URL is saved to localStorage on Ebony's device.

---

## Sheet requirements

The script expects these two tab names in the Google Sheet (exact spelling):
- `BD Pipeline`
- `Nurture List`

If your tab names differ, update lines 4–5 in `Code.gs`:
```javascript
const PIPELINE_TAB = 'BD Pipeline';
const NURTURE_TAB  = 'Nurture List';
```

---

## What gets written to the Sheet

### BD Pipeline (one row per synced contact)

| Column | Value |
|--------|-------|
| Contact Name | Person's name |
| Organization | Their org |
| Audience Type | Philanthropic Funder |
| Pipeline Stage | Mapped from warmth rating (Warm → Discovery Call, Lukewarm → Initial Outreach, Cold → Nurturing) |
| Referral Source | SF Funder Event 2026 |
| Last Contact | Today's date |
| Next Action | Follow-up action (editable in the app before syncing) |
| Due Date | 3 business days from sync |
| Priority | Mapped from warmth (Warm → High, Lukewarm → Medium, Cold → Low) |
| Notes | Everything captured in the note field |
| Fit Score | Auto-scored by tier (Tier 1 = 8, Tier 2 = 6, Tier 3/4 = 4) |
| Signal | Tier + event + warmth |
| Lead Hunt Source | SF Funder Event — May 2026 |

### Nurture List (when "Add to Nurture List" is tapped)

| Column | Value |
|--------|-------|
| Name | Person's name |
| Organization | Their org |
| Relationship Type | Funder |
| Strength | Mapped from warmth |
| Tier | Tier 1 / Tier 2 / Tier 3 |
| Last Touch | Today's date |
| Next Touch | +2 weeks (Tier 1) or +4 weeks (Tier 2+) |
| Frequency | Every 6 Weeks (Tier 1) or Quarterly (Tier 2+) |

---

## Re-deploying after changes

If you update `Code.gs`, you must create a **new deployment** for changes to take effect:
1. Apps Script editor → **Deploy → Manage deployments**
2. Click the edit pencil on your existing deployment
3. Change **Version** to `New version`
4. Click **Deploy**
5. The URL stays the same — no changes needed in the app

---

## Troubleshooting

**"Sync failed — check your URL in settings"**
- Make sure the deployment is set to `Anyone` (not `Anyone with link` or `Only myself`)
- Try re-deploying as a new version
- Check that your Sheet ID is correct in Code.gs

**Tab not found error**
- Check that your Sheet tabs are named exactly `BD Pipeline` and `Nurture List`
- Update `PIPELINE_TAB` and `NURTURE_TAB` in Code.gs if they differ

**Notes disappeared**
- Notes are stored in localStorage on the device. Clearing browser data will erase them.
- Always sync to Sheets before clearing browser data.

---

*Built for Ebony Twilley Martin · Regenerative Leadership Lab · May 2026*
