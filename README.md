# Query Quest – Aptitude Test (Grades 1–10)

The test page is hosted on **GitHub Pages**. Every submission is scored by a **Google Apps Script** attached to the question sheet and saved to two tabs in that sheet:

| Tab | What it holds |
|---|---|
| **Responses** | One row per student: name, grade, mobile, score, accuracy, time taken, auto-submitted, late flag |
| **Response Details** | One row per question per student: answer given, correct answer, result, mistake type, time spent |

Both tabs are created automatically on the first submission.

```
Student's browser ──► GitHub Pages (index.html)
                          │  fetch (start / submit)
                          ▼
              Apps Script Web App (Code.gs)
                          │
                          ▼
     Google Sheet: Questions ► Responses ► Response Details
```

## Files in this repo

```
index.html                     the whole test (landing, details, test, report)
assets/vedantu-logo.png        logo
assets/sobha-royal-pavilion.jpg venue photo
README.md                      this guide
```

`Code.gs` is **not** in this repo. It lives in the Google Sheet (Extensions > Apps Script).

---

## Part A: Backend (Google Sheet + Apps Script)

1. Open the Google Sheet that has the **Questions** tab (columns: Grade, Question No., Aptitude Question, Option A–D, Correct Answer, Topic, Detailed solution, Type of question, Type of Mistakes).
2. **Extensions > Apps Script**. Replace everything in `Code.gs` with the new `Code.gs`. You can delete the old `Index` and `Images` files there because they are no longer used. Click **Save**.
3. Pick **testSetup** in the function dropdown and click **Run**. Approve the permissions. The log should list the number of questions found for each grade.
4. **Deploy > New deployment** > gear icon > **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
   - Click **Deploy** and copy the **Web app URL**. It ends in `/exec`.
5. Paste that URL into a new browser tab. You should see `{"ok":true,"app":"Query Quest API",...}`.

> **Company Google account:** if **Anyone** is missing from the access list, your Workspace admin has blocked public web apps. Ask the admin to allow it, or make a copy of the sheet in a personal Gmail account and deploy from there. Do not choose "Anyone within <your domain>", because students would be blocked.

## Part B: Front end (GitHub Pages)

6. In `index.html`, find this line near the top of the `<script>` section:
   ```js
   var API_URL = 'PASTE_YOUR_WEB_APP_URL_HERE';
   ```
   Paste your `/exec` URL between the quotes.
7. On GitHub, go to **New repository**, name it (e.g. `query-quest`), make it **Public**, and click **Create repository**. Click **uploading an existing file** and drag in `index.html`, `README.md` and the `assets` folder. Then click **Commit changes**.
8. Open **Settings > Pages**. Under Build and deployment, choose **Source: Deploy from a branch** and **Branch: main / (root)**, then click **Save**. After 1–2 minutes the site is live at
   `https://<your-github-username>.github.io/query-quest/`
9. Take the test yourself once and check that rows appear in **Responses** and **Response Details**.

---

## Making changes later

| You changed… | Do this |
|---|---|
| `Code.gs` | Deploy > **Manage deployments** > pencil (Edit) > Version: **New version** > Deploy. The URL stays the same. |
| `index.html` | On GitHub, open the file, click the pencil, edit, then **Commit**. The site updates in about 1 minute. |
| Questions in the sheet | Run **clearQuestionCache** in Apps Script, or wait 5 minutes. |
| Timer / marks / "rushed" threshold | Edit the `CONFIG` block at the top of `Code.gs`, then redeploy a new version. |

## Good to know

- Correct answers and solutions never reach the student's browser until the test is submitted, because scoring happens in Apps Script.
- The 30-minute limit is also checked on the server. Late submissions are flagged in the **Late Submission Flag** column.
- If the network drops while a student submits, the page retries automatically and then shows a **Retry** button. Answers stay on screen, and an attempt is never saved twice.
- If you deployed with **New deployment** instead of **Manage deployments**, the URL changes. In that case, update `API_URL` in `index.html` again.
