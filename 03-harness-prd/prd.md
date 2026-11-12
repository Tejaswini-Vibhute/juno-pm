# AI PRD · Juno

_Module 3 · Lab 2 · AI PRD Builder. Proposed V1 requirements; numeric targets are design assumptions to validate during testing, not measured performance._

## Problem & user

RocketShip Product Managers need a reliable way to turn scattered customer feedback, engineering alerts, and sales signals into prioritized, evidence-backed actions. Juno is an AI Associate Product Manager that synthesizes those inputs, identifies delivery and commercial risks, drafts or creates Jira tickets for verified new work, and proposes PRD changes for PM approval. It never independently commits product scope or sends customer communications.

## The harness

| Surface | V1 specification |
|---|---|
| 01 Context | Retrieve scoped Slack, Jira, Notion, support/interview, CRM, and web-review evidence; preserve source IDs and freshness. |
| 02 Tools | Authorized reads and internal drafts are automatic; Jira/Notion writes are confirmation-gated; external sends are unavailable. |
| 03 Loop | Maximum 7 reasoning/action steps per request; escalate on contradictions, missing critical sources, or 2 failed tool calls. |
| 04 Memory | Session working context plus team-scoped issue/decision log; no cross-customer leakage; source systems remain authoritative. |
| 05 Permissions | `read auto` · `draft auto` · `write confirm` · `send blocked`. |
| 06 Verification | Check claim-level evidence, business figures, duplicates, permissions, and action receipts; fail safely and visibly. |

## 01 Context · Data Requirements

**Required sources, and exclusions**

- Juno Skill File: role, task, constraints, Markdown format, and escalation rules.
- Slack: relevant engineering and stakeholder threads, including `#backend-monitoring` for reporting-service incidents; capture message permalink/ID.
- Jira: existing bugs, tasks, status, owners, and dependencies; capture issue keys.
- Notion: current PRD and approved roadmap sections relevant to the request; capture page ID and version.
- Customer feedback: provided interview transcript, support ticket, and relevant web reviews; preserve artifact/ticket identifiers.
- CRM: relevant account records and the **Sales Pipeline Snapshot** for renewal, expansion, and deal blockers; preserve record ID and update timestamp.
- For the lab, **Raw Signals** and **Sales Pipeline Snapshot** are supplied artifacts, not live Slack/Jira/CRM records. Cite their artifact names and sections; mark absent native IDs `NEEDS VERIFICATION` rather than inventing them.
- **Excluded:** unrelated private messages, unrelated customer accounts, historical PII, unapproved external communications, and unsupported competitor claims. The CEO's AI-feature request is a stakeholder signal, not proof of feasibility or an approved roadmap commitment.

**How fresh must it be**

- Re-fetch Jira status, Notion PRD version, and CRM deal/renewal details **at the start of every action-oriented run** and immediately before any write.
- For active incidents and blockers, use Slack/support updates no older than **15 minutes** at decision time; older information is labeled stale and rechecked.
- For non-urgent research, refresh retrieved records at least once per **24 hours**; display source timestamps.
- If a critical source is unreachable, retry at most **2 times**, then produce a clearly labeled partial analysis and **do not create tickets or update PRDs** based on unverified assumptions. Escalate to the PM.

## 02 Tools · System Capabilities

**The verb list (proposed interfaces)**

| Function | Side-effect class | Purpose |
|---|---|---|
| `search_slack(query)` | READ | Retrieve relevant engineering/stakeholder messages and IDs. |
| `search_jira(query)` / `get_jira_issue(key)` | READ | Check existing work, duplicates, and status. |
| `read_notion_prd(page_id)` | READ | Retrieve current requirements and version. |
| `read_customer_feedback(scope)` | READ | Retrieve interviews, support tickets, and web reviews. |
| `read_crm_accounts(account_ids)` | READ | Verify commercial impact and commitments. |
| `draft_findings(evidence)` | DRAFT | Produce ranked insights, risks, and citations. |
| `draft_jira_issue(evidence)` | DRAFT | Prepare a proposed bug/task with acceptance criteria. |
| `draft_prd_diff(page_id,changes)` | DRAFT | Show a proposed requirement change without applying it. |
| `create_jira_issue(approved_draft)` | WRITE — CONFIRM | Create a nonduplicate ticket after PM confirmation. |
| `apply_notion_prd_diff(approved_diff)` | WRITE — CONFIRM | Apply the exact PM-approved diff to the current version. |
| `record_decision(approval,source_ids)` | WRITE — CONFIRM | Record approved decision and audit reference. |

These are proposed capabilities, not a claim that the prototype already has live integrations. Simulated actions must be labeled **SIMULATED** and must not return fabricated real Jira keys.

**Deliberate omissions, and why**

- No `send_slack_message()`, `send_email()`, or `reply_to_customer()`: external communication may create unauthorized commitments or expose sensitive information; route to the human PM.
- No `approve_roadmap_change()` or `promise_ship_date()`: product strategy and commitments remain human decisions.
- No `modify_crm_deal()` or `close_jira_issue()`: these could change commercial records or hide unresolved work outside V1's scope.

## 03 Loop · AI Costs & Latency

**Turn ceiling and escalation trigger**

1. Interpret PM request and define requested deliverable.
2. Retrieve scoped sources and timestamps.
3. Cluster related signals and assess urgency, impact, and dependencies.
4. Search Jira for duplicates and check contradictory claims.
5. Draft prioritized findings, tickets, risks, and PRD diffs.
6. Run verification gate and obtain PM confirmation for writes.
7. Perform approved action, verify returned record/version, and summarize outcome.

- **Ceiling:** 7 internal steps per request; no more than **2 retries per failed tool**. On reaching the limit, return completed verified findings and unresolved items to the PM.
- **Immediate escalation:** contradictory engineering and sales claims; missing evidence for ARR/deadlines; suspected customer PII leakage; legal/contract/regulatory matters; a critical source unavailable after retries; or a proposed roadmap commitment.
- Example: Sales claims that pipeline-failure Slack alerts exist, but Engineering says the feature is not built. Mark **NEEDS CLARIFICATION** and flag the customer-expectation risk; never present it as shipped.

**Latency and cost target**

- **Proposed V1 targets, pending measurement:** p95 **≤30 seconds** for a read-only synthesis of up to 20 retrieved records; p95 **≤60 seconds** for a multi-source draft with duplicate checks, excluding time waiting for PM approval.
- **Proposed cost ceiling:** **$0.20 per read-only analysis** and **$0.50 per multi-source draft** in model/tool usage, excluding third-party SaaS fees. Log actual cost and latency per request; stop optional retrieval when the budget is reached and disclose incomplete coverage.
- PM approval waiting time is not counted as model latency. Avoid repeating retrievals when source versions have not changed.

## 04 Memory · Data Requirements

**What persists, at what scope**

- **Turn/session:** current request, retrieved excerpts, provisional clusters, and draft output; visible only to the authorized session.
- **Team/project:** issue-to-source mappings, Jira keys, open risks, PM-approved decisions, and audit references; shared only with authorized RocketShip product-team members.
- **Customer/account:** commercial facts stay in CRM and are retrieved under account permissions; do not copy customer PII into general team memory or carry one customer's private details into another account's analysis.
- Jira, Notion, and CRM remain authoritative. Memory provides pointers and summaries, not a replacement for live records.

**Expiry and write rules**

- Discard temporary working excerpts after **24 hours** or session completion, whichever comes first, unless a documented retention requirement applies.
- Revalidate incident status after **15 minutes** and commercial/project facts after **24 hours**; expired summaries cannot authorize actions.
- Retain approved decision records and action receipts under RocketShip's applicable retention policy; **90 days is a proposed default** for non-policy-bound issue summaries pending PM/security approval.
- Store only verified findings or explicitly labeled hypotheses. Human PM corrections and current source-system records outrank earlier AI summaries; preserve superseded decisions for audit rather than silently overwriting them.

## 05 Permissions · AI Risks & Mitigations

**The permission tier**

| Side-effect class | Tier | Rationale |
|---|---|---|
| READ | AUTO | Scoped authorized retrieval does not alter source records; enforce least-privilege access. |
| DRAFT | AUTO | Internal findings, tickets, and PRD diffs are reversible until acted upon. |
| WRITE | CONFIRM | Creating Jira tickets or editing PRDs changes shared workflows; PM confirms the exact target and content first. |
| SEND | BLOCKED | External messages and customer promises can create irreversible reputational or contractual effects. |

**Justification and exceptions**

- Juno may independently analyze, prioritize, flag risks, and prepare drafts. It may **create a Jira ticket only after PM confirmation**, duplicate verification, and source checks; this resolves the ambiguity between “create tickets” in the Skill File and the write-confirm permission tier.
- PRD modifications require explicit PM approval of the proposed diff and a recheck that the underlying Notion page version has not changed.
- Juno cannot approve strategy, commit delivery dates, or handle legal/contract/regulatory decisions. Escalate those to the PM.
- All writes require an audit record with approver, source IDs, target record, timestamp, and action result. If the approval mechanism is unavailable, output a draft instead of writing.

## 06 Verification · AI Testing & Measurement

**The check before output ships**

| Check | Method and release bar |
|---|---|
| Evidence | Deterministic citation check: **100% of factual claims** have a resolvable source ID or artifact section; unsupported claims are removed or flagged. |
| Financial and date accuracy | Compare **100% of cited ARR values, renewal dates, and deadlines** with retrieved CRM/source text. |
| Duplicates | Jira search before **every** new ticket; potential duplicates go to PM review rather than automatic creation. |
| Priority | Every High/Critical recommendation names impact, urgency, and engineering dependency or explicitly states missing evidence. |
| Product promises | Cross-check claimed features against approved PRD/Jira/engineering evidence; unverified sales promises are not described as shipped. |
| Authorization | Every write has matching explicit PM approval; compare the approved diff with the proposed action. |
| Execution | Confirm returned Jira key or Notion version and re-read changed record where possible; no success claim without receipt. |

**Failure behaviour**

- If evidence is incomplete or contradictory, output: **`NEEDS CLARIFICATION — I cannot verify [specific claim] from the available sources. No write was performed. PM action needed: [specific question or decision].`**
- If Jira duplicate check fails, **do not create a ticket**; provide the draft and request PM review.
- If approval is missing, **do not change the PRD**; show the exact proposed diff and request approval.
- If a tool action fails or its outcome cannot be verified, mark **ACTION UNCONFIRMED**, report the error, and avoid blind retries that could duplicate writes.
- Never invent a citation, ARR figure, Jira key, customer commitment, or successful action to pass verification. Return a labeled partial result and escalate.

## Eval plan

_Module 6 will formalize a golden dataset, pass thresholds, and regression cadence. Initial scenarios: CSV export failure linked to API instability and Initech expansion; Pearson admin 403 and renewal risk; Acme Okta SSO blocker and unapproved Slack-alert/TikTok promises; ambiguous Globex deal status; CEO AI prototype request versus engineering constraints._

## Out of scope

Any capability not in the tool verb list, any `SEND` action, independent roadmap approvals, external customer communications, and legal/contract/regulatory decisions.
