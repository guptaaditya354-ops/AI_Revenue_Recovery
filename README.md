# AI Revenue Recovery Engine

An AI-powered system that detects failed payments in real time, classifies *why* they failed,
and automatically executes the right recovery action — turning declined transactions back
into revenue.

Built for the Razorpay Ideathon — **AI Revenue Recovery** track.

---

## The problem

A large share of failed payments on any gateway are not lost sales — they're recoverable.
A card that timed out, an OTP typed wrong, a bank server that blinked for two seconds:
these are not "the customer changed their mind," they're **operational failures with a fix**.
Most systems treat every failed payment the same way (or don't retry at all), leaving real
revenue on the table every day.

## The solution

This engine sits on the failed-payment stream and, for every decline:

1. **Detects** the failure the moment it happens.
2. **Classifies** the root cause (insufficient funds, bank timeout, OTP mismatch, network
   drop, expired card, risk block, limit exceeded) using a confidence-scored rules engine —
   the same categories a real payments risk team would use, built to be swapped for a
   trained ML classifier later.
3. **Chooses a recovery strategy** matched to that specific cause — instant retry, delayed
   retry, alternate payment method, or SMS nudge — instead of one generic "please try again."
4. **Executes and tracks** the outcome, so you can see recovered revenue, recovery rate, and
   at-risk amount as live numbers, not a monthly report.

Cases with genuinely low recovery odds (e.g. a bank risk block) are correctly flagged as
**not** safe to retry, instead of being force-retried and damaging the customer relationship —
this restraint is itself part of the pitch.

## What's in this repo

```
revenue-recovery/
├── index.html          # Vite entry point
├── package.json
├── vite.config.js
├── src/
│   ├── main.jsx         # React entry
│   └── App.jsx          # Entire application (dashboard, feed, AI logic)
└── README.md
```

Everything is in one React component (`src/App.jsx`) by design — a judge or teammate can
read the whole system top to bottom in one file: the failure taxonomy, the simulated
transaction stream, the recovery pipeline state machine, and the UI.

## Running it

You need [Node.js](https://nodejs.org) 18+ installed.

```bash
cd revenue-recovery
npm install
npm run dev
```

This opens the app at `http://localhost:5173`. It runs entirely in the browser — no backend,
no API keys, no database required for the demo.

To build a static production bundle (e.g. to deploy or zip for submission):

```bash
npm run build
```

The output goes to `dist/` — you can open `dist/index.html` directly or host it anywhere
static (Vercel, Netlify, GitHub Pages).

## How the demo works

- New failed transactions stream in automatically every few seconds, each with a random
  customer, amount, payment method, and failure cause.
- Each transaction moves through a visible pipeline: **Detected → Classifying → Action
  executed → Recovered / Not recovered.**
- Click any transaction in the left feed to see the AI's full reasoning on the right: the
  failure cause, its recovery confidence score, the exact strategy chosen, and the live
  pipeline state.
- The header shows running totals: revenue recovered today, recovery rate, amount still at
  risk, and transactions currently being worked.

## Where this goes next (good for Q&A)

- **Swap the rules engine for a trained model.** The failure taxonomy here is hand-coded
  with realistic confidence scores; in production this would be a classifier trained on
  historical decline data (issuer, BIN, time of day, amount, network) to predict both
  failure cause and recovery probability.
- **Real retry orchestration.** Hook the "action" step into Razorpay's actual retry/route
  APIs and SMS/WhatsApp nudge delivery instead of the simulated timer.
- **Merchant-level tuning.** Different merchants (subscriptions vs. one-time e-commerce)
  have different tolerances for retry aggressiveness — this should be a configurable policy,
  not a fixed rule.
- **Feedback loop.** Every recovered/lost outcome should retrain the confidence model, so
  the system gets sharper over time instead of staying static.

## Tech stack

- React 18 + Vite (fast dev server, instant reload, trivial to build/deploy)
- No external UI library — all styling is hand-built inline for full control over the
  fintech-terminal look, so there's no dependency risk during the demo
- Pure frontend simulation — zero backend setup, which means zero deployment risk on
  presentation day
