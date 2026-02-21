# 🤖 WhatsApp AI Agent — n8n Automation

An intelligent WhatsApp chatbot built with **n8n**, **Google Gemini**, and **Google Sheets** that handles customer queries, checks inventory, answers FAQs, and takes orders — fully automated, end-to-end.

![Workflow Screenshot](assets/workflow_screenshot.png)

---

## 📌 Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Google Sheets Structure](#google-sheets-structure)
- [API & Credentials Setup](#api--credentials-setup)
- [n8n Setup & Workflow Import](#n8n-setup--workflow-import)
- [Node-by-Node Breakdown](#node-by-node-breakdown)
- [How to Run](#how-to-run)
- [Folder Structure](#folder-structure)
- [Use Cases](#use-cases)
- [Future Improvements](#future-improvements)

---

## Project Overview

This project automates WhatsApp customer interactions using an AI agent. When a customer sends a message on WhatsApp, the system:

1. Receives the message via the **WhatsApp Business API**
2. Routes it through an **AI Agent** (powered by Google Gemini)
3. The agent decides which **tool** to use — check inventory, answer FAQ, or take an order
4. Reads/writes data from **Google Sheets**
5. Sends the AI-generated reply back to the customer on **WhatsApp**

No human intervention required. The agent handles everything autonomously.

---

## Architecture

```
Customer (WhatsApp)
        │
        ▼
┌─────────────────────┐
│   WhatsApp Trigger  │  ← Webhook listens for incoming messages
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│      AI Agent       │  ← Core brain of the system
│                     │
│  ┌───────────────┐  │
│  │ Gemini Model  │  │  ← LLM for natural language understanding
│  └───────────────┘  │
│  ┌───────────────┐  │
│  │ Simple Memory │  │  ← Stores conversation context
│  └───────────────┘  │
│  ┌───────────────┐  │
│  │     Tools     │  │  ← get inventory / FAQ / take orders
│  └───────────────┘  │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│   Google Sheets     │  ← Data layer (read/write)
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  WhatsApp Response  │  ← Sends reply back to customer
└─────────────────────┘
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **n8n** | Workflow automation platform |
| **WhatsApp Business API** | Messaging channel (via Meta) |
| **Google Gemini** | LLM for AI agent reasoning |
| **Google Sheets** | Database for inventory, FAQ, orders |
| **n8n Simple Memory** | Short-term conversational memory |

---

## Prerequisites

Before setting up, make sure you have the following:

- **n8n** instance running (self-hosted or cloud) — [Install n8n](https://docs.n8n.io/hosting/)
- **Meta Developer Account** with WhatsApp Business API access
- **Google Cloud Project** with Sheets API enabled
- **Google Gemini API Key** from [Google AI Studio](https://aistudio.google.com/)
- A **Google Sheet** set up with the correct structure (see below)

---

## Google Sheets Structure

Create a single Google Sheet with **3 tabs/sheets**:

### Sheet 1: `inventory`

| product_name | category | price | stock_quantity | description |
|---|---|---|---|---|
| Product A | Electronics | 999 | 50 | Short description |
| Product B | Clothing | 199 | 200 | Short description |

The AI agent reads this sheet when a customer asks about product availability or pricing.

---

### Sheet 2: `faq`

| question | answer |
|---|---|
| What are your delivery times? | We deliver within 3-5 business days. |
| Do you offer refunds? | Yes, within 7 days of purchase. |
| How can I track my order? | We'll send a tracking link via WhatsApp once shipped. |

Add all common customer questions here. The agent will fuzzy-match questions and return the correct answer.

---

### Sheet 3: `orders`

| timestamp | customer_phone | product_name | quantity | status |
|---|---|---|---|---|
| *(auto-filled)* | *(auto-filled)* | *(auto-filled)* | *(auto-filled)* | pending |

This sheet is **written to** by the agent when a customer places an order. All columns are auto-populated by the workflow — you don't need to pre-fill anything.

---

## API & Credentials Setup

### 1. WhatsApp Business API (Meta)

1. Go to [Meta for Developers](https://developers.facebook.com/) and create an app
2. Add the **WhatsApp** product to your app
3. Under **WhatsApp > API Setup**, copy:
   - `Phone Number ID`
   - `WhatsApp Business Account ID`
   - `Temporary Access Token` (generate a permanent one for production)
4. Set up a **Webhook**:
   - Go to WhatsApp > Configuration > Webhook
   - Set the **Callback URL** to your n8n webhook URL (you'll get this after importing the workflow)
   - Set the **Verify Token** to any string (you'll need this in n8n)
   - Subscribe to the `messages` field

### 2. Google Gemini API

1. Visit [Google AI Studio](https://aistudio.google.com/)
2. Click **Get API Key** → Create API Key
3. Copy the key — you'll paste it into n8n credentials

### 3. Google Sheets API

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or use an existing one)
3. Enable **Google Sheets API** and **Google Drive API**
4. Go to **APIs & Services > Credentials**
5. Create **OAuth 2.0 Client ID** (Desktop App type)
6. Download the JSON credentials file
7. In n8n, add new Google Sheets credential → paste OAuth details → authorize

### 4. Setting Up Credentials in n8n

In n8n, go to **Settings > Credentials** and add:

| Credential Name | Type | Details |
|---|---|---|
| `WhatsApp API` | WhatsApp Business Cloud API | Phone Number ID + Access Token |
| `Google Gemini` | Google Gemini(PaLM) API | Paste your API Key |
| `Google Sheets` | Google Sheets OAuth2 | OAuth2 credentials from Cloud Console |

---

## n8n Setup & Workflow Import

### Step 1: Export the Workflow (if you're the owner)

In n8n, open the workflow → click the `...` menu → **Download** → saves as `.json`

### Step 2: Import the Workflow

1. Open your n8n instance
2. Click **New Workflow** → `...` menu → **Import from File**
3. Select `workflow/whatsapp_ai_agent.json`

### Step 3: Update Credentials

After importing, each node will show a credential warning. Update them:

- **WhatsApp Trigger node** → assign `WhatsApp API` credential
- **AI Agent node** → assign `Google Gemini` credential
- **Google Sheets nodes** (get inventory, FAQ, take orders) → assign `Google Sheets` credential
- In each Sheets node, update the **Spreadsheet ID** to your own Sheet's ID (found in the URL)

### Step 4: Configure Webhook

1. Open the **WhatsApp Trigger** node
2. Copy the **Production Webhook URL**
3. Paste it in your Meta App → WhatsApp → Webhook Callback URL
4. Set the same Verify Token you used during Meta setup

### Step 5: Activate

Toggle the workflow to **Active** in the top-right of n8n. The agent is now live.

---

## Node-by-Node Breakdown

### 🟢 WhatsApp Trigger
- **Type:** Webhook
- **Purpose:** Listens for incoming WhatsApp messages from the Meta API
- **Output:** Passes the message body, sender phone number, and metadata downstream

### 🤖 AI Agent
- **Type:** n8n AI Agent node
- **Purpose:** The decision engine. Takes the incoming message, uses the Gemini model to understand intent, and calls the appropriate tool
- **Connected to:** Chat Model, Memory, and Tools

### 🔵 Google Gemini Chat Model
- **Type:** LLM node (Google Gemini)
- **Purpose:** Powers the natural language understanding and response generation
- **Model used:** `gemini-pro` (or `gemini-1.5-flash` for faster responses)

### 🧠 Simple Memory
- **Type:** n8n Memory node
- **Purpose:** Maintains conversational context within the same session so the agent remembers previous messages
- **Scope:** Per session (per customer phone number)

### 📊 get inventory (Tool)
- **Type:** Google Sheets — Read
- **Sheet:** `inventory`
- **Triggered when:** Customer asks about product availability, price, or stock

### ❓ FAQ (Tool)
- **Type:** Google Sheets — Read
- **Sheet:** `faq`
- **Triggered when:** Customer asks a general support question

### 🛒 take orders (Tool)
- **Type:** Google Sheets — Append
- **Sheet:** `orders`
- **Triggered when:** Customer wants to place an order; logs their phone, product, quantity, and timestamp

### 📤 Send Message
- **Type:** WhatsApp Business Cloud — Send Message
- **Purpose:** Takes the AI agent's response and sends it back to the customer on WhatsApp

---

## How to Run

**Test Mode:**
1. Open the workflow in n8n
2. Click **Execute Workflow**
3. Send a WhatsApp message from your test phone number (registered in Meta dev console)
4. Watch the nodes execute in real-time in the n8n editor

**Production Mode:**
1. Make sure the workflow is set to **Active**
2. Ensure your webhook URL is correctly registered in Meta
3. Any WhatsApp message sent to your business number will now trigger the workflow automatically

---

## Folder Structure

```
whatsapp-ai-agent-n8n/
│
├── README.md                        # This file
│
├── workflow/
│   └── whatsapp_ai_agent.json       # Exported n8n workflow (import this)
│
├── assets/
│   └── workflow_screenshot.png      # Screenshot of the n8n workflow
│
└── docs/
    └── setup_guide.md               # Detailed setup walkthrough (optional extended guide)
```

---

## Use Cases

- **E-commerce stores** — Let customers check products and place orders on WhatsApp
- **Customer support** — Automate FAQ responses without a human agent
- **Restaurants** — Take food orders via WhatsApp
- **Service businesses** — Handle bookings or appointment queries
- **Retail** — Real-time inventory checking for customers

---

## Future Improvements

- Add **payment integration** (Razorpay / Stripe) after order placement
- Switch Google Sheets to **Supabase or PostgreSQL** for scalability
- Add **order status tracking** tool
- Integrate **voice message transcription** (Whisper API) before passing to AI Agent
- Add **multi-language support** using Gemini's multilingual capabilities
- Build an **admin dashboard** to view orders in real-time

---

## 🙋 Author

Built with n8n, Google Gemini, and WhatsApp Business API.  
Feel free to fork, improve, and star ⭐ this project!
