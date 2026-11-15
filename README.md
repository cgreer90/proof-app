# Proof from past work (Catalyst Cup Shipathon, MVP #1)

Helps UX-trained career changers turn one piece of past, non-UX work into
proof an employer can judge, using only their own words.

Flow: Welcome → Step 1 Describe your work (five questions) → Step 2 Proof
candidates (use / edit wording / not accurate) → Step 3 Your proof (fill
gaps, privacy check, copy as text or download as a Word file).

## How the "AI help" works

`/api/proof-candidates` decides how to find proof:

| Situation | What runs | What the person sees |
|---|---|---|
| No `ANTHROPIC_API_KEY` | Rule-based engine | "Found by matching the words you used…" |
| Key set, AI answer passes the honesty checks | Claude | "Suggested by AI, using only your words…" |
| Key set, but AI fails or breaks a rule | Rule-based engine | A note saying the AI wasn't used |

Every result, AI or not, goes through `lib/guardrails.js`: quotes must be
word for word from the person's answers, no invented numbers, no hiring
promises, and the right number of candidates for the status.

## Project layout

```
api/proof-candidates.js   Vercel server function (POST)
lib/engine.js             Chooses AI or rules, runs guardrails
lib/rules-engine.js       No-key engine: matches the person's own sentences to UX skills
lib/system-prompt.js      Prompt used when a key is set
lib/guardrails.js         Honesty checks (shared by app and tests)
public/                   The web page: index.html, styles.css, app.js
dev-server.js             Local preview without the Vercel CLI
tests/                    Offline tests + live AI tests
```

No framework and no build step, so anyone on the team can read and edit it.
The Word download uses the `docx` library (pinned to 9.5.1), loaded from
jsDelivr only when someone clicks Download.

## Run it on your computer

Needs Node.js 18 or newer.

```
npm run dev        # http://localhost:3000, no key needed
npm test           # offline tests of the rule-based engine + guardrails
```

## Deploy to Vercel

1. Push this folder to a GitHub repo.
2. At vercel.com, choose **Add New → Project** and import the repo.
3. Leave the settings as detected: Framework Preset **Other**, no build
   command. Vercel serves `public/` and turns `api/` into a server function.
4. Click **Deploy**. Every push to `main` redeploys.

## Turn on AI later

When the team has a key (for example from a mentor):

1. In Vercel: **Project → Settings → Environment Variables**, add
   `ANTHROPIC_API_KEY`. Optionally add `MODEL` (default `claude-sonnet-5-5`).
2. Redeploy. No code changes.
3. Before demoing, run the live tests locally:
   `ANTHROPIC_API_KEY=sk-ant-... npm run test:ai` (add `RUNS=3` to catch flaky output).

Never put the key in the code, Figma, or Discord.

## Team rules for changes

- Keep the person's answers out of logs. The server only logs which rule broke.
- Any new feature should trace back to the locked problem (see the FigJam scope).
- If you change the rule-based engine or prompt, run `npm test` before pushing.

## Not in MVP #1 (held for the MMP)

Accounts and saving, progress across sessions, PDF export, the two-format
section drafting, readiness ratings and analytics, feedback and mentor sharing,
email and share links.
