# Adidas WhatsApp AI Agent — Customer Support Agent

> An intelligent, AI-powered WhatsApp customer support agent built for Adidas, capable of handling real-world customer queries including order tracking, FAQs, ticket creation, and returns — all through WhatsApp, with zero human intervention.

---

## Table of Contents

- [About the Project](#about-the-project)
- [Why I Built This](#why-i-built-this)
- [How I Built It](#how-i-built-it)
- [Tech Stack](#tech-stack)
- [Workflow Architecture](#workflow-architecture)
- [Features & Functionalities](#features--functionalities)
- [How It Works (Step by Step)](#how-it-works-step-by-step)
- [Setup & Installation](#setup--installation)
- [Environment Variables](#environment-variables)
- [Testing](#testing)
- [Results](#results)
- [Future Improvements](#future-improvements)
- [Author](#author)

---

## About the Project

The **Adidas WhatsApp AI Agent** is a fully automated, AI-powered customer support chatbot that handles real customer queries for Adidas directly through WhatsApp. Built using **n8n** (a workflow automation platform), **Groq's ultra-fast LLM**, and **Twilio's WhatsApp API**, this agent acts as a 24/7 virtual support representative.

Customers can simply send a WhatsApp message to get instant help — no waiting in queues, no hold music, no delays.

---

## Why I Built This

Customer support is one of the biggest pain points for large retail brands like Adidas. Traditional support systems suffer from:

- Long response times (hours or even days)
- High operational costs due to large support teams
- Inconsistent answers across different agents
- No 24/7 availability

I built this project to solve a **real-world business problem** — replacing or augmenting a traditional support team with an AI agent that can handle the most common customer queries instantly, accurately, and at scale. This is not a toy project or a learning exercise; it is designed to work in a production environment and deliver real business value.

---

## How I Built It

The build process went through the following phases:

### 1. Planning
Identified the four most common customer support request types for a retail brand like Adidas:
- Order tracking
- FAQ answering (sizing, returns policy, store locations, etc.)
- Support ticket creation
- Returns and refund handling

### 2. Designing the Workflow
Mapped out the agent flow on paper before building:
```
Customer WhatsApp Message
        ↓
   Twilio Trigger
        ↓
   Edit Fields (format & clean input)
        ↓
   AI Agent (Groq LLM with memory)
        ↓
   Tool Selection (order_records / order_tracking / create_ticket)
        ↓
   Send WhatsApp Reply via Twilio
```

### 3. Building in n8n
- Set up the Twilio Trigger node to receive incoming WhatsApp messages
- Added an Edit Fields node to extract and format the customer's message
- Configured the AI Agent node with Groq as the language model and gave it a system prompt defining its persona as an Adidas support agent
- Connected three custom tools: order record search, order tracking, and ticket creation
- Wired the final output back to Twilio to send the reply to the customer

### 4. Debugging & Iteration
- Ran multiple test executions, resolving errors across 6+ debug cycles
- Fixed issues with data formatting, tool routing, and response formatting
- Final version achieved a successful execution in **987ms**

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **n8n** | Visual workflow automation platform — the backbone of the entire agent |
| **Groq (LLM)** | Ultra-fast AI language model powering the agent's responses |
| **Twilio** | WhatsApp Business API — receives and sends WhatsApp messages |
| **AI Agent Node** | n8n's built-in agent that routes queries to the right tools |
| **Custom Tool Nodes** | order_records, order_tracking, create_ticket |

---

## Workflow Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                  ADIDAS WHATSAPP AI AGENT                    │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Twilio Trigger] → [Edit Fields] → [AI Agent] → [Send SMS] │
│                           ↑              │                    │
│                     Formats raw     ┌────┴────┐              │
│                      message        │  Tools  │              │
│                                     │─────────│              │
│                                     │ order_  │              │
│                                     │ records │              │
│                                     │─────────│              │
│                                     │ order_  │              │
│                                     │tracking │              │
│                                     │─────────│              │
│                                     │ create_ │              │
│                                     │ ticket  │              │
│                                     └─────────┘              │
│                         Model: Groq Chat Model                │
└─────────────────────────────────────────────────────────────┘
```

---

## ✅ Features & Functionalities

### 1. Order Tracking
Customers can ask about the status of their orders. The agent queries the `order_tracking` tool using the order ID and returns real-time status updates.

**Example:**
> "Where is my order #ADI-2024-5892?"
> → "Your order is currently out for delivery and will arrive by tomorrow."

---

### 2. 📋 Order Records Search
The agent can look up order history and details using the `order_records` tool.

**Example:**
> "What did I order last month?"
> → Fetches and summarizes previous order details.

---

### 3. Support Ticket Creation
When an issue cannot be resolved automatically, the agent creates a support ticket via the `create_ticket` tool and confirms the ticket number to the customer.

**Example:**
> "My shoes arrived damaged."
> → "I've created support ticket #TKT-8821 for you. Our team will reach out within 24 hours."

---

### 4. FAQ Answering
The AI agent handles common questions using its built-in knowledge:
- Return and refund policies
- Sizing guides
- Store locations and hours
- Delivery timelines
- Product availability

---

### 5. Returns & Refunds
Customers can initiate return or refund requests through the agent, which guides them through the process and creates a ticket if needed.

---

### 6. Conversation Memory
The AI Agent node includes memory, allowing it to maintain context across multiple messages in the same conversation — so customers don't have to repeat themselves.

---

## How It Works (Step by Step)

1. **Customer sends a WhatsApp message** to the Adidas support number (powered by Twilio)
2. **Twilio Trigger** fires in n8n and captures the incoming message payload
3. **Edit Fields node** extracts and formats the relevant fields (customer number, message body, etc.)
4. **AI Agent** receives the formatted message and decides how to respond:
   - If it's a general question → answers directly using the Groq LLM
   - If order info is needed → calls `order_records` or `order_tracking` tool
   - If an issue needs escalation → calls `create_ticket` tool
5. **Response is sent back** to the customer via Twilio's WhatsApp API

---

## Setup & Installation

### Prerequisites
- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- [Twilio account](https://www.twilio.com/) with WhatsApp Business enabled
- [Groq API key](https://console.groq.com/)

### Steps

1. **Clone this repository**
```bash
git clone https://github.com/sagramanisahil/adidas-whatsapp-ai-agent.git
cd adidas-whatsapp-ai-agent
```

2. **Import the workflow into n8n**
   - Open your n8n instance
   - Go to **Workflows → Import**
   - Upload `workflow.json`

3. **Add your credentials in n8n**
   - Twilio: Account SID + Auth Token
   - Groq: API Key

4. **Configure your Twilio WhatsApp sandbox or production number**
   - Point the webhook URL to your n8n Twilio Trigger URL

5. **Activate the workflow** in n8n

---

## 🔐 Environment Variables

Create a `.env` file based on `.env.example`:

```env
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_NUMBER=whatsapp:+1XXXXXXXXXX
GROQ_API_KEY=your_groq_api_key
N8N_WEBHOOK_URL=https://your-n8n-instance.com/webhook/xxxx
```

> **Never commit your `.env` file to GitHub.** It is listed in `.gitignore`.

---

## Testing

The agent was tested across multiple real-world scenarios:

| Test Case | Input | Expected Output | Status |
|-----------|-------|-----------------|--------|
| Order tracking | "Where is my order #1234?" | Order status with delivery date | ✅ Pass |
| FAQ - Return policy | "Can I return my shoes?" | Return policy explanation | ✅ Pass |
| Ticket creation | "My package is missing" | Ticket number confirmation | ✅ Pass |
| Returns initiation | "I want to return my order" | Return process steps | ✅ Pass |

### Execution Performance
- Latest successful execution: **987ms**
- Workflow Version: **3**
- Execution ID: **#186**
- Total debug iterations before stable version: **6+**

---

## 📈 Results

-  Average response time: **under 1 second**
-  Handles **4 major support categories** autonomously
-  Works natively on **WhatsApp** — no app download required for customers
-  Maintains **conversation context** across messages
-  Fully **no-code/low-code** — built entirely in n8n

---

## Future Improvements

- [ ] Add product recommendation based on customer history
- [ ] Integrate with Adidas's live inventory system
- [ ] Add multilingual support (Urdu, Arabic, etc.)
- [ ] Build a dashboard for monitoring ticket volumes and response times
- [ ] Add sentiment analysis to flag frustrated customers for human escalation
- [ ] Connect to a CRM (e.g., Salesforce or HubSpot)

---

## Author

**[Sahil Kumar]**
- GitHub: [@your_username](https://github.com/sagramanisahil)
- LinkedIn: [your LinkedIn](https://linkedin.com/in/sahil-kumar-sagramani)

---


> Built with ❤️ using n8n + Groq + Twilio | Solving real customer support problems with AI
