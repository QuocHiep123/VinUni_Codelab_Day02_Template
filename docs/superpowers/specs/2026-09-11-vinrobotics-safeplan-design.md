# VinRobotics SafePlan Copilot — Design Specification

## 1. Objective

Complete the Lab 02 deliverables around a VinRobotics use case: an AI copilot that converts a natural-language manufacturing request into a structured draft task plan for an industrial robot workcell. The copilot supports engineers but never sends motion commands or starts a robot.

The project is grounded in VinRobotics' publicly described focus on industrial humanoids, autonomous industrial robots, assembly, mobile manipulation, AI vision, and manufacturing automation. Any workflow volumes, processing times, error rates, or target metrics in the deliverables are explicitly labeled as prototype assumptions that require validation with internal data.

## 2. Selected Problem

Manufacturing and robotics engineers must translate a task request into ordered robot actions, check payload and work-zone constraints, attach the correct SOP, review hazards, simulate the plan, and obtain approval. This manual planning process is slow and inconsistent when requests arrive as unstructured Vietnamese text.

SafePlan Copilot will:

1. Parse an operator's task request and available robot context.
2. Validate required fields and deterministic safety constraints.
3. Produce a `[DRAFT_ONLY]` structured plan.
4. Escalate unsafe, ambiguous, or unsupported requests.
5. Require engineer approval and simulation before deployment.

SafePlan Copilot will not generate low-level joint trajectories, bypass interlocks, disable sensors, enter an occupied human zone, or directly control a physical robot.

## 3. Deliverables

### `01-problem-scan.md`

- Five VinRobotics operational opportunities spanning the four discovery lenses.
- Three complete Quick Problem Cards.
- A transparent comparison and selection of SafePlan Copilot.

### `02-deep-dive-report.md`

- Context, evidence, and clearly marked assumptions.
- Current-state workflow with roles, handoffs, bottlenecks, and estimated duration.
- Six-field problem statement.
- Baseline and target metrics.
- Rule vs LLM vs Agent comparison.
- Future-state workflow, system architecture, sequence diagram, and safety state machine in Mermaid.
- Human-in-the-loop, fallback, failure handling, and readiness decision.

### `03-ai-log.md`

- A draft first-person reflection based on the actual collaboration in this session.
- Records how AI supported ideation and rubric analysis, where unsupported assumptions appeared, and how prompts and boundaries were corrected.
- Includes a note that the student must review and personalize the reflection before submission.

### Workflow diagram

- `04-workflow-diagram.mmd` contains editable Mermaid source for the required current-state workflow.
- `04-workflow-diagram.png` is a rendered image accepted by the supplied autograder.
- Additional diagrams remain embedded in `02-deep-dive-report.md` so reviewers can inspect the complete system behavior.

### `starter-code/prompt_prototype.py`

- Loads `GEMINI_API_KEY` from the process environment or local `.env` without printing it.
- Calls `gemini-2.5-flash` using the current `google-genai` SDK.
- Uses a strict VinRobotics system prompt and `[DRAFT_ONLY]` output contract.
- Produces a JSON object after the draft marker.
- Includes at least three adversarial tests and deterministic verification checks.
- Retains the template safety concepts `5%` and `dispatch_mobile_charger` as a meaningful robot low-battery boundary, keeping compatibility with the supplied autograder without altering the grader.

## 4. Architecture and Data Flow

The prototype accepts a text request containing the task, robot identifier, battery level, payload, work zone, and human-presence status. Deterministic input validation checks missing fields and obvious hard limits. Gemini then classifies the request and drafts a task plan. A deterministic output validator verifies the draft marker, JSON schema, allowed actions, and required escalation behavior. Only a robotics engineer can approve the draft for simulation. Deployment to a controller is outside the prototype scope.

The production concept separates these components:

- Request intake and context collection.
- Rule-based safety pre-check.
- LLM planning feature.
- Schema and policy validator.
- Engineer review.
- Digital-twin simulation.
- Existing robot deployment workflow.

## 5. Safety and Error Handling

- All model responses must begin with `[DRAFT_ONLY]`.
- Battery below 5% blocks task planning and returns `dispatch_mobile_charger`.
- Requests to disable safety systems, bypass approval, or operate in an occupied safety zone are refused and escalated.
- Missing or conflicting context returns `request_clarification`.
- Invalid model output, API failure, timeout, or schema mismatch falls back to the current manual planning workflow.
- No API key or sensitive value is logged.
- The prompt and report distinguish public facts from unverified prototype assumptions.

## 6. Prototype Metrics

All values below are targets for a controlled pilot, not claims about current VinRobotics performance:

- Reduce initial task-plan drafting from an assumed 45 minutes to 10 minutes or less.
- At least 98% schema-valid responses on the test set.
- 100% recall on defined critical safety violations.
- 100% of plans remain drafts until engineer approval.
- At least 90% engineer acceptance after no more than one revision during the pilot.

## 7. Verification

1. Import and syntax checks run without exposing the API key.
2. The supplied autograder confirms all required files and code structure.
3. Live Gemini tests cover critical battery, approval bypass, disabled safety systems, occupied zones, unsupported payloads, and prompt injection.
4. Deterministic validators reject missing draft markers, malformed JSON, and unsafe actions.
5. The final Git diff is reviewed to ensure `.env` and `.venv` remain ignored.

## 8. Decision

The recommendation is **GO for a limited offline prototype with mandatory human review and simulation**. Autonomous execution from LLM output is explicitly **NO-GO**. Moving beyond the prototype requires real SOPs, robot capability data, safety-owner approval, an offline evaluation set, and measured operational baselines.

