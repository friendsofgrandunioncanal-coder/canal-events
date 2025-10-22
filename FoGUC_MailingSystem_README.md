# 💌 Friends of Grand Union Canal – Mailing List System

This setup automatically manages new volunteer signups, sends welcome emails, and handles unsubscribe requests — all integrated with Google Sheets, Apps Script, and GitHub Pages.

---

## 🧱 System Overview

| Component | Purpose | Location |
|------------|----------|----------|
| **Google Sheet** | Stores volunteer signup data (timestamp, name, email, consent) | `Volunteer Signups` tab |
| **Google Apps Script** | Processes form submissions, sends welcome emails, and removes unsubscribed users | Apps Script project: `Mailing List / FoGUC_App.gs` |
| **GitHub Pages** | Hosts the public unsubscribe form | [`https://friendsofgrandunioncanal-coder.github.io/foguc-assets/unsubscribe.html`](https://friendsofgrandunioncanal-coder.github.io/foguc-assets/unsubscribe.html) |
| **Google Sites (optional)** | Hosts the “Keep in Touch” webform | [`https://www.friendsofgrandunioncanal.co.uk/keep-in-touch`](https://www.friendsofgrandunioncanal.co.uk/keep-in-touch) |

---

## ⚙️ How It Works

### 1. Volunteer signs up
- User submits the **“Keep in Touch”** form on the website.  
- The form POSTs data (`firstName`, `lastName`, `email`, `consent`) to the Apps Script web app endpoint.  
- Apps Script adds a new row to the `Volunteer Signups` sheet.  

### 2. Automatic welcome email
- Apps Script fetches the latest event from GitHub (`canal-events` repo).
- Sends a **personalised HTML welcome email** with:
  - Upcoming event preview  
  - “See all our events” button  
  - **Unsubscribe link** (points to GitHub unsubscribe page)

---

### 3. Unsubscribe process
- Email link looks like this:  
  ```
  https://friendsofgrandunioncanal-coder.github.io/foguc-assets/unsubscribe.html?email=someone@example.com
  ```
- The unsubscribe page:
  - Auto-fills their email address (from query parameter)
  - Shows a confirmation button
  - Calls the Apps Script endpoint silently:
    ```
    https://script.google.com/macros/s/[deployment-ID]/exec?unsubscribe=email@example.com
    ```
- Apps Script removes the row from the `Volunteer Signups` sheet and returns a confirmation popup.

---

## 🧾 Files and Components

### 1. **Apps Script: `FoGUC_App.gs`**
Located in your Apps Script project (same as the volunteer spreadsheet).

Contains:
- `doPost(e)` → Handles form submissions  
- `buildEmailBody()` → Builds the HTML email  
- `doGet(e)` → Handles unsubscribe requests  
- `formatDate()` → Formats event dates  
- `testEmail()` → Sends a test email for previewing  

**Deployment settings:**
- Execute as: *Me (friendsofgrandunioncanal@gmail.com)*  
- Who has access: *Anyone*  
- Web app URL:  
  ```
  https://script.google.com/macros/s/AKfycbxFYypWfi40gdcrUQamJ0Je3JDYEVT90GTOZelKE3xlPlU9IJyE--IdOoU2xK2hgsbfUg/exec
  ```

---

### 2. **GitHub Pages: `unsubscribe.html`**
Located in:  
[`foguc-assets/unsubscribe.html`](https://github.com/friendsofgrandunioncanal-coder/foguc-assets/blob/main/unsubscribe.html)

Features:
- Simple unsubscribe form  
- Pre-fills the user’s email  
- Spinner animation during processing  
- Calls the Apps Script endpoint via `fetch()`  
- Displays confirmation popup  

**To update it:**
1. Edit `unsubscribe.html` in GitHub.  
2. Commit and push.  
3. GitHub Pages automatically redeploys within 1–2 minutes.

---

### 3. **Events data**
Stored in the repo:  
[`canal-events/events.json`](https://github.com/friendsofgrandunioncanal-coder/canal-events/blob/main/events.json)

Each event contains:
```json
{
  "title": "Canal Clean-up at Marsworth",
  "date": "2025-11-16",
  "area": "Marsworth Reservoir",
  "image": "https://friendsofgrandunioncanal-coder.github.io/foguc-assets/marsworth.jpg"
}
```

Apps Script fetches this file and includes the next upcoming event in welcome emails.

---

## 🧪 Maintenance Tasks

| Task | How to do it |
|------|---------------|
| **Send test email** | Open Apps Script → Run `testEmail()` |
| **Update unsubscribe page** | Edit `unsubscribe.html` on GitHub Pages |
| **Update event list** | Edit `events.json` in the `canal-events` repo |
| **Check new volunteers** | Open Google Sheet → tab `Volunteer Signups` |
| **Check email log** | Open Google Sheet → tab `Email Log` |

---

## 🚨 Troubleshooting

| Issue | Likely Cause | Fix |
|-------|---------------|-----|
| “Sorry, unable to open the file…” | Wrong or private Google Script link in email | Ensure unsubscribe link points to GitHub Pages URL |
| Email missing unsubscribe link | Forgot to redeploy script after edit | Open Apps Script → “Deploy” → “Manage deployments” → “Update” |
| Unsubscribe form doesn’t remove from list | Script URL in unsubscribe form outdated | Check form JavaScript → update `fetch()` URL |
| Emails not being sent | Apps Script exceeded quota or GmailApp permission expired | Re-authorize script (Run → testEmail → review permissions) |

---

## 🔒 Security Tips
- Never share the full edit link to your Apps Script project.  
- Only the **published web app URL** should be public.  
- Keep the GitHub repos public but avoid storing any personal data there.  
- Emails and consent data are safely stored in your Google Sheet.

---

## 🧭 Summary

✅ **Data source** → Google Sheet  
✅ **Automation logic** → Google Apps Script  
✅ **Public web assets** → GitHub Pages  
✅ **Website integration** → Google Sites  
