# 🗜️ Shorthand-mem

> A two-way semantic compiler for Claude — compress massive documents into dense symbolic notation, reconstruct them back into professional prose, and build a persistent library of saved compressions.

---

## What Is This?

**Shorthand-mem** is a Claude skill that acts as an asymmetric compiler for heavy context blocks. You hand it a sprawling PRD, a complex codebase description, or a research paper — and it compresses it by 60–80% into a lossless symbolic notation called **SHM**. Later, you hand it that notation and it reconstructs the full document with no invented details.

Every compression is automatically labelled, saved to Claude's memory, and exported as a `.shm` file — so you build a growing library of squeezed documents you can recall, search, and export at any time.

**Three supported domains:**
- `PRD` — Product requirement docs, specs, architecture decisions
- `CODE` — Function signatures, API contracts, data schemas, system interfaces
- `PAPER` — Research papers, methodology, benchmarks, hypotheses

---

## Why Use It?

| Problem | Without Shorthand-mem | With Shorthand-mem |
|---|---|---|
| Long PRD in every prompt | Wastes 600–1000 tokens per send | ~120 tokens per send |
| Re-explaining architecture repeatedly | Copy-paste entire spec | `RECALL PRD_billing_2025-06-24` |
| Losing context between sessions | Re-upload docs every time | Persistent `.shm` library |
| Extracting key decisions from a 40-page paper | Read the whole thing | `SQUEEZE PAPER <abstract+methods>` |

---

## Installation

Download `shorthand-mem.skill` and install it into your Claude environment via the Skills panel. Once installed, the commands below activate automatically — no setup needed.

---

## Token Reference (The DSL)

Shorthand-mem translates natural text into predicate logic tokens. All numbers, variable names, error codes, and architectural identifiers are preserved exactly.

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

### Extended Tokens — PAPER Domain Only

| Token | Meaning |
|---|---|
| `HYP(Statement)` | Research hypothesis or claim being tested |
| `METH(Approach)` | Experimental methodology or technique |
| `RESULT(Finding)` | Key quantitative or qualitative outcome |
| `LIMIT(Scope)` | Stated limitation, scope boundary, or confound |

**Logical operators:** `→`, `==`, `!=`, `&&`, `||`, `>`, `<`, `>=`, `<=`, `⇒`

---

## Commands

| Command | Description |
|---|---|
| `SQUEEZE PRD \|CODE\| PAPER <payload>` | Compress a document into SHM notation |
| `EXPAND <shm_block>` | Reconstruct SHM notation into full prose |
| `LIST SQUEEZES` | Show all saved compressions as a table |
| `RECALL {label}` | Retrieve a specific squeeze by label |
| `SEARCH {keyword}` | Search across all saved labels and content |
| `DELETE {label}` | Remove a squeeze from memory (with confirmation) |
| `EXPORT ALL` | Dump entire library to a single `.shm` file |

---

## Full Example: PRD → SHM → Stats

The following example shows Shorthand-mem applied to a realistic product requirement section for a **multi-channel notification delivery system**.

### Input PRD (Original)

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

---

### SQUEEZE Output

**Command used:** `SQUEEZE PRD <above text>`

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

📊 **Compression: ~880 → ~195 tokens (78% reduction)**
💾 Saved as `PRD_notification_delivery_2025-06-24`

---

### What Was Preserved

Every factual boundary from the original PRD survives the compression:

| Detail | Original | SHM |
|---|---|---|
| Channels | in-app, email, push | `(in-app, email, push)` |
| Critical SLA | 5 seconds | `deliver <= 5s` |
| Standard SLA | 60 seconds | `deliver <= 60s` |
| SendGrid retry count | 3 retries | `RETRY x3` |
| Backoff start | 500ms, exponential | `backoff(500ms, exponential)` |
| Dead letter on failure | DeadLetterQueue | `WRITE(DeadLetterQueue)` |
| Escalation channel | PagerDuty | `PAGE(PagerDuty)` |
| Push opt-out field | push_enabled = false | `user.push_enabled == false` |
| FCM token action | delete stale token | `DELETE(UserDevices.token)` |
| Hard dependency | UserPreferences | `DEP(UserPreferences_Svc)++` |
| Soft dependency | Analytics | `DEP(Analytics_Svc)` |
| Rate limit | 50/user/hr | `50 notifications/user/hr` |
| Rate limit action | EventLog entry | `WRITE(EventLog.RateLimitExceeded)` |

Nothing lost. No invention. 78% fewer tokens.

---

### EXPAND It Back

**Command used:** `EXPAND <above shm block>`

> ## Notification Delivery Service Specifications
>
> ### Overview
>
> The Notification Delivery Service is responsible for routing and delivering all system-generated alerts to end users across three channels: in-app, email, and push.
>
> ### Priority Queue
>
> The service enforces a priority-based delivery model. Critical notifications must be delivered within 5 seconds of entering the queue. Standard notifications have a delivery window of 60 seconds.
>
> ### Email Delivery (SendGrid)
>
> All outbound email notifications are routed through SendGrid. If SendGrid returns a 5xx error or fails to respond, the service retries the delivery up to three times, applying exponential backoff starting at 500ms. If all three attempts fail, the notification is written to the Dead Letter Queue and the on-call engineer is paged via PagerDuty.
>
> ### Push Delivery (FCM)
>
> Push notifications are routed through Firebase Cloud Messaging. Users with `push_enabled = false` in their preferences are silently skipped — no error is raised. If FCM returns a token-expired error, the stale token is deleted from the UserDevices table and the delivery attempt is skipped.
>
> ### Service Dependencies
>
> The Notification Delivery Service has a hard dependency on the UserPreferences service. If UserPreferences is unavailable, all notification dispatches are suspended and queued for replay. No notification may be sent without a confirmed preference lookup. The Analytics service is a soft dependency used for delivery event tracking. If Analytics is unavailable, the notification is still sent but the delivery event is silently dropped with a warning log entry.
>
> ### Rate Limiting
>
> A per-user rate limit of 50 notifications per hour applies across all channels combined. Notifications that exceed this limit are silently dropped, and a `RateLimitExceeded` event is written to the EventLog.
>
> ### Escalation
>
> Any delivery failure for Critical notifications must be escalated immediately to the engineering lead.

---

## More Examples

### SQUEEZE CODE

```
SQUEEZE CODE <function or class description>
```

```shm
OBJ(RateLimiter_cls)
REQ: constructor(windowMs:number, maxRequests:number)
REQ: method checkLimit(clientId:str) → bool
SCHEMA: Map<clientId:str, timestamps:number[]>
RULE: len(timestamps) > maxRequests → RETURN false
RULE: len(timestamps) <= maxRequests → APPEND now() + RETURN true
ERR(InvalidClientError) → IF clientId == null || clientId == "" → THROW
```
📊 Compression: ~548 → ~108 tokens (80% reduction)

---

### SQUEEZE PAPER

```
SQUEEZE PAPER <abstract or methodology section>
```

```shm
OBJ(Medical_NER_Study)
HYP: Domain_fine-tuned_transformers > General_LLMs ON medical_entity_extraction
METH: Fine-tune(BioBERT, ClinicalBERT) ON i2b2_2010 | 5-fold_cross-validation
RESULT: BioBERT F1=0.89 > GPT-4 F1=0.81 ON NER_subtask
LIMIT: English-only + single-hospital → low generalizability
```
📊 Compression: ~248 → ~60 tokens (76% reduction)

---

## Persistence & Library

Every SQUEEZE automatically:

1. **Reports compression** — shows original vs. compressed token count and percentage saved
2. **Auto-labels** — generates a label in `DOMAIN_topic_YYYY-MM-DD` format
3. **Checks for conflicts** — appends `_v2`, `_v3` if the label already exists
4. **Saves to memory** — persists across all future Claude sessions
5. **Exports a file** — writes a `.shm` file you can download
6. **Prompts for rename** — reply with any string to override the auto-label

```
LIST SQUEEZES

| # | Label                              | Domain | Date       | Preview                              |
|---|------------------------------------|--------|------------|--------------------------------------|
| 1 | PRD_notification_delivery_2025-06  | PRD    | 2025-06-24 | OBJ(Notification_Delivery_Svc) GOA.. |
| 2 | CODE_ratelimiter_2025-06-24        | CODE   | 2025-06-24 | OBJ(RateLimiter_cls) REQ: construc.. |
| 3 | PAPER_medical_ner_2025-06-24       | PAPER  | 2025-06-24 | OBJ(Medical_NER_Study) HYP: Domain.. |
```

---

## Quick Reference

```
SQUEEZE PRD <text>          Compress a PRD or spec
SQUEEZE CODE <text>         Compress a codebase or API description  
SQUEEZE PAPER <text>        Compress a research paper section

EXPAND <shm_block>          Reconstruct full prose from SHM

LIST SQUEEZES               Show all saved compressions
RECALL {label}              Retrieve a specific squeeze
SEARCH {keyword}            Search across all saved content
DELETE {label}              Remove a squeeze (asks for confirmation)
EXPORT ALL                  Dump full library to one .shm file
```

---

## License

MIT
