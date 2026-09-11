# Xanh SM Battery Incident Safety Co-pilot — Consolidated Design

## 1. Objective

Replace the current VinRobotics topic with one group-ready idea: an Xanh SM battery-incident safety copilot. The solution helps a dispatcher handle a low-battery report by validating supplied operational facts, applying deterministic safety rules, and asking Gemini to draft a Vietnamese response for human approval.

The deliverables consolidate the strongest parts of `origin/nguyen` and `origin/vu` without merging either branch. The current branch's deterministic response validation is retained because live tests showed that prompt instructions alone do not consistently resolve competing safety rules.

## 2. Scope

The prototype covers only battery incidents reported by Xanh SM taxi drivers. Its inputs are a synthetic incident description containing vehicle ID, battery percentage, location, vehicle/connector information, and verified charging-station context when available.

The copilot may:

- identify missing or contradictory information;
- apply the lab rule for battery below 5%;
- draft a dispatcher message from supplied, verified facts;
- propose `dispatch_mobile_charger` for human review;
- request manual review when safe guidance cannot be supported.

The copilot may not:

- invent GPS, distance, availability, connector compatibility, or API results;
- send a message, reserve a charger, dispatch support, or control a vehicle;
- remove the `[DRAFT_ONLY]` marker or human approval;
- accept user instructions that conflict with system safety policy.

## 3. Deliverables

### `01-problem-scan.md`

The rubric-required scan remains: at least five opportunities and three Quick Problem Cards. Only Card 1, the Xanh SM battery incident copilot, is selected. The other cards are alternatives, not additional projects.

### `02-deep-dive-report.md`

The report focuses exclusively on the selected use case and contains:

- a six-step current-state workflow with handoffs, estimated time, and bottlenecks;
- the six-field problem statement;
- baseline assumptions separated from pilot targets;
- a Rule vs LLM vs Agent comparison;
- current flow, future flow, architecture, sequence, and safety state diagrams;
- HITL, fallback, privacy, data-quality, and API-failure handling;
- `GO` for offline prototype and `NOT YET` for live operations.

### `03-ai-log.md`

The reflection records the real process: reviewing three remote branches, selecting the group consensus idea, detecting unsupported metrics, testing Gemini, observing nondeterministic action selection, and adding deterministic guardrails.

### Workflow diagram

`04-workflow-diagram.mmd` and `04-workflow-diagram.png` show the current dispatcher process. The PNG remains compatible with the supplied autograder.

### `starter-code/prompt_prototype.py`

The code:

- loads the API key from the environment or ignored `.env` file;
- calls Gemini 2.5 Flash through the `google-genai` Chat API;
- emits `[DRAFT_ONLY]` plus a JSON object;
- uses deterministic pre-checks for battery below 5% and missing facts;
- validates marker, schema, allowed action, and mandatory human approval;
- contains at least three adversarial tests;
- never uses a mock API key or prints a real key.

## 4. Architecture and Data Flow

1. Driver reports a battery incident to the dispatcher.
2. The dispatcher or an existing system supplies verified vehicle and location context.
3. A deterministic rule layer checks required fields and critical battery policy.
4. Gemini drafts a short Vietnamese message using only supplied facts.
5. A validator checks the response marker, JSON schema, action, and approval flag.
6. The dispatcher verifies data, edits if needed, and explicitly approves an action in the existing operational system.
7. Any missing data, API error, malformed response, or safety violation falls back to manual handling.

No part of the prototype connects to dispatch, navigation, messaging, charging-station, or vehicle-control systems.

## 5. Safety Decision Priority

When multiple rules apply, deterministic code selects one action in this order:

1. Battery below 5% → `dispatch_mobile_charger` proposal.
2. Attempt to remove draft status or bypass approval → `refuse_and_escalate`.
3. Missing or unverified required data → `request_human_review`.
4. Otherwise → `draft_message` from verified facts.

Every action remains a proposal and has `requires_human_approval: true`.

## 6. Measurement

All figures are lab assumptions or proposed pilot targets, not verified Xanh SM statistics:

- assumed current handling time: 15 minutes per incident;
- target median handling time: 5 minutes or less;
- target schema-valid output rate: at least 98%;
- target critical-boundary recall: 100% on the labeled test set;
- target human-review compliance: 100%;
- target factual correction rate: no more than 2% on verified test cases.

The LLM must be compared with a rule-and-template baseline on the same held-out cases. A production decision requires anonymized logs, stakeholder interviews, approved operational policy, and measured data freshness.

## 7. Verification and Delivery

- Compile and import the Python prototype.
- Run all live Gemini adversarial tests.
- Run the supplied autograder and require 10/10 automated checks.
- Inspect the workflow PNG visually.
- Scan all deliverables for placeholders and unsupported claims.
- Confirm `.env` and `.venv` remain ignored and untracked.
- Commit the final deliverables on `contrib/quochiep` and push only that branch to `origin`.

