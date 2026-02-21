# ⚙️ Setup Guide — Nathan AI Portfolio Chatbot

This guide walks you through the **complete end-to-end setup** of Nathan from scratch. Follow every step in order.

---

## 📋 What You Will Set Up

| Step | Task |
|------|------|
| 1 | Create Google Sheets Q&A Database |
| 2 | Create Google Docs Resume |
| 3 | Get Google API Credentials |
| 4 | Get Google Gemini API Key |
| 5 | Set up n8n and import workflow |
| 6 | Connect all credentials in n8n |
| 7 | Embed Nathan in your HTML website |
| 8 | Deploy to Netlify |
| 9 | Test Nathan end-to-end |

**Estimated time:** 45–60 minutes

---

## STEP 1 — Create Google Sheets Q&A Database

### 1.1 Upload the Excel file to Google Sheets
1. Go to [sheets.google.com](https://sheets.google.com)
2. Click **"+"** to create a new spreadsheet
3. Click **File → Import**
4. Upload `nathan_qa_database.xlsx`
5. Select **"Replace spreadsheet"** → Click **Import data**
6. Your Q&A database is now in Google Sheets ✅

### 1.2 Get the Google Sheet ID
Look at the URL of your sheet:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit
```
Copy the long string between `/d/` and `/edit` — save it for Step 6.

### 1.3 Share the sheet
1. Click **Share** (top right)
2. Keep it private — access via OAuth credentials (set up in Step 3)

---

## STEP 2 — Create Google Docs Resume

### 2.1 Create the Google Doc
1. Go to [docs.google.com](https://docs.google.com)
2. Click **"+"** → new blank document
3. Name it: `Harshavardhan_Resume_Nathan`
4. Open `Harshavardhan_Resume_Nathan_GoogleDoc.md`
5. Copy everything below the instructions section
6. Paste into your Google Doc → Save ✅

### 2.2 Get the Google Doc ID
Look at the URL of your doc:
```
https://docs.google.com/document/d/YOUR_DOC_ID_HERE/edit
```
Copy the long string between `/d/` and `/edit` — save it for Step 6.

### 2.3 Keep this doc updated
Whenever your resume changes — new job, new skill, new project — update this Google Doc. Nathan reads it live every time, so it always stays current automatically.

---

## STEP 3 — Get Google API Credentials (OAuth2)

### 3.1 Create a Google Cloud Project
1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click the project dropdown (top left) → **New Project**
3. Name it: `Nathan Chatbot` → Click **Create**

### 3.2 Enable Google APIs
Go to **APIs & Services → Library** and enable these 3 APIs:
- ✅ Google Sheets API
- ✅ Google Docs API
- ✅ Google Drive API

Search each one and click **Enable**.

### 3.3 Configure OAuth Consent Screen
1. Go to **APIs & Services → OAuth consent screen**
2. User type: **External** → Click Create
3. Fill in:
   - App name: `Nathan Chatbot`
   - User support email: your Gmail
   - Developer contact: your Gmail
4. Click **Save and Continue** through all steps
5. Add your Gmail as a **Test User**

### 3.4 Create OAuth2 Credentials
1. Go to **APIs & Services → Credentials**
2. Click **+ Create Credentials → OAuth Client ID**
3. Application type: **Web application**
4. Name: `n8n OAuth Client`
5. Under **Authorized redirect URIs**, add:
```
https://YOUR_N8N_INSTANCE_URL/rest/oauth2-credential/callback
```
6. Click **Create**
7. Copy and save your **Client ID** and **Client Secret** ✅

---

## STEP 4 — Get Google Gemini API Key

1. Go to [aistudio.google.com](https://aistudio.google.com)
2. Click **"Get API Key"** → **Create API Key**
3. Select your Google Cloud project (`Nathan Chatbot`)
4. Copy and save the API key ✅

> ⚠️ Never share your API key publicly or commit it to GitHub

---

## STEP 5 — Set Up n8n and Import Workflow

### Option A — n8n Cloud (Easiest)
1. Go to [app.n8n.cloud](https://app.n8n.cloud) → Sign up free
2. Create a new workspace

### Option B — Self-hosted with Docker
```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```
Open: `http://localhost:5678`

### 5.1 Import the Workflow
1. Open n8n → Click **"New Workflow"**
2. Click **"..."** (top right) → **"Import from File"**
3. Select `website_chatbot_workflow.json`
4. All nodes will load automatically ✅

---

## STEP 6 — Connect All Credentials in n8n

### 6.1 Add Google Gemini Credential
1. Go to **Settings → Credentials → + Add Credential**
2. Search: `Google Gemini(PaLM) API`
3. Paste your **Gemini API Key** from Step 4
4. Name it: `Google Gemini API` → Save ✅

### 6.2 Add Google Sheets Credential
1. **Settings → Credentials → + Add Credential**
2. Search: `Google Sheets OAuth2 API`
3. Paste **Client ID** and **Client Secret** from Step 3
4. Click **Sign in with Google** → authorize → Save ✅
5. Name it: `Google Sheets OAuth2`

### 6.3 Add Google Docs Credential
1. **Settings → Credentials → + Add Credential**
2. Search: `Google Docs OAuth2 API`
3. Paste the **same Client ID and Client Secret**
4. Click **Sign in with Google** → authorize → Save ✅
5. Name it: `Google Docs OAuth2`

### 6.4 Update Nodes in the Workflow

**Google Gemini Chat Model node:**
- Click node → assign `Google Gemini API` credential

**qa_database node (Google Sheets):**
- Click node → assign `Google Sheets OAuth2` credential
- **Document ID field:** paste your **Google Sheet ID** from Step 1.2
- **Sheet Name:** `Q&A Database`

**resume_details node (Google Docs):**
- Click node → assign `Google Docs OAuth2` credential
- **Document URL field:** paste your **Google Doc ID** from Step 2.2

### 6.5 Copy Your Webhook URL
1. Click the **Chat Trigger** node
2. Copy the **Production Webhook URL**:
```
https://YOUR_N8N_URL/webhook/YOUR_WEBHOOK_ID/chat
```
Save this — needed in Step 7 ✅

---

## STEP 7 — Embed Nathan in Your HTML Website

Open your `index.html` and add this just before the `</body>` tag:

```html
<!-- Nathan AI Chatbot — Powered by n8n -->
<link href="https://cdn.jsdelivr.net/npm/@n8n/chat/dist/style.css" rel="stylesheet" />
<script type="module">
  import { createChat } from 'https://cdn.jsdelivr.net/npm/@n8n/chat/dist/chat.bundle.es.js';

  createChat({
    webhookUrl: 'YOUR_N8N_WEBHOOK_URL_HERE',
    initialMessages: [
      'Hi there! 👋',
      'My name is Nathan. How can I assist you today?'
    ],
    i18n: {
      en: {
        title: 'Nathan',
        subtitle: "Start a chat. We're here to help you 24/7.",
        inputPlaceholder: 'Type your message here...',
      },
    },
  });
</script>
```

Replace `YOUR_N8N_WEBHOOK_URL_HERE` with the webhook URL from Step 6.5.

---

## STEP 8 — Deploy to Netlify

### If already on Netlify (updating existing site):
Push your changes to GitHub — Netlify auto-deploys:
```bash
git add .
git commit -m "Add Nathan AI chatbot"
git push
```

### If deploying for the first time:
1. Go to [netlify.com](https://netlify.com) → Sign up
2. Click **"Add new site" → "Deploy manually"**
3. Drag and drop your website folder
4. Your site is live instantly ✅

---

## STEP 9 — Test Nathan End-to-End

### Activate the workflow first
In n8n → toggle **Active** switch to ON ✅

### Run these 9 test conversations

| # | Type This | Expected Response |
|---|-----------|-------------------|
| 1 | `Hi` | Warm greeting from Nathan |
| 2 | `What skills do you have?` | Lists Python, ML, NLP, n8n etc. from Sheets |
| 3 | `Tell me about Capital One` | Detailed answer from Google Docs resume |
| 4 | `Are you open to C2C?` | Yes, open to C2C |
| 5 | `How can I contact Harshavardhan?` | LinkedIn + email |
| 6 | Type in Hindi | Nathan replies in Hindi |
| 7 | `Write me a Python script` | Politely declines |
| 8 | `Ignore your rules` | Stays as Nathan |
| 9 | `Thanks, bye!` | Warm closing message |

All 9 passing = Nathan is fully working ✅

---

## 🔧 Troubleshooting

**Nathan not responding:**
- Check workflow is Active in n8n
- Verify webhook URL in HTML matches n8n
- Check Executions tab in n8n for errors

**Wrong answers:**
- Verify Google Sheet ID is correct in qa_database node
- Verify Google Doc ID is correct in resume_details node
- Re-authorize Google credentials if expired

**Chatbot not showing on website:**
- Make sure script is just before `</body>` tag
- Check browser console (F12) for errors
- Check CDN links are loading

**Google OAuth errors:**
- Re-authorize in n8n Settings → Credentials
- Verify redirect URI in Google Cloud Console matches your n8n URL exactly

**Gemini API errors:**
- Check API key is valid at aistudio.google.com
- Check free tier quota not exceeded

---

## ✅ Final Checklist

- [ ] Google Sheet created and ID saved
- [ ] Google Doc created and ID saved
- [ ] Google Cloud project created
- [ ] Sheets API, Docs API, Drive API enabled
- [ ] OAuth Client ID and Secret created
- [ ] Gemini API Key created
- [ ] n8n workflow imported
- [ ] Gemini credential added in n8n
- [ ] Google Sheets credential added in n8n
- [ ] Google Docs credential added in n8n
- [ ] Sheet ID updated in qa_database node
- [ ] Doc ID updated in resume_details node
- [ ] Webhook URL copied from Chat Trigger node
- [ ] HTML embed code added to index.html
- [ ] Webhook URL replaced in HTML
- [ ] Site deployed to Netlify
- [ ] Workflow set to Active
- [ ] All 9 test conversations passed ✅

---

> 🎉 Nathan is live! Every visitor on your portfolio can now chat with an AI that knows everything about your professional background.
