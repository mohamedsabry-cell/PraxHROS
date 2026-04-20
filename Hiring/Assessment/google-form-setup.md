# Google Form setup — step by step

The assessment site at `index.html` submits each candidate's answers to a Google Form. The Form writes responses to a Google Sheet in your Drive. Follow these steps once — 10 minutes — then every candidate submission lands in the Sheet automatically.

---

## Step 1 — Create the Form

1. Open https://forms.google.com while signed in as **mohamed.sabry@praxionhr.com**.
2. Click **Blank form**. Title it: **Praxion — Technical Assessment Submissions**.
3. Turn on **Settings → Responses → Collect email addresses → Do not verify** (we capture email ourselves).
4. Turn off **Settings → Presentation → Show progress bar** (not needed).

---

## Step 2 — Add these eight fields IN THIS ORDER

For every field, choose **Short answer** except `answersJson` which is **Paragraph**. None of them need to be required — the assessment site handles validation.

| # | Question text (copy exactly) | Type |
|---|------------------------------|------|
| 1 | `Name` | Short answer |
| 2 | `Email` | Short answer |
| 3 | `Role` | Short answer |
| 4 | `StartedAt` | Short answer |
| 5 | `SubmittedAt` | Short answer |
| 6 | `DurationMin` | Short answer |
| 7 | `TabSwitches` | Short answer |
| 8 | `AnswersJson` | **Paragraph** |

> Why the field names are title-case with no spaces: makes the resulting Sheet columns easy to read and script against.

---

## Step 3 — Connect to a Google Sheet in Drive

1. In the Form, click the **Responses** tab.
2. Click the green Sheets icon ("Link to Sheets").
3. Choose **Create a new spreadsheet** → name it `Praxion — Assessment Responses`.
4. After it's created, open the Sheet. Move it into `/My Drive/Praxion/Hiring/` (or wherever you want). Responses will stream into this Sheet as candidates submit.

---

## Step 4 — Grab the `formResponse` URL

1. In the Form, click **Send** (top right) → the link icon → copy the link. It looks like: `https://docs.google.com/forms/d/e/1FAIpQLSd.../viewform`.
2. Replace `viewform` at the end with `formResponse`. You now have something like:
   `https://docs.google.com/forms/d/e/1FAIpQLSd.../formResponse`
3. **Save this URL** — you'll paste it into `index.html` in Step 6.

---

## Step 5 — Grab the eight `entry.xxxxxx` IDs

Google assigns a unique `entry.xxxxxx` ID to every field. We need all eight.

**Easiest method — prefilled link:**
1. In the Form, click the three-dot menu (top right) → **Get pre-filled link**.
2. Type placeholder text into each of the 8 fields (anything, e.g. `x1`, `x2`, ... `x8`).
3. Click **Get link** at the bottom → then **Copy link**.
4. Paste the copied URL into a text editor. It looks like:
   ```
   https://docs.google.com/forms/d/e/.../viewform?usp=pp_url
     &entry.1234567890=x1
     &entry.2345678901=x2
     &entry.3456789012=x3
     &entry.4567890123=x4
     &entry.5678901234=x5
     &entry.6789012345=x6
     &entry.7890123456=x7
     &entry.8901234567=x8
   ```
5. The order matches the order you created the fields — so `entry.*=x1` is the Name field, `entry.*=x2` is Email, etc.

Write down the eight IDs against the field they belong to.

---

## Step 6 — Paste everything into `index.html`

Open `index.html` in a text editor. Near the top of the `<script>` tag (look for `// ----- CONFIG -----`), replace:

```js
const GOOGLE_FORM_URL = "REPLACE_WITH_YOUR_GOOGLE_FORM_FORMRESPONSE_URL";

const FORM_FIELDS = {
  name:        "entry.1111111111",
  email:       "entry.2222222222",
  role:        "entry.3333333333",
  startedAt:   "entry.4444444444",
  submittedAt: "entry.5555555555",
  durationMin: "entry.6666666666",
  tabSwitches: "entry.7777777777",
  answersJson: "entry.8888888888",
};
```

...with your real values from Steps 4 and 5. Save the file.

---

## Step 7 — Test it end-to-end

1. Open `index.html` locally (double-click — it runs without a server).
2. Fill the intro with a test name and email (e.g. `Test User` / `test@praxionhr.com`).
3. Click Start, answer one question, click Submit.
4. Open the linked Sheet. Within ~5 seconds, a new row should appear with your test data.
5. The `AnswersJson` column holds the full answer JSON. Use **Data → Split text to columns** or just read it as-is.

If nothing shows up — check the browser DevTools Network tab. The POST to `formResponse` should be status 200 (note: `mode: 'no-cors'` means the response is opaque, so the site treats it as success regardless — the only way to confirm is to check the Sheet).

---

## Step 8 — Share the Sheet with whoever reviews

The Sheet is in your Drive. Right-click → Share → add reviewers. The Form itself does not need to be shared — candidates never see it; they interact with the Netlify-hosted site.

---

## Notes

- **Why a single `AnswersJson` column** instead of one column per question? Simpler form config (8 fields vs 9+), easier to add/remove questions later, and the JSON is trivial to expand in a reviewing script if you want to.
- **Duplicate submissions**: if a candidate hits Submit twice (e.g. refreshed the "Done" screen), you may see duplicate rows. The site blocks this on the client side, but rows are keyed by email+submitted-at so duplicates are easy to spot.
- **Privacy**: responses live only in your Drive. No third-party service. Google Form uses HTTPS.
