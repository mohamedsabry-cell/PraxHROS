# Praxion — Technical Assessment

A live, 75-minute, web-based coding assessment used to shortlist engineering candidates for a 3rd interview. Runs as a single static HTML file, hosted on Netlify. Submissions land in a Google Sheet in your Drive.

---

## What's in this folder

| File | Purpose |
|------|---------|
| `index.html` | The assessment site itself. Single file — React via CDN. |
| `google-form-setup.md` | 10-minute setup to create the Google Form + Drive Sheet, get the entry IDs, and paste them into `index.html`. **Do this first.** |
| `scoring-rubric.md` | Internal rubric for reviewers. Highlights the AI-resistance signals to watch for in each question. |
| `README.md` | This file. |

---

## End-to-end setup (once, ~20 minutes)

1. **Create Google Form and link to a Drive Sheet** → follow `google-form-setup.md` (Steps 1–5).
2. **Paste Form URL + entry IDs** into `index.html` (Step 6 of that doc).
3. **Test locally** — open `index.html`, fill in a test submission, confirm the row appears in the Sheet.
4. **Deploy to Netlify** — see below.
5. **Send candidates the Netlify URL**.

---

## Deploy to Netlify (2 minutes, no CLI needed)

1. Go to https://app.netlify.com, sign in.
2. Click **Add new site → Deploy manually**.
3. Drag the **folder containing `index.html`** (this `Assessment/` folder) onto the drop zone.
4. Netlify gives you a URL like `https://radiant-hroslark-123abc.netlify.app`.
5. Click **Site configuration → Change site name** → rename it to something like `praxion-assessment` so the URL reads cleanly.
6. Done. Every candidate goes to the same URL and picks their role on the intro screen.

### Updating the assessment later

If you change `index.html`, go back to the same Netlify site → **Deploys** → drag the updated folder again. The URL stays the same.

### Optional: custom domain

You can point `assessment.praxionhr.com` at the Netlify site if you want. In Netlify: **Domain management → Add a domain**. You'll need to add a DNS record at your domain registrar (Netlify shows you which one). Skip for v1 unless it matters.

---

## Workflow for each candidate

1. Email the candidate: "Here's the link, you have 75 minutes from when you click Start. Use the email we invited you on."
2. They open the URL → type name + email + pick role → click Start.
3. Timer runs. Answers auto-save in their browser. If they close the tab, answers come back when they reopen — but the clock keeps running.
4. They click Submit (or the site auto-submits at 00:00).
5. A new row appears in your Google Sheet within a few seconds.
6. Review against `scoring-rubric.md`.

---

## What the Google Sheet will contain per candidate

| Column | Example |
|--------|---------|
| Timestamp | Google's own timestamp |
| Name | Walid Sayed |
| Email | walid@example.com |
| Role | frontend / backend |
| StartedAt | 2026-04-21T10:02:15Z |
| SubmittedAt | 2026-04-21T11:16:02Z |
| DurationMin | 74 |
| TabSwitches | 3 |
| AnswersJson | `{ "A1": "...", "A2": "...", ... }` (full answers in one cell) |

The `AnswersJson` cell is where the actual answers live. You can read it directly, or write a short Apps Script to explode it into columns — up to you.

---

## AI-resistance — what's baked in

Standard coding tasks are trivially solved by pasting the question into ChatGPT or Claude. This assessment is designed so that a copy-paste answer produces something **confident-sounding but wrong-shaped**. What we test:

- **Domain judgment** — HR/payroll pitfalls (e.g. soft-delete on `employees`, salary history, transfer effective dates) that LLMs won't flag unless explicitly prompted.
- **Bug-to-symptom mapping** — LLMs find individual bugs but don't reliably link each one to a specific user-visible symptom.
- **Intentional ambiguity** — one question has a deliberate contradiction in the spec. Top candidates name it; LLMs silently pick one and miss the other.
- **Severity ranking** — no single right answer; reasoning is the signal.
- **"What would you push back on?"** — LLMs default to compliance. Senior humans push back.
- **Tab-switch counting** — we log how many times the candidate left the tab. Not a catch-cheating mechanism per se, just a data point for reviewers.

See `scoring-rubric.md` for the specific signals to read for.

---

## If the candidate has a technical problem

- Timer keeps running when they close the tab. That's intentional.
- If they email to say the site crashed or answers are lost, have them screenshot `localStorage` in DevTools (`Application → Local Storage → Your site`) — answers live under keys starting with `px_`.
- For a true emergency, they can email raw answers to **mohamed.sabry@praxionhr.com** and you can grade those manually.

---

## Cost

- Netlify: free tier is plenty.
- Google Form / Drive: free with your Google account.
- Total: $0/month.

---

## Removing / rotating after hiring is done

- Pause the Netlify site: **Site configuration → Site visibility → Stop auto-publishing**.
- Or archive it entirely and delete the deploy — the Google Sheet keeps all past responses.
