# Voics CSM API — Setup Guide

## What this is
An API that powers the Voics CSM training tool. A CSM types in a client situation, and the API returns a full diagnosis: health score, bottleneck, prescribed action, routing, and a ready-to-use script.

---

## Step 1 — Get your Groq API key (5 min)

1. Go to [console.groq.com](https://console.groq.com)
2. Sign up / log in
3. Click **API Keys** in the left sidebar
4. Click **Create API Key**
5. Copy the key — you'll need it in Step 3

---

## Step 2 — Deploy to Render (free hosting, 10 min)

1. Go to [render.com](https://render.com) and sign up with GitHub
2. Click **New** → **Web Service**
3. Choose **Deploy from GitHub** → connect your repo
   - (If you don't have a GitHub repo yet: go to github.com → New Repository → upload these files)
4. Set these settings:
   - **Name:** voics-csm-api
   - **Runtime:** Node
   - **Build Command:** `npm install`
   - **Start Command:** `node index.js`
5. Click **Advanced** → **Add Environment Variable**
   - Key: `GROQ_API_KEY`
   - Value: (paste your key from Step 1)
6. Click **Create Web Service**
7. Wait ~2 minutes. Render gives you a URL like: `https://voics-csm-api.onrender.com`
8. Copy that URL — you need it for Step 3

---

## Step 3 — Test it works (2 min)

Open your terminal or use a tool like [hoppscotch.io](https://hoppscotch.io) (free, no install needed):

**POST** to `https://your-render-url.onrender.com`

Body (JSON):
```json
{
  "client_name": "Test Client",
  "program": "Incubator",
  "situation": "Client hasn't booked a call in 3 weeks, says they are too busy, but is still posting in the community"
}
```

You should get back a full diagnosis with health_score, bottleneck, prescribed_action, script, etc.

---

## Step 4 — Connect to your Lovable UI

Copy this prompt and paste it into your Lovable project chat:

---

**LOVABLE PROMPT (copy exactly):**

```
Connect the diagnosis form to a live API. When the CSM submits a client situation, send a POST request to: [YOUR RENDER URL HERE]

Request body should be JSON with these fields:
- situation (string, required) — the client situation text
- program (string) — "Incubator" or "Apex" from the dropdown
- client_name (string) — the client name field

The API returns a JSON object with these fields:
- health_score (Green/Yellow/Red)
- health_reasoning
- bottleneck
- diagnosis
- prescribed_action
- route_to
- script
- timeline

Display the results in a clear card layout:
- Health score as a colored badge (green/yellow/red)
- Bottleneck as a bold headline
- Diagnosis as body text
- Prescribed action in a highlighted box
- Route to as a pill/tag
- Script in a copyable text box with a copy button
- Timeline as a small label

Show a loading spinner while waiting for the API response. Show a friendly error message if the API fails.
```

---

## API Reference

**Endpoint:** `POST /`

**Request:**
```json
{
  "client_name": "Mary Scott",
  "program": "Apex",
  "situation": "Hasn't submitted accountability form in 2 weeks, missed last group call, responded to my DM with one word"
}
```

**Response:**
```json
{
  "health_score": "Red",
  "health_reasoning": "Three simultaneous disengagement signals in under 2 weeks",
  "bottleneck": "Silent churn risk — client is withdrawing from the program",
  "diagnosis": "...",
  "prescribed_action": "...",
  "route_to": "Darren",
  "script": "...",
  "timeline": "Within 24 hours"
}
```

---

## Cost
Groq free tier: 1,000 requests/day. Enough for 2-5 CSMs in normal usage. If you scale, paid tier is ~$10-15/month.
