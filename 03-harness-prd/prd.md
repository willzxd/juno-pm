# AI PRD · Juno

_Module 3 · AI PRD. Built from the Lab 1 Harness Sketch in the AI PRD Builder, then clarified for review._

## Problem & user

RocketShip PMs receive product signals across interviews, monitoring, engineering, and planning documents. Before deciding what to investigate, they need a short brief that separates observations from possible explanations, shows the source for each claim, and names missing evidence. Juno prepares that first synthesis as a tool-using copilot. The PM decides priority, scope, delivery commitments, and external communication.

This PRD specifies the harness for the [Module 2 insight-brief prototype](../02-prototype/prototype.md) and follows Juno's [skill file](../01-prompting/skill-file.md). The export example draws on the course's [RocketShip Company Context Documents](https://app.productschool.com/lms/student/content/aipmc%402026.1/outputs/certifications/aipmc/2026.1/Modules/M3%20-%20Company%20Context%20Documents.html). The prototype's six-month note is synthetic and is not a RocketShip source.

## Architecture decision

| Decision factor | Assessment for this workflow |
|---|---|
| Blast radius | The output is a private PM draft; a wrong claim could still mislead a later decision, so it must show its evidence limits. |
| Recoverability | A draft can be discarded. Juno has no shared-record write or send tool. |
| Verifiability | A machine can check citation links and format; a PM must judge the finding's meaning and priority. |
| Repetition | PMs repeat this synthesis during product reviews, so a small reusable tool set is useful. |
| Latency | A PM waits for the brief; the pilot target is p95 at or below 20 seconds. |

**Choice: tool-using copilot.** It gets a short loop and read/draft actions, while the PM remains responsible for decisions.

## The harness

| Surface | Specification |
|---|---|
| 01 Context | For each brief, Juno receives the PM's question and reads only sources that PM may access. |
| 02 Tools | Four scoped actions: search, batch-read, draft, and verify. |
| 03 Loop | Tool-using copilot; five turns at most, including one optional follow-up before drafting. |
| 04 Memory | Keep source passages, the working draft, and PM corrections only within the current PM task. |
| 05 Permissions | read auto · draft auto · write blocked · send blocked |
| 06 Verification | Before display, check every factual claim against an authorized, retrievable source passage and show its source ID and date. |

## 01 Context · Data Requirements

**Required sources, and exclusions**

For each brief, Juno receives the PM's question and reads only sources that PM may access. For the export question, the required sources are RocketShip Raw Signals (Artifact 1, user interview; Artifact 5, backend monitoring), the Engineering Capacity Note, the Product Strategy One-Pager, and the Product Quarterly Roadmap. Add the Sales Pipeline Snapshot only if the PM asks about deal impact and has access. Exclude the synthetic six-month prototype note from RocketShip evidence. Exclude unrelated CEO AI requests, SSO, admin-access, and TikTok items from this export brief unless the PM expands the question. Juno must not query the reporting database. Keep the small, stable skill file in the prompt, and fetch these changing documents on demand. Do not load an entire corpus or build a vector index for this narrow question.

**How fresh must it be**

Fetch the latest authorized version and record its source ID, event date, and last-updated time at the start of each run. Keep historical user reports in their original time frame. For claims about current engineering capacity, roadmap, or deal status, require a source updated within seven days; otherwise say that current status is unconfirmed. If a source cannot be reached, name the gap and do not silently use a cached copy. Use the latest approved strategy version and flag a missing version date.

## 02 Tools · System Capabilities

**The verb list**

Juno has four model-callable actions: scoped search, batch reading of authorized sources, private brief drafting, and source-based verification.

- `search_sources(scope, query)` — READ: search only sources the PM can access.
- `read_sources(source_ids)` — READ: fetch up to five authorized sources in one batch, with IDs, dates, versions, and supporting passages.
- `draft_insight_brief(question, excerpts)` — DRAFT: create a private Markdown brief, up to 500 words, with scope, at most three findings, and a PM handoff.
- `check_brief(brief, source_spans)` — DRAFT: check citations, evidence labels, and required format before display.

Opening a citation is a PM interface action, not a model call. No action in this list changes a shared system.

**Deliberate omissions**

Do not provide direct reporting-database queries: Engineering says the legacy reporting system is fragile and cannot take extra AI load this quarter. Do not provide ticket, roadmap, CRM, email, Slack-post, or publishing tools. Those would change shared state or create an external commitment. Copying Markdown remains a deliberate PM action in the interface, not a model tool.

## 03 Loop · AI Costs & Latency

**Turn ceiling and escalation**

Juno is a tool-using copilot capped at five model/tool turns per brief: search, batch-read up to five authorized sources, optionally make one targeted follow-up read before drafting, draft, and verify. Without the follow-up it uses four turns. If verification finds an unsupported claim, return a partial brief rather than starting another loop. Also stop after two failed reads, an unresolved source conflict, no supporting evidence, or the turn limit. Tell the PM exactly what needs checking. This shape fits a private, reversible draft; citations can be checked mechanically, but a PM must judge meaning and priority. The work recurs, and a PM is waiting for the answer.

**Latency and cost target**

Initial pilot targets: p95 end-to-end time at or below 20 seconds and model-plus-tool cost at or below $0.15 per brief. Set hard caps of 30 seconds or $0.20 for one request; when a cap is reached, stop and return the partial result with the reason. Measure these targets in a pilot before treating them as achieved.

## 04 Memory · Data Requirements

**What persists, at what scope**

Keep source passages, the working draft, and PM corrections only within the current PM task. Do not share them across users or teams and do not store model inferences as reusable facts. The PM may manually copy the final brief, with source IDs, source dates, and creation time, into a workspace they choose; that saved copy is outside Juno memory. Every new request re-reads the underlying sources.

**Expiry and write rules**

Delete the task draft and cached excerpts when the task ends or after 30 minutes of inactivity, whichever comes first. Juno cannot write persistent memory. A PM correction outranks the model draft for the current task, but it does not overwrite an underlying source; show a source conflict to the PM. If the PM manually saves a brief, record the correction and author there, and never treat that old brief as current source truth without rechecking it.

## 05 Permissions · AI Risks & Mitigations

**The permission tier**

| Side-effect class | Tier |
|---|---|
| read | auto |
| draft | auto |
| write | blocked |
| send | blocked |

**Justification**

READ is automatic only inside the PM’s existing access and the chosen question; a mistaken read changes nothing. DRAFT is automatic only in a private view that the PM can discard. WRITE is blocked because this version has no shared-record write tool; an incorrect record could mislead colleagues. SEND is blocked because an external message or publication cannot reliably be recalled. Any future write or send needs a separate design and explicit PM approval for the exact action.

## 06 Verification · AI Testing & Measurement

**The check before output ships**

Before display, check every factual claim against an authorized, retrievable source passage and show its source ID and date. Require 100% of factual findings to have support; label hypotheses and proposed actions explicitly. Check the skill-file format: Scope and coverage, no more than three findings in the required five-column table, PM handoff, and at most 500 words. Reject invented prevalence, root cause, priority, owner, or date. In the export case, do not turn the user’s CSV failure and the over-50-MB errors into one confirmed cause. A separate claim-to-passage review flags weak support or contradictions. Module 5 will test this gate on a golden set; these are requirements, not achieved results.

**Failure behaviour**

If a source is missing, stale for a current-status claim, or does not support a claim, do not present that claim as fact. Return a clearly marked partial brief with the source gap, the disputed statement, and a concrete question for the PM. If the verification check itself fails, stop and show the failure. Never silently drop the issue or invent a replacement citation. The PM reviews the brief before using it for a product decision.

## Eval plan

Module 5 will define a golden set, pass thresholds for citation and claim support, and a regression cadence for the verification gate above. Module 6 can assess whether the workflow improves PM outcomes. The targets in this PRD are proposed requirements, not measured results.

## Out of scope

No direct reporting-database query; no ticket, roadmap, or CRM write; no model-initiated send or publication; and no automatic assignment of priority, owner, or delivery date. These are explicit product boundaries.

