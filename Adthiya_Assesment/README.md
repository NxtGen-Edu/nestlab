# NEST Quiz — Data, AI & Machine Learning

A single-page quiz app in the NEST theme. Students enter their name and department,
answer 30 questions, and get an instant score plus an explanation of everything they
got wrong. The questions and answers are identical to the printed assessment paper.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app — layout, styling, scoring. Open it and it works. |
| `questions.js` | The 30 questions, options, correct answers and explanations. |
| `README.md` | This file. |

Nothing to install, no build step, no internet needed once loaded.

---

## Try it right now

Download the folder and double-click `index.html`. That's it — it runs offline in any
browser. This is enough if students use one shared laptop.

---

## Host it free on GitHub Pages

1. Create a GitHub account (if you don't have one) and click **New repository**.
2. Name it `nest-quiz`, set it **Public**, tick **Add a README**, then **Create**.
3. Click **Add file → Upload files**. Drag in `index.html` and `questions.js`. Commit.
4. Go to **Settings → Pages**.
5. Under *Build and deployment*, set **Source: Deploy from a branch**,
   **Branch: `main`**, folder **`/ (root)`**, then **Save**.
6. Wait about a minute and refresh. GitHub shows your live link:

   `https://<your-username>.github.io/nest-quiz/`

Share that link with students. It's free, and it works on phones too.

> Other free options if you prefer: **Netlify Drop** (netlify.com/drop — drag the folder
> onto the page, get a link in seconds, no account needed) or **Cloudflare Pages**.

---

## Collect every student's answers in one Google Sheet

By default each result is saved **on that student's own device**, which is no good when
50 students are on 50 laptops. To get everything into one sheet you control:

**1. Make the sheet**
- Create a new Google Sheet. Name it `NEST Quiz Results`.

**2. Add the script**
- In the sheet: **Extensions → Apps Script**.
- Delete whatever is there and paste this:

```javascript
function doPost(e) {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getSheetByName('Results') || ss.insertSheet('Results');
  if (sheet.getLastRow() === 0) {
    sheet.appendRow(['When','Name','Department','Score','Part A','Part B',
                     'Minutes','Answers Q1-Q30','Wrong questions']);
  }
  const d = JSON.parse(e.postData.contents);
  sheet.appendRow([new Date(d.when), d.name, d.dept, d.score, d.partA, d.partB,
                   d.minutes, d.answers, d.wrong]);
  return ContentService.createTextOutput('ok');
}
```

**3. Publish it**
- Click **Deploy → New deployment**.
- Type: **Web app**.
- *Execute as*: **Me**.
- *Who has access*: **Anyone**.  ← this matters, or student browsers can't reach it.
- **Deploy**, approve the permission prompt, and copy the **Web app URL**.

**4. Point the quiz at it**
- Open `index.html`, find this line near the bottom:

```javascript
const SHEET_URL = "";
```

- Paste your URL between the quotes and save:

```javascript
const SHEET_URL = "https://script.google.com/macros/s/AKfy..../exec";
```

- Re-upload `index.html` to GitHub.

Every submission now appends a row to your sheet: name, department, score, Part A and
Part B breakdown, time taken, all 30 answers as a string, and which questions they got
wrong. Sort by score, filter by department, done.

---

## Your trainer view

Add `?admin` to the end of the link:

`https://<your-username>.github.io/nest-quiz/?admin`

It lists every result submitted **in that browser** and lets you download them as CSV.
Useful as a backup, or if the whole class uses one machine. The Google Sheet is the
reliable record when students are on their own laptops.

---

## Things worth knowing

- **Students can retake it.** There is no login. If you need one attempt each, watch the
  room, or check the sheet for duplicate names.
- **Answers are in `questions.js`.** A curious student could open it and read the key.
  This is fine for practice; for a graded exam, use a paper copy or Google Forms.
- **Pass mark** is 18/30. Change `PASS_MARK` in `index.html` to adjust.
- **Part B needs Jupyter open.** Students run each line on `3_Sales_Retail.csv` and
  answer from what they see. Tell them to have the notebook ready before starting.
- **Nothing breaks if storage is blocked.** If a browser blocks local storage, the score
  and explanations still show, and the CSV download still works.
