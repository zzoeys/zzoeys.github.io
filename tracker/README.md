# Track My Portfolio — Setup Guide

## What's in this folder

| File | Purpose | Where it goes |
|---|---|---|
| `index.html` | The full app | Upload to `github.com/zzoeys/zzoeys.github.io/upload/master/tracker` |
| `worker.js` | Cloudflare Worker for Yahoo Finance | Paste into Cloudflare dashboard |
| `uk-theme-iq.jsx` | Standalone copy of the React code | Reference file for reading the code |

---

## What works in v1

| Feature | How it works |
|---|---|
| **Yahoo Finance live prices + MTD** | Via Cloudflare Worker — fully automatic |
| **News Intel (FT articles)** | Manual — analyse in a Claude chat, paste JSON output into app |
| **Portfolio Tracker** | Manual — extract holdings via Claude chat, paste JSON output into app |
| **Themes / Stocks / ETFs / Scenarios** | Built-in — no APIs needed |

No Anthropic API key required. Yahoo Finance access is free.

---

## Step 1 — Deploy the Cloudflare Worker (5 min)

1. Go to [dash.cloudflare.com](https://dash.cloudflare.com) and sign up / log in (free, no card)
2. Left sidebar → **Workers & Pages**
3. Click **Create application** → **Create Worker**
4. Name it `tracker-api` (or anything you like — remember the name)
5. Click **Deploy** to create the default worker
6. Click **Edit code**
7. Delete everything in the editor
8. Copy all of `worker.js` and paste it in
9. Click **Save and deploy**

**Note your worker URL** — it'll look like:
`https://tracker-api.YOURNAME.workers.dev`

(Find it under **Settings → Triggers** if you forget.)

---

## Step 2 — Update index.html with your Worker URL (1 min)

1. Open `index.html` in any text editor
2. Search for: `tracker-api.YOURNAME.workers.dev`
3. Replace with your actual worker URL from Step 1
4. Save

---

## Step 3 — Upload to GitHub Pages (1 min)

1. Open `github.com/zzoeys/zzoeys.github.io/upload/master/tracker`
2. Drag `index.html` into the upload box
3. Scroll down → **Commit changes**
4. Wait ~60 seconds
5. Visit `https://zzoeys.github.io/tracker/` — the app is live

---

## Using News Intel & Portfolio

### News Intel — analysing FT articles

1. Open a new Claude chat (claude.ai)
2. Paste in the FT article (gift link or text)
3. Ask Claude: *"Analyse this for Track My Portfolio. Return only JSON in this format:"* and paste the JSON shape from the News Intel input box
4. Copy Claude's JSON response
5. In your app, **News Intel tab → Add Article** → paste the JSON → **Save Article**

### Portfolio — extracting holdings from screenshots

1. Open a new Claude chat
2. Upload your brokerage screenshot
3. Ask: *"Extract all holdings as a JSON array. Each item should have: ticker, name, shares, avgCost, currentValue, gainLoss, currency."*
4. Copy the JSON
5. In your app, **Portfolio tab → Add Holdings** → paste the JSON → **Save Holdings**

---

## Testing the Worker

After deploying, test the worker directly by pasting this in your browser:

```
https://YOUR-WORKER.workers.dev/yahoo/quote?symbols=AAPL
```

You should see JSON with Apple's price. If it returns an error, the worker isn't deployed correctly.

---

## Where to edit things

### The Worker URL
In `index.html` near the top, search for `WORKER_URL`. Big commented block, easy to spot.

### The Worker code
In `worker.js`. Two routes: `/yahoo/quote` (current prices) and `/yahoo/chart/<ticker>` (historical for MTD).

### Theme data
In `index.html` search for `const themes = [` — that's the array of all 10 investment themes.

### Major code sections
Search for `SECTION:` to jump between the major chunks of the app:
- API Configuration
- Live Price Hook
- News Intel
- Portfolio Tracker

---

## Costs

| Service | Free tier | Will you exceed it? |
|---|---|---|
| Cloudflare Workers | 100,000 requests/day | No |
| GitHub Pages | 100 GB/month bandwidth | No |

Everything is free at your usage level.

---

## Troubleshooting

**MTD equals YTD on stock cards (no green LIVE badge)**
→ Yahoo fetch failed. Test the worker directly:
  `https://YOUR-WORKER.workers.dev/yahoo/quote?symbols=NVDA`
→ Should return JSON. If it errors, the worker isn't deployed or the URL is wrong.

**"Invalid JSON" when pasting article/holdings**
→ Make sure you're pasting only the JSON Claude returned (no extra text or markdown fences). The app strips ` ```json ` and ` ``` ` fences automatically, but extra prose before the JSON will break it.

**App shows "Loading" forever**
→ JSX syntax error. Open browser console (F12) — should show the line.