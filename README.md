# AI Solutions CRM: setup guide

Everything here runs on free services:

| Part | Service | Cost |
|---|---|---|
| The app (index.html) | GitHub Pages, which you already use | Free |
| Shared database | A Google Sheet | Free |
| Backend + email sending | Google Apps Script (Code.gs) | Free (about 100 emails/day on a Gmail account, 1,500 on Google Workspace) |

Total setup time is about 10 minutes.

---

## Step 1. Put the app on GitHub Pages (auto-updates)

1. Open your repository `mmustafam87/ai-solutions-crm` on github.com.
2. Upload `index.html`, `manifest.webmanifest`, `sw.js`, `privacy.html` and the `icons` folder (Add file → Upload files; drag the whole set in). Commit. Don't upload `Code.gs`; it goes into Google Apps Script.
3. Settings → Pages: Source = "Deploy from a branch", branch = `main`, folder = `/ (root)`.
4. After a minute the app is live at `https://mmustafam87.github.io/ai-solutions-crm/`.

Every time you commit a change to `index.html`, GitHub Pages republishes it automatically within about a minute. Nobody needs to install anything.

## Step 2. Create the free backend (Google Sheet + Apps Script)

1. Go to sheets.google.com and create a blank sheet named **AI Solutions CRM Database**.
2. In the sheet: **Extensions → Apps Script**.
3. Delete the sample code, paste the whole of `Code.gs`, and click Save.
4. **Project Settings** (gear icon) → set the time zone to **(GMT+10:00) Brisbane**.
5. Back in the editor, choose the function **setup** in the toolbar and click **Run**. Approve the permissions Google asks for (it's your own script; click "Advanced → Go to project" if you see a warning).
6. Open **Execution log**. Copy the line that says *Your API key*.
7. Click **Deploy → New deployment** → type **Web app**:
   - Execute as: **Me**
   - Who has access: **Anyone**
   - Click Deploy and copy the **Web app URL** (ends in `/exec`).

"Anyone" is needed so the GitHub page can reach it. Requests without the API key are rejected, and reports can only ever be sent to `mohamed@ai-es.com.au` (hard-coded in Code.gs), so the endpoint can't be used to email anyone else.

## Step 3. Connect the app

1. Open the app and sign in as **mohamed@ai-es.com.au** with PIN **1234**. Choose a new PIN when asked.
2. Go to **Settings**, paste the Web app URL and API key, and click **Connect**.
   The first connection uploads your projects and team into the Google Sheet.
3. Click **Copy team invite link** and send it privately to each team member. Opening the link connects their device; they sign in with their email and PIN 1234, then choose their own PIN.

## Step 4 (optional). Automatic weekly email

In the Apps Script editor, select **installWeeklyTrigger** and click **Run**. From then on, last week's report (Monday to Sunday) is emailed to mohamed@ai-es.com.au every Monday around 8am Brisbane time, as PDF and Excel, even if nobody opens the app.

---

## Using it

- **Submit timesheet**: pick project, task, date and hours. The app warns when an entry would push a task or project over its limit (weekly for in-direct projects, total for standard ones).
- **Approvals**: team members' entries start as Pending. Administrators approve them on Timesheets; only approved hours count toward pay.
- **Payroll reports**: choose a week, then **Email PDF + Excel** sends both files to mohamed@ai-es.com.au in one click. You can also download either file.
- **Updates between people**: every device syncs to the Google Sheet and refreshes every minute, so changes from teammates appear automatically.

## Things to know

- **Changing the recipient**: edit `REPORT_EMAIL` at the top of both `Code.gs` (then Deploy → Manage deployments → Edit → New version) and `index.html`.
- **After editing Code.gs** you must publish a new version: Deploy → Manage deployments → pencil icon → Version: New version → Deploy. The URL stays the same.
- **Sign-in is light**: PINs are hashed, but this is a small-team tool, not bank-grade security. The API key is the real lock on your data, so share invite links privately.
- **Public repo**: your GitHub repository is public. `index.html` contains starter data (names, emails, phone numbers) used only before the backend is connected. Once you've connected, you can blank out the `employees` list in `seedDb()` and commit, so personal details live only in your private Google Sheet.
- **Backups**: Settings → Download backup gives you a JSON file. The Google Sheet also keeps version history (File → Version history).
- **Exchange rates** in Settings are manual and only used for the approximate AUD totals.

---

## Windows app in the Microsoft Store

The app is set up as a Progressive Web App (PWA), so Microsoft's free PWABuilder tool can turn it into a Windows Store package (MSIX). People install it from the Store like any other app: Start menu, taskbar, its own window, and your logo. It still loads from your GitHub Pages site, so updates you upload reach everyone automatically without resubmitting to the Store.

1. **Developer account (free).** Go to storedeveloper.microsoft.com and register. Choose **Company** to publish as your business (needs your registered business details, for example your ABN) or **Individual** to publish under your own name. Expect ID verification.
2. **Reserve the name.** In Partner Center → Apps and games → **New product → MSIX or PWA app**, reserve "AI Solutions CRM" (or another available name). Open **Product management → Product identity** and note the **Package ID**, **Publisher ID** and **Publisher display name**.
3. **Build the package.** Go to pwabuilder.com, enter your GitHub Pages link, click **Start**, then **Package for stores → Windows**. Paste the three values from step 2 and download the zip.
4. **Submit.** In Partner Center, start a submission: upload the `.msixbundle` (and the classic `.appxbundle`) from the zip, add a description and at least one screenshot, set category **Business**, and use this privacy policy URL: `https://YOUR-USERNAME.github.io/ai-solutions-crm/privacy.html`.
5. **Choose who can see it** (Pricing and availability → Visibility):
   - **Private audience:** only the Microsoft accounts you list can find and install it. Best for an internal team tool.
   - **Public but not discoverable:** anyone with the direct Store link can install it; it doesn't appear in search.
   - **Public:** anyone can find it.
6. Submit for certification. Microsoft usually reviews within a few business days.

**Updating later:** upload the new files to GitHub as usual; the installed app picks them up. You only resubmit to the Store if you change the app's name, icon or Store listing.
