# Prototype · Juno

> Module 2 · Prototype One Piece of Juno. This is a low-fidelity, working prototype using **synthetic** notes. It is not evidence about a real customer or product.

## Step 1 — Sharpened candidate

**Candidate:** Juno turns a small set of supplied product signals into a sourced insight brief for a human PM. This is an **insight + augmentation** use case: it shortens the recurring work of reading scattered notes, separating observations, identifying gaps, and drafting an internal brief.

**Who and when:** A PM preparing a weekly product review of a reported export issue.

**One piece to prototype:** An on-demand insight brief screen that lets the PM read scope and coverage, two findings, evidence strength, source notes, validation steps, and a PM handoff.

**Testable assumption:** A cold reader can name the documented problem, point to its supporting note, and identify an unresolved question before making a product decision. In a Show and Swap review, ask the reader to do those three tasks and record where they hesitate. The PM retains priority, scope, and external communication decisions.

## Step 2 — Build tool

**Tool:** Codex. **Build output:** a standalone [HTML/CSS/JavaScript prototype](./index.html), with no account, API, live data connection, or build step required. GitHub Pages hosts the same file for a stranger-openable link.

## Step 3 — Low-fidelity build prompt and output

The build prompt used the four parts of the class framework:

- **Role:** Prototype Juno, an internal AI Associate PM that helps a human PM understand supplied product signals and decide what to investigate next.
- **Task:** Build one on-demand insight brief from three explicitly synthetic export-related notes. Show supported observations, decision implications, evidence strength, and the smallest useful validation steps. Keep the two user notes as separate observations.
- **Constraints:** Use only the three sample notes displayed in the prototype. Label them synthetic. Cite local references `[Source A]`, `[Source B]`, and `[Source C]`; do not invent source-system IDs, customer names, prevalence, root cause, fix status, priority, or delivery date. Show coverage gaps. Distinguish observations from proposals. Do not send messages, change records, or make final PM decisions.
- **Format:** One responsive internal brief with (1) Scope and coverage, (2) no more than three findings in the skill file's five-column table, and (3) PM handoff. Include clickable local source references and a Copy Markdown brief action. Keep the brief under 500 words.

**Output:** The [working screen](./index.html) contains two findings from three synthetic notes. A source button highlights its note. The Copy Markdown action copies the brief. I opened the local screen and verified that action displayed its success state; the hosted version is linked below.

## Step 4 — Check against Juno's skill file

I checked the prototype against the committed [Module 1 skill file](../01-prompting/skill-file.md).

| Skill file requirement | Prototype output |
|---|---|
| Synthesize supplied signals into an actionable insight brief | One export insight brief from three visible synthetic notes; no spec or final ranking |
| Trace findings to source IDs or labeled local references | Findings link to [Source A], [Source B], and [Source C] on the same screen |
| Do not infer prevalence, cause, resolution, or commitments | The brief names these as unverified and makes no priority or date promise |
| Show coverage and evidence limits | Scope lists missing telemetry, denominator, incident count, reproduction, and fix status; findings label strength Medium or Low with limits |
| Use the required format | Scope and coverage, two findings in the exact five-column structure, and two PM handoff items |
| Keep the PM in control | Only validation suggestions appear; there is no send, record-edit, or decision action |

**Revision after checking:** An earlier risk-ranking concept did not fit the skill file's evidence-synthesis task. I replaced it with this brief.

## Step 5 — Stranger-openable prototype link

[**Open the live Juno insight brief**](https://willzxd.github.io/juno-pm/02-prototype/). The prototype is also available as [source code in this repository](./index.html). It uses only embedded synthetic notes, so a reviewer needs no login or setup.

## What it demonstrates

The single flow is **read a sourced brief → inspect a supporting note → take an unresolved question to the PM**. The prototype tests whether the first synthesis is understandable and traceable; it does not test live ingestion or the accuracy of AI-generated analysis.

## Debrief

- **What worked (self-review):** Separate observations, visible coverage gaps, source references, and a concise PM handoff make the screen readable. The Copy Markdown action worked in the local browser check.
- **What broke / felt like a toy:** The three notes are static and synthetic. Source highlighting is a UI interaction, not a connection to source systems. No peer cold-read result has been collected yet.
- **What I'd change next pass:** Run the three-task cold read with a partner, record the actual confusion points, then revise the evidence labels and handoff wording. Next, test the same format with a permissioned real-source bundle and review citation accuracy before any product decision.

**Show and Swap feedback:** Pending a real partner review; no feedback is attributed to a partner here.
