# 🛠️ Complete Setup Guide — WhatsApp AI Agent (n8n)

This guide walks you through the **full setup from scratch** — from creating your Meta developer app to having a live AI-powered WhatsApp bot running on n8n.

Estimated time: **45–90 minutes**

---

## 📋 Prerequisites Checklist

Before starting, confirm you have access to:

- [ ] A computer with a stable internet connection
- [ ] A **Meta (Facebook) Developer Account** — [developers.facebook.com](https://developers.facebook.com)
- [ ] A **Google Account** (for Sheets + Gemini API)
- [ ] A **phone number** registered with WhatsApp (for testing)
- [ ] **n8n** installed and running (self-hosted or cloud)

---

## 📦 Part 1 — Install & Run n8n

### Option A: n8n Cloud (Easiest)
1. Go to [https://app.n8n.cloud](https://app.n8n.cloud)
2. Sign up for a free account
3. Your n8n instance is ready — no installation needed

### Option B: Self-Hosted via npm
```bash
npm install n8n -g
n8n start
```
Open browser at: `http://localhost:5678`

### Option C: Self-Hosted via Docker
```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```
Open browser at: `http://localhost:5678`

> ⚠️ **Important:** For WhatsApp webhooks to work, your n8n instance must be **publicly accessible** (not localhost). Use a tool like [ngrok](https://ngrok.com/) for local testing or deploy to a VPS/cloud server.

### Using ngrok for Local Testing
```bash
# Install ngrok
npm install -g ngrok

# Start n8n first, then expose it
ngrok http 5678
```
Copy the `https://xxxx.ngrok.io` URL — this will be your webhook base URL.

---

## 📊 Part 2 — Set Up Google Sheets

### Step 1: Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Create a **New Spreadsheet**
3. Name it: `WhatsApp AI Agent Data`
4. Create **3 tabs** at the bottom: `inventory`, `faq`, `orders`

---

### Step 2: Set Up the `inventory` Tab

Click the `inventory` tab and add these exact column headers in Row 1:

| A | B | C | D | E |
|---|---|---|---|---|
| product_name | category | price | stock_quantity | description |

Then fill in your actual products from Row 2 onwards. Example:

| product_name | category | price | stock_quantity | description |
|---|---|---|---|---|
| iPhone 15 | Electronics | 79999 | 30 | Latest Apple smartphone |
| Samsung TV 55" | Electronics | 49999 | 15 | 4K UHD Smart TV |
| Nike Air Max | Footwear | 8999 | 100 | Running shoes |

---

### Step 3: Set Up the `faq` Tab

Click the `faq` tab and add these headers in Row 1:

| A | B |
|---|---|
| question | answer |

Fill in your FAQs from Row 2:

| question | answer |
|---|---|
| What are your delivery times? | We deliver within 3–5 business days across India. |
| Do you accept returns? | Yes, returns accepted within 7 days with original packaging. |
| What payment methods do you accept? | UPI, Credit/Debit Card, Net Banking, and Cash on Delivery. |
| How do I track my order? | You'll receive a tracking link on WhatsApp once your order is shipped. |
| Is COD available? | Yes, COD is available for orders below ₹5000. |

---

### Step 4: Set Up the `orders` Tab

Click the `orders` tab and add these headers in Row 1 only — **do not fill any data**, the workflow will auto-append:

| A | B | C | D | E |
|---|---|---|---|---|
| timestamp | customer_phone | product_name | quantity | status |

---

### Step 5: Copy the Spreadsheet ID

From your browser URL when the Sheet is open:
```
https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID_HERE/edit
```
Copy the long string between `/d/` and `/edit` — you'll need this later.

---

## 🔑 Part 3 — Google Cloud & Sheets API Setup

### Step 1: Create a Google Cloud Project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click **Select a Project** → **New Project**
3. Name it `whatsapp-agent` → Click **Create**

### Step 2: Enable Required APIs

1. In the left menu → **APIs & Services** → **Library**
2. Search for and enable:
   - ✅ **Google Sheets API**
   - ✅ **Google Drive API**

### Step 3: Create OAuth 2.0 Credentials

1. Go to **APIs & Services** → **Credentials**
2. Click **+ Create Credentials** → **OAuth 2.0 Client ID**
3. If prompted, configure the **OAuth Consent Screen** first:
   - User Type: **External**
   - App Name: `WhatsApp Agent`
   - Add your email as test user
4. Back to Create Credentials → OAuth 2.0 Client ID:
   - Application Type: **Web Application**
   - Name: `n8n-whatsapp-agent`
   - Authorized redirect URIs: Add your n8n OAuth callback URL:
     ```
     https://your-n8n-domain.com/rest/oauth2-credential/callback
     ```
     (For local: `http://localhost:5678/rest/oauth2-credential/callback`)
5. Click **Create** → copy the **Client ID** and **Client Secret**

---

## 🤖 Part 4 — Google Gemini API Setup

### Step 1: Get Your API Key

1. Go to [aistudio.google.com](https://aistudio.google.com)
2. Sign in with your Google account
3. Click **Get API Key** in the left sidebar
4. Click **Create API Key** → Select your Google Cloud project
5. Copy the generated API key and store it safely

> ⚠️ Never commit this API key to GitHub. Use environment variables or n8n's credential manager.

---

## 📱 Part 5 — WhatsApp Business API (Meta) Setup

This is the most involved part. Follow carefully.

### Step 1: Create a Meta Developer App

1. Go to [developers.facebook.com](https://developers.facebook.com)
2. Click **My Apps** → **Create App**
3. Select **Business** as the app type
4. Fill in App Name: `WhatsApp AI Agent`
5. Click **Create App**

### Step 2: Add WhatsApp Product

1. On your app dashboard, scroll down to **Add Products**
2. Find **WhatsApp** → click **Set Up**
3. You'll be taken to the WhatsApp Getting Started page

### Step 3: Get Your API Credentials

On the WhatsApp Getting Started page, note down:

| Field | Where to find it |
|---|---|
| **Phone Number ID** | Shown on the Getting Started page |
| **WhatsApp Business Account ID** | Shown on the Getting Started page |
| **Temporary Access Token** | Click "Generate Token" — valid for 24hrs |

> For production, generate a **Permanent Token** via a System User in Meta Business Manager.

### Step 4: Add a Test Phone Number

1. Under **To** field in the API setup, click **Manage Phone Number List**
2. Add your personal WhatsApp number for testing
3. You'll receive a verification code on WhatsApp — enter it

### Step 5: Set Up the Webhook

> You'll need your n8n webhook URL for this step. Get it after importing the workflow in Part 6, then come back here.

1. In the left sidebar → **WhatsApp** → **Configuration**
2. Under **Webhook** → click **Edit**
3. Set:
   - **Callback URL:** `https://your-n8n-url/webhook/whatsapp`
   - **Verify Token:** Any custom string, e.g., `my_whatsapp_secret_123`
4. Click **Verify and Save**
5. After saving, click **Manage** next to Webhook Fields
6. Subscribe to: ✅ `messages`

---

## ⚙️ Part 6 — n8n Workflow Setup

### Step 1: Import the Workflow

1. Open your n8n instance
2. Click **New Workflow**
3. Click `...` (top right) → **Import from File**
4. Select `workflow/whatsapp_ai_agent.json`
5. The workflow will load with all nodes visible

### Step 2: Add Credentials in n8n

Go to **Settings** → **Credentials** → **Add Credential**

#### Add Google Gemini Credential
- Type: `Google Gemini(PaLM) API`
- Name: `Google Gemini`
- API Key: *(paste your Gemini API key)*
- Save

#### Add Google Sheets Credential
- Type: `Google Sheets OAuth2 API`
- Name: `Google Sheets`
- Client ID: *(from Google Cloud)*
- Client Secret: *(from Google Cloud)*
- Click **Sign in with Google** → Authorize access
- Save

#### Add WhatsApp Credential
- Type: `WhatsApp Business Cloud API`
- Name: `WhatsApp API`
- Access Token: *(your Meta access token)*
- Phone Number ID: *(from Meta developer console)*
- Save

### Step 3: Assign Credentials to Nodes

Click each node and assign the correct credential:

| Node | Credential to Assign |
|---|---|
| WhatsApp Trigger | WhatsApp API |
| Google Gemini Chat Model | Google Gemini |
| Simple Memory | *(no credential needed)* |
| get inventory | Google Sheets |
| FAQ | Google Sheets |
| take orders | Google Sheets |
| Send message | WhatsApp API |

### Step 4: Update Spreadsheet IDs

For each Google Sheets node (`get inventory`, `FAQ`, `take orders`):
1. Click the node
2. Find the **Spreadsheet ID** field
3. Replace the existing ID with **your** Spreadsheet ID (copied in Part 2, Step 5)
4. Select the correct **Sheet Name** (`inventory`, `faq`, or `orders`)

### Step 5: Get Your Webhook URL

1. Click the **WhatsApp Trigger** node
2. Copy the **Production Webhook URL** shown
3. Go back to Meta Developer Console → Webhook → paste this URL (Part 5, Step 5)

---

## ✅ Part 7 — Testing the Workflow

### Test 1: Workflow Execution Test

1. In n8n, click **Execute Workflow** (bottom of screen)
2. Send a WhatsApp message from your test number to your business number
3. Watch each node light up green as data flows through
4. Check the final output in the **Send message** node

### Test 2: Test Each Tool

Send these messages to trigger each tool:

| Message to send | Expected tool triggered |
|---|---|
| `Do you have iPhone 15 in stock?` | `get inventory` |
| `What is your return policy?` | `FAQ` |
| `I want to order 2 units of Nike Air Max` | `take orders` |
| `Hi, how are you?` | General AI response (no tool) |

### Test 3: Verify Google Sheets

After sending an order message, open your Google Sheet → `orders` tab. You should see a new row auto-appended with the order details.

---

## 🚀 Part 8 — Going Live (Production)

### Checklist before going live:

- [ ] Replace temporary Meta access token with a **permanent system user token**
- [ ] n8n instance is deployed on a **public server** (not localhost)
- [ ] Webhook URL is **HTTPS** (not HTTP)
- [ ] Workflow is set to **Active** (toggle in top-right of n8n)
- [ ] Google Sheets OAuth token is refreshed and working
- [ ] Test numbers removed from Meta whitelist (open to all users)

### Recommended Deployment for n8n

For self-hosted production deployments:

```bash
# Using Docker Compose with persistent storage
version: '3'
services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=your-domain.com
      - N8N_PROTOCOL=https
      - WEBHOOK_URL=https://your-domain.com/
    volumes:
      - ~/.n8n:/home/node/.n8n
```

Use **nginx** as a reverse proxy with **Let's Encrypt SSL** for HTTPS.

---

## 🐛 Common Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| `Webhook not receiving messages` | Wrong webhook URL or not subscribed to `messages` | Re-verify webhook in Meta console and check subscription |
| `Google Sheets: 403 Forbidden` | OAuth not authorized or wrong scopes | Re-authorize Google Sheets credential in n8n |
| `Gemini API: 400 Bad Request` | Invalid API key or wrong model name | Check API key and model name in Gemini node |
| `WhatsApp: Message not sending` | Wrong Phone Number ID or expired token | Refresh access token in Meta console |
| `Agent not using tools` | Tool descriptions not clear enough | Edit the tool description inside the AI Agent node to be more specific |
| `Memory not working` | Session ID not set correctly | Check Simple Memory node → Session ID should be set to customer phone number |

---

## 🔐 Security Best Practices

- **Never hardcode API keys** in the workflow JSON before uploading to GitHub — clear all credentials first
- Use **n8n environment variables** for sensitive values
- Set a **strong Verify Token** for the WhatsApp webhook
- Rotate your **Meta access token** regularly
- Restrict your **Google Cloud OAuth** to only necessary scopes
- Use **HTTPS** for all webhook endpoints

---

## 📞 Support & Resources

| Resource | Link |
|---|---|
| n8n Documentation | [docs.n8n.io](https://docs.n8n.io) |
| Meta WhatsApp API Docs | [developers.facebook.com/docs/whatsapp](https://developers.facebook.com/docs/whatsapp) |
| Google Gemini API Docs | [ai.google.dev/docs](https://ai.google.dev/docs) |
| Google Sheets API | [developers.google.com/sheets](https://developers.google.com/sheets) |
| n8n Community Forum | [community.n8n.io](https://community.n8n.io) |

---

*Setup guide written for the WhatsApp AI Agent n8n project. Refer to `README.md` for project overview and architecture.*