```
███████╗██╗  ██╗ ██████╗ ██████╗ ████████╗██╗  ██╗ █████╗ ███╗   ██╗██████╗     ███╗   ███╗███████╗███╗   ███╗
██╔════╝██║  ██║██╔═══██╗██╔══██╗╚══██╔══╝██║  ██║██╔══██╗████╗  ██║██╔══██╗    ████╗ ████║██╔════╝████╗ ████║
███████╗███████║██║   ██║██████╔╝   ██║   ███████║███████║██╔██╗ ██║██║  ██║    ██╔████╔██║█████╗  ██╔████╔██║
╚════██║██╔══██║██║   ██║██╔══██╗   ██║   ██╔══██║██╔══██║██║╚██╗██║██║  ██║    ██║╚██╔╝██║██╔══╝  ██║╚██╔╝██║
███████║██║  ██║╚██████╔╝██║  ██║   ██║   ██║  ██║██║  ██║██║ ╚████║██████╔╝    ██║ ╚═╝ ██║███████╗██║ ╚═╝ ██║
╚══════╝╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═════╝     ╚═╝     ╚═╝╚══════╝╚═╝     ╚═╝
```

<p align="center"><strong>Semantic compression for Claude — no setup, no API key, no install</strong></p>
<p align="center">60–80% fewer tokens &nbsp;·&nbsp; persistent library &nbsp;·&nbsp; diff · merge · summarise &nbsp;·&nbsp; four document domains</p>

<p align="center">
  <a href="SKILL.md">Docs</a> &nbsp;·&nbsp;
  <a href="shorthand-mem.skill">Install</a> &nbsp;·&nbsp;
  <a href="https://github.com/jai2010/shorthand-mem/issues">Issues</a>
</p>

> **Looking for the MCP-backed version with local model routing?**
> See [shorthand-mem-mcp](https://github.com/jai2010/shorthand-mem-mcp) — same commands, compression routed to Haiku separately from your Claude session.

---
## Demo

<p align="center">
  <img src="https://raw.githubusercontent.com/jai2010/shorthand-mem/main/shorthand-mem-demo.png" alt="shorthand-mem compression demo" width="860">
</p>
<p align="center"><i>880 tokens compressed to 195 — 78% reduction. Syntax-highlighted SHM output with fidelity score.</i></p>
---

## What It Does

**shorthand-mem** compresses verbose documents into a structured symbolic notation (SHM) and reconstructs them on demand. It runs entirely inside Claude — install the `.skill` file, type commands in chat, everything else is handled.

No Python. No API key. No local server. Just Claude.

```
You type:  SQUEEZE PRD <your document>
Claude:    → compresses to SHM notation
           → runs fidelity check (dates, amounts, entities captured)
           → saves to memory + exports .shm file
           → 78% fewer tokens next time you need this context
```

---

## Installation

1. Download [`shorthand-mem.skill`](./shorthand-mem.skill)
2. Go to **Claude.ai → Settings → Skills**
3. Click **Install Skill** and upload the file

That's it.

---

## Commands

| Command | What It Does |
|---|---|
| `SQUEEZE PRD \| CODE \| PAPER \| MEETING <text>` | Compress document into SHM notation |
| `EXPAND [label or SHM block]` | Reconstruct full prose |
| `DIFF [label1] [label2]` | Structural diff between two versions |
| `MERGE [label1] [label2] ...` | Combine squeezes under SCOPE headers |
| `SUMMARISE [label]` | 5-bullet plain-English summary (lossy) |
| `LIST SQUEEZES` | Library table with compression stats |
| `RECALL [label]` | Retrieve a saved squeeze |
| `SEARCH [keyword or RISK( or DEC:]` | Keyword or token-level search |
| `DELETE [label]` | Remove a squeeze (asks for confirmation) |
| `EXPORT ALL` | Dump full library to one `.shm` file |

---

## Token Reference

### Universal — All Domains

| Token | Meaning |
|---|---|
| `OBJ(Name)` | Core entity, system, module, or actor |
| `SCOPE: Name` | Module or domain boundary |
| `GOAL(Action)` | Primary intent |
| `REQ: action()` | Strict functional mandate |
| `RULE: constraint` | Business logic, rate limit, or guardrail |
| `DEP(X)` | Dependency — `++` for hard/critical |
| `RISK(Factor)` | Failure mode — `++` for high severity |
| `ERR(Type) → THROW(Behaviour)` | Failure boundary |

**Conditional:** `IF (cond) → THEN (action) ! EXCEPT (override)`

### PAPER Domain

| `HYP` | `METH` | `RESULT` | `LIMIT` |
|---|---|---|---|
| Hypothesis | Methodology | Outcome | Limitation |

### MEETING Domain

| Token | Meaning |
|---|---|
| `DEC(Decision)` | Decision made |
| `ACT(Owner → Action → Date)` | Action item with owner and due date |
| `ESC(Risk → Owner)` | Escalation raised |
| `BLOCK(Item → Blocker → ETA)` | Blocker with expected resolution |

---

## Full Example: PRD

### Input (~880 tokens)

> The Notification Delivery Service routes alerts across in-app, email, and push. Critical notifications must be delivered within 5 seconds; Standard within 60 seconds. Email via SendGrid — on 5xx or 3-second timeout, retry 3× with exponential backoff from 500ms. After 3 failures → DeadLetterQueue + PagerDuty. Push via FCM — if `push_enabled=false`, skip silently. FCM token-expired → delete stale token + skip. Hard dependency on UserPreferences — if unavailable, suspend all and queue for replay. Rate limit: 50 notifications/user/hr. Excess silently dropped + EventLog entry. Critical failures escalate to engineering lead.

### Output (~195 tokens — 78% reduction)

```shm
@ASSET:PRD | @TARGET:60-80% | @CCR:a3f2b1c9 | @DATE:2025-06-24

OBJ(Notification_Delivery_Svc)
GOAL: Multi_channel_alert_routing(in-app, email, push)
IF (priority == Critical) → THEN (deliver <= 5s)
IF (priority == Standard) → THEN (deliver <= 60s)
REQ: Email_via_SendGrid
ERR(SendGrid_5xx || timeout>3s) → RETRY x3 | backoff(500ms, exponential)
IF (retry_count == 3 && fail) → WRITE(DeadLetterQueue) + PAGE(PagerDuty)
RULE: user.push_enabled == false → SKIP silently
ERR(FCM_token_expired) → DELETE(UserDevices.token) + SKIP
DEP(UserPreferences_Svc)++ → IF (unavailable) → SUSPEND_ALL + QUEUE_replay
REQ: RateLimit == 50 notifications/user/hr
IF (rate_exceeded) → DROP silently + WRITE(EventLog.RateLimitExceeded)
RISK(Critical_delivery_failure)++ → ESCALATE(engineering_lead)
```

🎯 Fidelity: 📅 0/0 dates · 💰 2/2 amounts · 🚦 0/0 statuses · 👤 6/6 entities
📊 Compression: ~880 → ~195 tokens (78% reduction)

---

## MEETING Example

```
SQUEEZE MEETING
Decision: Migrate billing to Stripe by Q3 2025.
Action: Rohan to complete API contract by Jul 15.
Action: Priya to sign off on compliance review by Jul 10.
Blocker: Legal approval pending — ETA Jul 8.
Escalation: Stripe SLA risk escalated to CTO.
```

```shm
@ASSET:MEETING | @TARGET:60-80% | @CCR:b7e3d2f1 | @DATE:2025-06-24

DEC(Migrate billing to Stripe by Q3 2025)
ACT(Rohan → Complete API contract → Jul 15)
ACT(Priya → Sign off compliance review → Jul 10)
BLOCK(Legal_approval → Pending → Jul 8)
ESC(Stripe_SLA_risk → CTO)
```

🎯 Fidelity: 📅 3/3 dates · 💰 0/0 amounts · 🚦 0/0 statuses · 👤 3/3 entities

---

## DIFF Example

```
DIFF MEETING_steerco_2025-06-17 MEETING_steerco_2025-06-24

Summary: 🟢 2 added · 🔴 1 removed · 🟡 1 changed · ⚪ 8 unchanged

🟢 Added
  + ACT(Priya → Sign off compliance review → Jul 10)
  + ESC(Stripe_SLA_risk → CTO)

🔴 Removed
  - BLOCK(Procurement → Vendor shortlist → Jun 28)

🟡 Changed
  before: DEC(Evaluate Stripe migration)
  after:  DEC(Migrate billing to Stripe by Q3 2025)
```

---

## Token-Level SEARCH

```
SEARCH RISK(     → all risks across every squeeze in your library
SEARCH DEC(      → all decisions across every meeting note
SEARCH ACT(Rohan → all actions assigned to Rohan
SEARCH REQ:      → all requirements across all PRDs
```

---

## vs. shorthand-mem-mcp

| | shorthand-mem (this) | [shorthand-mem-mcp](https://github.com/jai2010/shorthand-mem-mcp) |
|---|---|---|
| Setup | Install `.skill` only | `pip install` + `python install.py` |
| API key needed | ❌ No | ✅ Yes (for Haiku/Sonnet calls) |
| Compression model | Current Claude session | Haiku (separate call, cheaper) |
| Model selection for EXPAND | Manual tip (switch in settings) | Haiku / Sonnet / Opus — remembered |
| Large doc chunking | Warning + guidance | Automatic chunking and merge |
| Best for | Most users | High-volume or pipeline use |

---

## License

MIT
