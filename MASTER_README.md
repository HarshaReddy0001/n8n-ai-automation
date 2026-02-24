# 🤖 N8N AI Automation Projects

A collection of **3 real-world AI automation workflows** built with n8n, Google Gemini, and various APIs. Each project solves a practical problem — from job hunting to customer support — all fully automated with zero manual effort.

---

## 📂 Projects

### 1. 🔍 [Auto-LinkedIn-Job-Tracker-Telegram alerts](./linkedin-job-hunter-n8n/)
**Auto-match resume with AI + Cover Letter + Telegram alerts**

Runs every day at 5PM — scrapes LinkedIn, scores each job against your resume using Gemini AI (0–100), generates a custom cover letter, and sends only the best matches to your Telegram.

| | |
|-|-|
| **Trigger** | Automated daily at 5PM |
| **AI** | Google Gemini — resume scoring + cover letter |
| **Output** | Telegram alerts + Google Sheets results |
| **Tools** | n8n, Gemini, Google Drive, Google Sheets, Telegram |

![LinkedIn Job Hunter](./linkedin-job-hunter-n8n/screenshots/01_workflow_overview.png)

---

### 2. 💬 [WhatsApp AI Chatbot](./whatsapp-chatbot/)
**AI-powered customer support bot with inventory + order management**

An intelligent WhatsApp bot that handles customer FAQs, checks inventory, and takes orders — all powered by Google Gemini and Google Sheets as the database.

| | |
|-|-|
| **Trigger** | WhatsApp message |
| **AI** | Google Gemini — conversational agent |
| **Output** | WhatsApp reply + Google Sheets order logging |
| **Tools** | n8n, Gemini, WhatsApp Business API, Google Sheets |

---

### 3. 🌐 [Website Portfolio Chatbot — Nathan](./website-chatbot/)
**AI assistant embedded on personal portfolio website**

Nathan is an AI chatbot on my portfolio website that answers visitor questions 24/7. Reads a Google Sheets Q&A database for quick answers and a Google Docs resume for detailed answers — responds in the visitor's language automatically.

| | |
|-|-|
| **Trigger** | Visitor message on website |
| **AI** | Google Gemini — reads resume + Q&A database |
| **Output** | Real-time chat reply on website |
| **Tools** | n8n, Gemini, Google Sheets, Google Docs, Netlify |

🌐 **Live demo:** [harsha-porfolio.netlify.app](https://harsha-porfolio.netlify.app)

---

## 🗂️ Repository Structure

```
n8n-ai-automation-projects/
│
├── README.md                          ← You are here
│
├── linkedin-job-hunter-n8n/           ← Project 1
│   ├── README.md
│   ├── requirements.md
│   ├── setup_guide.md
│   ├── Auto-LinkedIn-Job-Tracker-N8N.json
│   ├── google_sheet.xlsx
│   └── screenshots/
│
├── whatsapp-chatbot/                  ← Project 2
│   ├── README.md
│   └── whatsapp_ai_agent.json
│
└── website-chatbot/                   ← Project 3
    ├── README.md
    ├── setup_guide.md
    ├── agent_rules.md
    ├── workflow.json
    ├── nathan_qa_database.xlsx
    └── Harshavardhan_Resume_Nathan_GoogleDoc.md
```

---

## 🛠️ Tech Stack Across All Projects

| Tool | Used In |
|------|---------|
| **n8n** | All 3 projects |
| **Google Gemini** | All 3 projects |
| **Google Sheets** | All 3 projects |
| **Google Drive** | LinkedIn Job Hunter |
| **Google Docs** | Website Chatbot |
| **Telegram Bot API** | LinkedIn Job Hunter |
| **WhatsApp Business API** | WhatsApp Chatbot |
| **Netlify** | Website Chatbot |

---

## 👤 Author

**Harshavardhan Reddy**
- 🌐 Portfolio: [see my works](https://harsha-porfolio.netlify.app)
- 💼 LinkedIn: [Can connect and chit chat ](https://www.linkedin.com/in/harshareddyr)
- 🐙 GitHub: [go through my projects](https://github.com/HarshaReddy0001)

---

> Built with n8n + Google Gemini. Real workflows. Real results. ⚡
