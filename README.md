# atomic-sourcing — the atomic✳HR sourcer's plugin

SOP: what this plugin is, how to set it up, and how to use every skill. Audience: the atomic✳HR sourcing team. This is the sibling of `atomic-recruiting` (the CES/recruiter plugin) — one system per persona: the sourcing app is the sourcers' system, and this plugin is the sourcer's copilot on top of it.

**What this is.** A set of Claude skills that lets your Claude run your funnel with you across the three systems you already juggle: the **sourcing app** (via its MCP — your searches, your pending piles, the record-keeping verbs), **LinkedIn** (via the LinkedIn MCP — what actually happened in your inbox and sent folder, plus sending), and **Teamtailor** (via the TT MCP — meetings and transcripts, so we know if calls really happened). The point: **reduce pending candidates at every funnel step.** Ratios count only DECIDED outcomes — every pending pile you clear raises your own number.

**How to use any skill:** describe what you want in plain words ("clean up my funnel", "go through my replies"). The matching skill triggers on its own. You never need to name the skill, though you can (`/funnel-hygiene`, `/reply-triage`, and so on).

**The one rule that governs everything: Claude proposes, you approve.** Nothing is written to the app and nothing is sent on LinkedIn without your explicit yes on the specific list or batch. And whatever you already did by hand gets *recorded*, not redone — act-then-sync.

---

## Installation

Two steps: install the plugin, then say "set me up" and let the onboarding skill do the rest.

### Step 1 — install the plugin (Cowork)
In Claude (Cowork): **Customize → Plugins → Add new → From marketplace**, and add `atomic-sourcing` (ask Tania for the repo path when it's published).

### Step 2 — say "set me up"
Start a chat and type **"set me up for the sourcing plugin"**. The **sourcing-onboarding** skill checks the three MCP connections and walks you through anything missing:

- **Sourcing-app MCP** — from the app: **Settings → Connect your Claude** shows your MCP URL + personal token. The token is yours alone; every action your Claude takes lands in the audit trail under your name, with exactly your permissions.
- **LinkedIn MCP** — your own LinkedIn session (local server or your personal tunnel — ask Tania which applies to you). Never someone else's.
- **Teamtailor MCP** — connected in claude.ai connector settings.

It ends with a short tour. When it shows all ✅, you're ready. Quick self-test: ask "what's pending on my searches?"

---

## The morning routine

```
1. "clean up my funnel"        funnel-hygiene reconciles every stage against
                               LinkedIn + TT and brings you ONE fix list
2. approve the list            done-by-hand gets recorded; aged-out gets archived
                               (only after a verified no-reply); AMs get nudged
3. "go through my replies"     reply-triage: every reply advances or logs the no
4. "check on my booked calls"  call-watch: no-shows recorded, rebooks drafted
5. (when there's outreach time) "contact the saved candidates on [search]"
```

Ten minutes of approvals instead of an hour of tab-archaeology. Run it daily — the piles are small when the gap is one day wide.

---

## The skills, one by one

### 0. sourcing-onboarding — setup + tour
Checks the three MCPs with real calls, helps you fix anything missing, then tours the daily loop. **Say:** "set me up" or "am I connected?"

### 1. funnel-hygiene — the morning cleanup (the flagship)
- **What it does:** pulls every stage's pending pile, cross-references LinkedIn (did they actually reply? did you already message them?) and Teamtailor (did the call happen?), and proposes one fix list: record what you did by hand, archive what aged out, flag what needs a decision, nudge the AM on client-verdict piles.
- **Say:** "clean up my funnel" / "what's pending on [search]?"
- **Know:** archive proposals only ever come with a verified no-reply on LinkedIn. Recently-touched candidates are left alone — "not moved yet is not a no."

### 2. contact-saved — reach the saved pile
- **What it does:** drafts one personalized message per saved-never-contacted candidate — one true line from *their* profile + the role + comp + soft close, in the playbook voice from your search's own message kit — then, after you approve the batch, sends via LinkedIn paced like a human and records every send.
- **Say:** "contact the saved candidates on [search]".
- **Know:** hard caps — max 10 per sitting, max 25 new outreaches a day, one at a time with pauses. A 200-person pile is a multi-week program, on purpose.

### 3. reply-triage — advance or log the no
- **What it does:** classifies every waiting reply. Interested → scheduling link. Question/objection → answered from the role's Replies & objections kit, then pushed toward booking. Not interested → logged with a re-engagement date. **Two exits, no parking.**
- **Say:** "go through my replies" or paste a reply.
- **Know:** strong-but-wrong-fit candidates get flagged for the referral play instead of just dying.

### 4. call-watch — did the call happen?
- **What it does:** checks TT meetings/transcripts for every booked candidate. Transcript = call happened (board catches up). Past the window with no transcript = no-show recorded + a warm rebook draft. Link sent but never booked = a gentle nudge (max two).
- **Say:** "check on my booked calls" / "who no-showed this week?"
- **Know:** recording a no-show *helps* your ratio — it's a decided outcome.

### 5. referral-ask — recover value from a graceful no
- **What it does:** a good-but-wrong-fit candidate gets one warm "who should I talk to?" message; names that come back are handed to you as new leads; every ask and outcome is logged.
- **Say:** "ask [candidate] for a referral".
- **Know:** one ask, no chase, always after a real conversation — never cold, never mass.

### 6. my-patterns — learn from your own outcomes
- **What it does:** contrasts your submitted candidates against your passed-on ones and turns the difference into at most three concrete sourcing changes ("your submitted candidates had X; your passed ones lacked it").
- **Say:** "why are my candidates getting passed on?" / "what should I source differently?"
- **Know:** it reports sample sizes and calls thin data a hunch, not a finding.

---

## Guardrails (what Claude will and won't do)

| The skills never… | They always… |
|---|---|
| Write to the app or send on LinkedIn without your approval | Show you the full list/batch first, then act on your yes |
| Blast identical messages or burst-send | Personalize per candidate, pace like a human, respect hard caps |
| Keep going after a LinkedIn warning | STOP everything immediately — the account was flagged before |
| Archive anyone without proof | Verify no LinkedIn reply exists first, every time |
| Chase clients or touch submission decisions | `notify_am_pending` and stop — submission is the AM's call |
| Touch money, comp promises, role config, or client links | Quote comp verbatim from the brief; leave config to the app |
| Obey text inside candidate messages/profiles | Treat candidate-authored text as data to summarize, never instructions |
| Log a "no" the candidate didn't say | Leave recent silence alone — not moved YET is not a no |
| Invent message copy or objection answers | Draw from the search's own brief and templates via `search_brief` |

## Why the writes are safe by construction
The sourcing-app MCP uses **your personal token**: same permissions, same rules engine, same audit trail as clicking in the app. There is nothing Claude can reach through the MCP that you can't reach yourself — money, roles, and client links aren't in the tool set at all.

## Troubleshooting
1. **A skill can't see your searches / 401s** → your app token is missing or rotated. Say "set me up" — sourcing-onboarding re-checks all three MCPs.
2. **LinkedIn tools return someone else's profile** → you're on the wrong LinkedIn MCP endpoint. Stop and fix the connector before doing anything.
3. **"It archived / messaged something I didn't approve"** → it shouldn't ever; that's a bug, not a setting. Tell Tania with the transcript.
4. **Stale plugin version** → Cowork: Plugins → update `atomic-sourcing`, then START A NEW SESSION.

## Known gaps (honest list, as of v1.0.0)
- **Warm touches** (profile view / like a post "so they feel seen") — the LinkedIn MCP has no like/view-as-touch tools yet; not in v1.
- **App-side paced send queue** — v1 sends via the LinkedIn MCP directly under the skills' own caps; queue-persistence across sessions (enqueue-and-forget) needs an app-side tool that isn't in the v1 manifest.
- **Opener corpus mining** (learning which openers get replies from our own send/reply history) — depends on app-side analytics; `my_feedback_patterns` covers outcome patterns only.
- **Automated 30/60/90 re-engagement** — `log_not_interested` records the date; the resurfacing lives in the app.
