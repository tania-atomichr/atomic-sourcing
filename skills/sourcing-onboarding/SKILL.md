---
name: sourcing-onboarding
description: >-
  Onboard a sourcer to the atomic-sourcing plugin — check and guide the three MCP connections it
  needs (the sourcing-app MCP via the app's "Connect your Claude" screen, the LinkedIn MCP, and
  the Teamtailor MCP), verify each one with a real call, then give a short tour of the daily loop.
  Use when someone just installed the plugin, asks "how do I set this up / get started / connect
  everything", "what can this do", "am I connected", or when a sourcing skill fails because an MCP
  is missing or a tool call errors with auth problems.
---

# sourcing-onboarding — get connected, then learn the loop

Two jobs: **(A) make sure all three MCPs are live under YOUR identity**, and **(B) show the
sourcer what they can now do**. Run A first (fix what's missing), then B.

The whole plugin rides on three MCPs. Your Claude only sees what YOU can see and can only do
what YOU can do — the sourcing-app token is personal, so every write lands in the app's audit
trail under your name, exactly as if you had clicked.

## A. Connection check (run each, report ✅ / ❌ with the fix)

Go through these in order. For each, actually test it, then tell the user the result and, if
it's missing, exactly how to connect it. Do not assume — check.

1. **Sourcing-app MCP** — the foundation: your searches, your pending piles, the write verbs.
   - Check: call `my_searches`. If it returns your active searches, it's live. If the tool
     doesn't exist or errors with 401, it's not connected or the token is wrong.
   - If missing: in the sourcing app (sourcing.hireatomic.com) go to **Settings → Connect your
     Claude**. That screen shows two things: the **MCP URL** and your **personal token**. Add it
     as a connector in claude.ai settings (or `claude mcp add --transport http sourcing <URL>`
     in an interactive session, with the token as the auth header the screen shows). The token
     is YOURS — never share it, never paste someone else's. If the screen shows no token, ask
     Tania to enable MCP access for your account.
   - The token carries your permissions and nothing more: sourcers cannot touch money, role
     config, or client links through the MCP because they can't through the app.

2. **LinkedIn MCP** — reading your inbox/sent messages and sending, on YOUR LinkedIn session.
   - Check: call `mcp__linkedin__get_my_profile` (or `get_inbox` with limit 1). It should
     return YOUR profile — if it returns someone else's, stop: you're on the wrong session.
   - If missing: each sourcer has their own LinkedIn MCP endpoint (locally
     `http://127.0.0.1:8377/mcp`, or your personal `<name>-li.recruithink.com/mcp` tunnel).
     Ask Tania which applies to you and register it as a connector. Never point at another
     person's tunnel.

3. **Teamtailor MCP** — meetings, transcripts, candidate timelines, stages.
   - Check: a small call like `list_meetings` or `list_jobs`. If unavailable or unauthed, it's
     not connected.
   - If missing: connect the Teamtailor MCP in claude.ai connector settings (or `/mcp` in an
     interactive session). This session cannot run the OAuth flow for the user — point them to
     the right place and wait.

**Report as a checklist** so they see exactly what's ready and what to fix:
```
✅ Sourcing app (connected as you — 3 active searches found)
✅ LinkedIn (your session — profile verified)
❌ Teamtailor — connect it in claude.ai settings, then re-run setup
```
If anything is ❌, say which skills won't work yet: no app MCP = nothing works; no LinkedIn =
funnel-hygiene can't verify replies and contact-saved can't send; no TT = call-watch is blind.

## B. What you can do (the tour — show after setup)

Give a short, concrete tour. The core loop:

> **The morning routine:** say "clean up my funnel". I check every stage's pending pile,
> cross-reference LinkedIn and Teamtailor for what actually happened, and bring you one fix
> list to approve. Whatever you already did by hand gets recorded; whatever needs a nudge gets
> drafted. Clearing pending is not admin — ratios count only DECIDED outcomes, so every pile
> you clear raises your own number.

The building blocks, with an example ask for each:
- **funnel-hygiene** — "clean up my funnel" / "what's pending on [search]?"
- **contact-saved** — "let's contact the saved candidates on [search]"
- **reply-triage** — "go through my replies" (advance or log the no — two exits, no parking)
- **call-watch** — "check on my booked calls"
- **referral-ask** — "ask [candidate] for a referral" (good person, wrong fit)
- **my-patterns** — "what do my submitted vs passed candidates look like?"

**The rules to tell every new sourcer:**
- Claude proposes, you approve. Nothing is written to the app and nothing is sent on LinkedIn
  without your explicit yes on the specific batch.
- Sending is paced like a human: small batches, spread out, hard caps. Any LinkedIn warning
  stops everything — the account has been flagged before; this is non-negotiable.
- Submission is the AM's call. Client-verdict stages get a nudge to the AM
  (`notify_am_pending`), never a chase from you.

Point them to the full SOP: the plugin README (what Claude will and won't do, the morning
routine, the guardrails table).

## Do not
- Share or store anyone's sourcing-app token anywhere but their own connector config.
- Point a sourcer at another person's LinkedIn MCP endpoint — one session per human.
- Claim a connection works without testing it with a real call.
