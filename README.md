```
███████╗██╗  ██╗ ██████╗ ██████╗ ████████╗██╗  ██╗ █████╗ ███╗   ██╗██████╗     ███╗   ███╗███████╗███╗   ███╗
██╔════╝██║  ██║██╔═══██╗██╔══██╗╚══██╔══╝██║  ██║██╔══██╗████╗  ██║██╔══██╗    ████╗ ████║██╔════╝████╗ ████║
███████╗███████║██║   ██║██████╔╝   ██║   ███████║███████║██╔██╗ ██║██║  ██║    ██╔████╔██║█████╗  ██╔████╔██║
╚════██║██╔══██║██║   ██║██╔══██╗   ██║   ██╔══██║██╔══██║██║╚██╗██║██║  ██║    ██║╚██╔╝██║██╔══╝  ██║╚██╔╝██║
███████║██║  ██║╚██████╔╝██║  ██║   ██║   ██║  ██║██║  ██║██║ ╚████║██████╔╝    ██║ ╚═╝ ██║███████╗██║ ╚═╝ ██║
╚══════╝╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝╚═════╝     ╚═╝     ╚═╝╚══════╝╚═╝     ╚═╝
```

<p align="center"><strong>The native prompt-layer semantic compression engine for Claude</strong></p>
<p align="center">60–80% fewer tokens &nbsp;·&nbsp; native skill &nbsp;·&nbsp; persistent memory library &nbsp;·&nbsp; lossless reconstruction</p>

<p align="center">
  <a href="SKILL.md">Docs</a> &nbsp;·&nbsp;
  <a href="shorthand-mem.skill">Install</a> &nbsp;·&nbsp;
  <a href="https://github.com/jai2010/shorthand-mem/issues">Issues</a>
</p>

---

**shorthand-mem** compresses verbose PRDs, prompt buffers, codebases, and research papers directly inside Claude's environment — before they exhaust your context window. Same precision. A fraction of the token cost. Every compression is saved to memory, exported as a `.shm` file, and reconstructable back to full prose on demand.

---

## How It Works

```text
  Raw Input — PRD / Codebase / Research Paper
                        │
                        ▼
  ┌─────────────────────────────────────────────────────┐
  │              shorthand-mem Core Engine              │
  ├─────────────────────────────────────────────────────┤
  │  1. Domain Router    (PRD · CODE · PAPER)           │
  │  2. Token Mapper     (prose → predicate DSL)        │
  │  3. Dependency Graph (surfaces hidden trade-offs)   │
  │  4. Density Compactor (60–80% token reduction)      │
  └─────────────────────────────────────────────────────┘
                        │
                        ▼
       High-Density SHM Token Stream
  (Lossless · Reconstructable · Persistently Saved)
```

---

## Key Features

- **Native Skill** — Runs inside Claude as an imported `.skill` file. No external APIs, no wrapper latency, no extra setup.
- **Lossless Compression** — All numbers, error codes, variable names, SLAs, and architectural identifiers are preserved exactly. EXPAND reconstructs the original with full declarative confidence.
- **Persistent Memory Library** — Every SQUEEZE is auto-labelled, saved to Claude's cross-session memory, and exported as a `.shm` file. Build a growing library you can `RECALL`, `SEARCH`, and `EXPORT ALL`.
- **Three Domain Modes** — Dedicated compression strategies for PRDs, codebases, and research papers, each with domain-specific token extensions.
- **Prompt-Injection Safe** — Structured metadata wrapping keeps raw, untrusted data isolated so compressed variables don't bleed into execution environments.
- **KV-Cache Friendly** — Standardised output structures stabilise prefix alignment, maximising downstream API cache hit rates.

---

## Why Use It?

| Problem | Without shorthand-mem | With shorthand-mem |
|---|---|---|
| Long PRD in every prompt | Wastes 600–1000 tokens per send | ~120 tokens per send |
| Re-explaining architecture repeatedly | Copy-paste entire spec every time | `RECALL PRD_billing_2025-06-24` |
| Context lost between sessions | Re-upload docs every session | Persistent `.shm` library across all chats |
| Extracting decisions from a 40-page paper | Read the whole thing | `SQUEEZE PAPER <abstract+methods>` |
| Bloated agent coordination records | Full history in every agent turn | Compressed state replay via SHM |

---

## Installation

1. Download [`shorthand-mem.skill`](./shorthand-mem.skill)
2. In Claude, go to **Settings → Skills**
3. Click **Install Skill** and upload the file

Once installed, all commands below activate automatically — no configuration needed.

---

## Commands

| Command | What It Does |
|---|---|
| `SQUEEZE PRD <text>` | Compress a product spec or architecture doc |
| `SQUEEZE CODE <text>` | Compress a codebase, API, or function description |
| `SQUEEZE PAPER <text>` | Compress a research paper section |
| `EXPAND <shm_block>` | Reconstruct full prose from any SHM block |
| `LIST SQUEEZES` | Show your full compression library as a table |
| `RECALL {label}` | Retrieve a specific squeeze by label |
| `SEARCH {keyword}` | Search across all saved labels and content |
| `DELETE {label}` | Remove a squeeze from memory (with confirmation) |
| `EXPORT ALL` | Dump your entire library to a single `.shm` file |

---

## Token Reference (The DSL)

Shorthand-mem translates natural language into structured predicate logic tokens. All identifiers, version strings, error codes, and numeric boundaries are preserved verbatim.

### Universal Tokens — All Domains

| Token | Meaning |
|---|---|
| `OBJ(Name)` | Core entity, system, module, or actor |
| `GOAL(Action)` | Primary intent or objective |
| `REQ(Text)` | Absolute functional or technical requirement |
| `DEP(X)` | Upstream/downstream dependency or blocker |
| `RISK(Factor)` | Vulnerability or failure mode — `++` marks high severity |
| `RULE(Condition)` | Invariant logic pattern or architectural trade-off |
| `ERR(Code/Msg)` | Explicit failure boundary or error handler |

### Extended Tokens — PAPER Domain

| Token | Meaning |
|---|---|
| `HYP(Statement)` | Research hypothesis or claim being tested |
| `METH(Approach)` | Experimental methodology or technique |
| `RESULT(Finding)` | Key quantitative or qualitative outcome |
| `LIMIT(Scope)` | Stated limitation, scope boundary, or confound |

**Logical operators:** `→` `==` `!=` `&&` `||` `>` `<` `>=` `<=` `⇒`

---

## Full Example: PRD → SHM → Expand

The following is a real compression run on a multi-channel notification delivery system spec.

### Original PRD (~880 tokens)

> **Feature: Notification Delivery Service**
>
> The Notification Delivery Service (NDS) is responsible for routing and delivering all system-generated alerts to end users across three channels: in-app, email, and push. The service must support priority-based queuing, where Critical notifications bypass the standard queue and are delivered within 5 seconds, while Standard notifications may be delivered within 60 seconds.
>
> All outbound email delivery must be routed through SendGrid. If SendGrid returns a 5xx error or fails to respond within 3 seconds, the service must retry up to 3 times with exponential backoff starting at 500ms. After 3 failed retries, the notification must be written to the DeadLetterQueue and the on-call engineer must be paged via PagerDuty.
>
> Push notifications must be routed through Firebase Cloud Messaging (FCM). The service must respect per-user opt-out preferences stored in the UserPreferences table — any user with push_enabled = false must be silently skipped, with no error raised. FCM token expiry must be handled gracefully: if FCM returns a token-expired error, the service must delete the stale token from the UserDevices table and skip the delivery attempt.
>
> The NDS has a hard runtime dependency on the UserPreferences service. If UserPreferences is unavailable, all notification dispatches must be suspended and queued for replay — no notification may be sent without a confirmed preference lookup. There is also a soft dependency on the Analytics service for delivery event tracking; if Analytics is unavailable, the notification is still sent but the delivery event is dropped with a warning log.
>
> The system must enforce a per-user rate limit of 50 notifications per hour across all channels combined. Exceeding this limit must result in the notification being silently dropped and a RateLimitExceeded event written to the EventLog. This is a high-risk, high-visibility feature — any delivery failure for Critical notifications must be escalated immediately to the engineering lead.

### SQUEEZE Output (~195 tokens)

**Command:** `SQUEEZE PRD <above>`

```shm
OBJ(Notification_Delivery_Svc)
GOAL: Multi_channel_alert_routing(in-app, email, push)

REQ: Priority_Queue
IF priority == Critical → deliver <= 5s
IF priority == Standard → deliver <= 60s

REQ: Email_via_SendGrid
ERR(SendGrid_5xx || timeout>3s) → RETRY x3 | backoff(500ms, exponential)
IF retry_count == 3 && fail → WRITE(DeadLetterQueue) + PAGE(PagerDuty)

REQ: Push_via_FCM
RULE: user.push_enabled == false → SKIP silently, no ERR
ERR(FCM_token_expired) → DELETE(UserDevices.token) + SKIP

DEP(UserPreferences_Svc)++ → IF unavailable → SUSPEND_ALL + QUEUE_for_replay
RULE: NO notification WITHOUT confirmed_preference_lookup

DEP(Analytics_Svc) → IF unavailable → SEND_notification + DROP(delivery_event) + WARN_LOG

REQ: RateLimit == 50 notifications/user/hr (all channels)
IF rate_exceeded → DROP silently + WRITE(EventLog.RateLimitExceeded)

RISK(Critical_delivery_failure)++ → ESCALATE(engineering_lead)
```

📊 **~880 → ~195 tokens — 78% reduction**
💾 Auto-saved as `PRD_notification_delivery_2025-06-24`

### What Was Preserved

| Detail | Original | SHM |
|---|---|---|
| Delivery channels | in-app, email, push | `(in-app, email, push)` |
| Critical SLA | 5 seconds | `deliver <= 5s` |
| Standard SLA | 60 seconds | `deliver <= 60s` |
| SendGrid retry count | 3 retries | `RETRY x3` |
| Backoff parameters | 500ms, exponential | `backoff(500ms, exponential)` |
| Failure escalation | DeadLetterQueue + PagerDuty | `WRITE(DeadLetterQueue) + PAGE(PagerDuty)` |
| Push opt-out field | push_enabled = false | `user.push_enabled == false` |
| FCM token action | Delete stale token | `DELETE(UserDevices.token)` |
| Hard dependency | UserPreferences | `DEP(UserPreferences_Svc)++` |
| Soft dependency | Analytics | `DEP(Analytics_Svc)` |
| Rate limit | 50/user/hr | `50 notifications/user/hr` |
| Rate limit action | EventLog entry | `WRITE(EventLog.RateLimitExceeded)` |

Nothing invented. Nothing lost. 78% fewer tokens.

### EXPAND Output

**Command:** `EXPAND <above shm block>`

> ## Notification Delivery Service Specifications
>
> ### Overview
> The Notification Delivery Service routes and delivers system-generated alerts across three channels: in-app, email, and push.
>
> ### Priority Queue
> Critical notifications must be delivered within 5 seconds. Standard notifications have a 60-second delivery window.
>
> ### Email Delivery (SendGrid)
> All email is routed through SendGrid. On a 5xx error or response timeout, the service retries up to three times with exponential backoff starting at 500ms. After three failures, the notification is written to the Dead Letter Queue and PagerDuty pages the on-call engineer.
>
> ### Push Delivery (FCM)
> Push is routed through Firebase Cloud Messaging. Users with `push_enabled = false` are silently skipped. On a token-expired error, the stale token is deleted from UserDevices and the delivery attempt is abandoned.
>
> ### Service Dependencies
> UserPreferences is a hard dependency — if unavailable, all dispatches are suspended and queued for replay. No notification may be sent without a confirmed preference lookup. Analytics is a soft dependency — if unavailable, the notification still sends but the delivery event is dropped with a warning log.
>
> ### Rate Limiting
> A 50 notifications/user/hour limit applies across all channels. Overflow is silently dropped; a `RateLimitExceeded` event is written to the EventLog.
>
> ### Escalation
> Any Critical notification delivery failure must be escalated immediately to the engineering lead.

---

## More Examples

### SQUEEZE CODE

```shm
OBJ(RateLimiter_cls)
REQ: constructor(windowMs:number, maxRequests:number)
REQ: method checkLimit(clientId:str) → bool
SCHEMA: Map<clientId:str, timestamps:number[]>
RULE: len(timestamps) > maxRequests → RETURN false
RULE: len(timestamps) <= maxRequests → APPEND now() + RETURN true
ERR(InvalidClientError) → IF clientId == null || clientId == "" → THROW
```
📊 ~548 → ~108 tokens (80% reduction)

### SQUEEZE PAPER

```shm
OBJ(Medical_NER_Study)
HYP: Domain_fine-tuned_transformers > General_LLMs ON medical_entity_extraction
METH: Fine-tune(BioBERT, ClinicalBERT) ON i2b2_2010 | 5-fold_cross-validation
RESULT: BioBERT F1=0.89 > GPT-4 F1=0.81 ON NER_subtask
LIMIT: English-only + single-hospital → low generalizability
```
📊 ~248 → ~60 tokens (76% reduction)

---

## Persistence & Library

Every SQUEEZE automatically:

1. **Reports compression** — original vs. compressed token count and % saved
2. **Auto-labels** — `DOMAIN_topic_YYYY-MM-DD` format (e.g. `PRD_billing_2025-06-24`)
3. **Checks for conflicts** — appends `_v2`, `_v3` if the label already exists, never overwrites silently
4. **Saves to memory** — persists across all future Claude sessions
5. **Exports a file** — downloads a `.shm` file
6. **Prompts for rename** — reply with any plain string to override the auto-label

```
LIST SQUEEZES

| # | Label                             | Domain | Date       | Preview                             |
|---|-----------------------------------|--------|------------|-------------------------------------|
| 1 | PRD_notification_delivery_2025-06 | PRD    | 2025-06-24 | OBJ(Notification_Delivery_Svc)...   |
| 2 | CODE_ratelimiter_2025-06-24       | CODE   | 2025-06-24 | OBJ(RateLimiter_cls) REQ: const...  |
| 3 | PAPER_medical_ner_2025-06-24      | PAPER  | 2025-06-24 | OBJ(Medical_NER_Study) HYP: Dom...  |
```

---

## Quick Reference

```
SQUEEZE PRD <text>        Compress a PRD or spec
SQUEEZE CODE <text>       Compress a codebase or API description
SQUEEZE PAPER <text>      Compress a research paper section

EXPAND <shm_block>        Reconstruct full prose from SHM

LIST SQUEEZES             Show all saved compressions
RECALL {label}            Retrieve a specific squeeze
SEARCH {keyword}          Search across all saved content
DELETE {label}            Remove a squeeze (asks for confirmation)
EXPORT ALL                Dump full library to one .shm file
```

---

## License

MIT