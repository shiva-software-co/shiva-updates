---
Version: 2
Spec hash: 367a6281d930334bf1cbe83d83393a950a57397ece1b1cd5ee4034a84df26195
Date: 2026-09-28
Summary: Test strategy for the "Google AI Mode — entering an empty chat with a personalized greeting" feature. Two-stage plan (dev.google.com — full coverage of 19 scenarios; production www.google.com — smoke, 3 scenarios × 6 cells = 18 runs, D-12/D-13/D-23); invariant oracles prior to baselines (DEP-1…DEP-4); regression policy (TBD-2); exploratory, 3 charters × 2 sessions × 60 min (TBD-1); automation roadmap for the entire suite (TBD-3); management via files in the repository (TBD-4); observable metrics — facts of the first cycle, targets set after the baseline (TBD-5).
TBD status: open TBDs — 0; TBD-1…TBD-5 closed by the owner's answers in round 1 (2026-09-28); strategy finalized by the owner (final question Q-6 — "yes", 2026-09-28). The registry is in the TBD Questions section. External dependencies DEP-1…DEP-4 are inherited from the spec with temporary status rules (observation) — this is a mechanism for confirming the implementation during a run, not a strategy TBD.
---

# Google AI Mode Chat Entry — Test Strategy ("Google AI Mode — entering an empty chat")

## Strategy Overview
- **Strategy Version:** V2.0 (Version: 2) — final; history: v1 — round 1 (closing TBD-1…TBD-5 with the owner's answers); v2 — finalization (Q-6 — "yes", 2026-09-28)
- **Development Date:** 2026-09-28
- **Developer:** QA agent (AI) — execution model: test design and execution are performed by an AI agent; matters of staffing, budget, approvers, deadlines, and calendar durations are outside the strategy
- **Applicable Scope:** the "Google AI Mode — entering an empty chat with a personalized greeting" feature; input — spec specs/0001-google-ai-mode-chat-entry/spec.md (Version 5); scope — web UI (desktop + mobile web) per matrix D-1, two stands (dev.google.com, production https://www.google.com)

---

## Project Background Analysis

### Business Background
- **Project Overview:** entering the AI chat ("AI Mode") from the https://www.google.com page: transition to an empty chat with the personalized greeting "Hello, Ivan! What are you interested in?", an empty message history, and an input field (spec 1.1, BR-1…BR-5).
- **Business Objectives:** the single goal set by the input — entering the AI chat; the personalized greeting lowers the barrier to starting a dialogue; the empty chat provides a clean starting point; access without signing in (D-24) broadens the audience; explicit overflow indication and blocking of invalid submission (D-26/D-27) prevent input loss (spec 1.1, 1.3).
- **Success Criteria:** exit criteria of spec 8.3: stage 1 (dev) — 100% P0/P1 Passed, 0 open Critical defects (D-11, D-12, taxonomy D-10); stage 2 (production) — 100% of the smoke suite (TC-P-001, TC-N-001, TC-N-003) on each of the 6 cells of matrix D-1 — 18 out of 18 runs (D-13/D-23); artifacts complete for every executed scenario; residual risks documented (RR-1…RR-3).
- **Key Stakeholders:** requirements owner (source of decisions D-1…D-27); the "AI Mode" implementation (confirmation of external dependencies DEP-1…DEP-4); QA agent (test design and execution). Approvals, budgets, and staffing are outside the execution model.

### Technical Background
- **Technical Architecture:** public web frontend google.com → the "AI Mode" chat; entry point — the "AI Mode" button on the search page; interaction with the AI service is observed through the UI (sending a message → the AI's reply appears in the history, D-3, BR-6). Internal APIs, databases, and the backend are not mentioned in the input → out of scope.
- **System Scale:** 6 cells of compatibility matrix D-1 (Chrome/Firefox/Edge on Windows 11, Safari on macOS 14, Safari on iPhone 15 (iOS 17), Chrome on Pixel 8 (Android 14)); 19 scenarios; 6 test accounts + a guest session (D-9); 2 stands (D-12).
- **Integration Complexity:** frontend integration with the AI service is verified only through observable UI behavior (D-3); the infrastructure is not tested directly.
- **Technical Risks:** unconfirmed implementation mechanisms — DEP-1 (exact greeting texts for alternative account states), DEP-2 (the actual basis of the character-limit count: code points vs UTF-16), DEP-3 (availability of the "AI Mode" button for test accounts, rollout/flags), DEP-4 (the AI service's error text); the indicative nature of timing measurements (the network is not controlled, D-7).

### Project Constraints
- **Execution Model:** test design and execution are performed by an AI agent; constraints on time, staffing, budget, approvers, and deadlines are outside the strategy's area (prompt rule).
- **Quality Constraints:** defect taxonomy D-10 (5 levels: Blocking / Critical / Major / Minor / Trivial); result statuses 9.1 (Passed / Failed / Blocked / N/A / Excluded / Observation, indicative label); artifact rules 9.2/9.3 (text is verified as text — DOM/transcription; screenshots — visual state only; flows — video); exit thresholds D-11/D-23 — without inventing thresholds beyond the owner's answers (8.3).
- **Technical Constraints:** matrix D-1 is fixed (the latest stable browser versions are recorded in the run protocol); stage order dev → production (D-12); production — smoke only (D-13/D-23, RR-3); production data "as is" without preparation/cleanup (D-21, RR-2); data preparation on dev — re-creating accounts before every run (D-21, D-25).

---

## Test Objectives and Scope

### Test Objectives

#### Primary Quality Objectives
- **Functional Quality Objectives:**
  - Functional requirement coverage: 100% P0/P1 Passed at the dev stage (D-11, D-12); the full suite of 19 scenarios is executed on dev (D-12); P2 scenarios are executed per the run order (4.2); no threshold beyond D-11 is set for them (rule 8.3: thresholds beyond the owner's answers are not invented). All BR-1…BR-10 are covered by scenarios (spec traceability 6.1/6.2).
  - Core function availability: transition to an empty chat with the exact greeting (TC-P-001, TC-C-001, BR-2/BR-3); guest entry without a sign-in prompt (D-24, TC-P-005); basic message submission with an AI reply (D-3, TC-P-002, BR-6); repeated entry — always a new empty chat (D-15, BR-9).
  - Business process completeness: precondition BR-1 covered (search field, "AI Mode" button, "Google Search" button → TC-P-001, TC-C-001); production smoke 18 out of 18 (D-13/D-23).
- **Non-Functional Quality Objectives:**
  - System response time: median transition time ≤ 1,000 ms over 10 launches, indicative (D-7); repeat rule — median in 1,000–1,200 ms → 10 additional launches (D-20). Resource consumption is excluded (D-20 → RR-1).
  - Security: XSS scripts do not execute, the name and message render literally (D-6, BR-8); session reactions are observable, access to another user's chat without authorization is impossible (D-19, BR-10).
  - Compatibility: the TC-P-001 oracle passes on all 6 cells of D-1; the greeting text is identical across all cells (TC-C-001).
  - Concurrent users / availability: not mentioned in the input → out of scope (Exclusion Scope, reason "not mentioned in the input").

#### Test Efficiency Objectives
- **Automation Potential (forecast):** a technical forecast of the automatability of the 19 scenarios (NOT the automation pace of the current manual cycle; the cycle per spec 4.2 is manual):
  - **Fully automatable ≈ 68% (13 of 19):** TC-P-001…006, TC-N-001, TC-N-002, TC-N-003, TC-B-001, TC-B-002, TC-B-003, TC-S-002 — web UI automation (conditional tool recommendation: Playwright or Selenium): navigation and clicks, text assertions of the greeting/history from the DOM (text verified as text, 9.3), injection of boundary strings (31,999/32,000/32,001 cp; astral set), programmatic counting of code points and UTF-16 units, assertions of button state (enabled/disabled) and highlighting (screenshot + style), interception of alert dialogs (XSS), sending a message and awaiting a non-empty AI reply.
  - **Partially automatable ≈ 32% (6 of 19):** TC-C-001 (desktop cells fully automatable; real iPhone 15/Pixel 8 devices — automation is conditional, part of the checks manual); TC-N-004 (network interruption on desktop is emulated instrumentally (DevTools/CDP); weak networks on mobile devices — physical switching, manual); TC-N-005 (simulating AI service unavailability on dev is scriptable if simulation facilities are accessible; on production — observational mode, manual); TC-S-001 (DOM assertions and alert interception automatable; preparing the test-payload account with a payload name — manual data setup); TC-S-003 (the mechanism for triggering session expiry is recorded in the run protocol — partially scriptable); TC-PF-001 (measurements by the "click → greeting visible" markers and the median are scriptable; indicative metadata recorded manually).
  - **Not automatable remainder ≈ 5–10% of the volume (within partially):** physical network control on real mobile devices where emulation is unavailable; the observational part of TC-N-005 on production (real AI service unavailability incidents cannot be reproduced by a script); recording/confirmation of baseline strings DEP-1/DEP-4 and the baseline of the highlighting appearance at the first run — an automatable capture + the owner's human decision on baseline approval.
  - Owner's position (TBD-3, round 1, 2026-09-28): "we aim to automate all scenarios" → the strategy includes an automation roadmap for the entire suite (see Summary and Recommendations); the partially/not-automatable remainder is documented above with reasons.
- **Defect Discovery Efficiency:** expressed in measures derived from the input: all planned scenarios executed (dev — the full suite, production — 18/18), exit thresholds D-11/D-23 met, defect distribution across taxonomy D-10 recorded. Detection/fix/leak rates and defect density are collected as facts of the cycle and form a baseline (TBD-5, round 1); target values are set after the first cycle — not invented.
- **Test Execution Efficiency:** actual cycle statistics: executed scenarios and runs (the full dev suite by applicability 3.7; 18 production smoke runs; 10 TC-PF-001 measurements across cells + repeats per rule D-20), repeat launches, Blocked records, N/A cells (outside matrix D-1). Effort norms are not applied (AI execution); efficiency norms/targets — after the baseline of the first cycle (TBD-5).

### Test Scope

#### Functional Test Scope
The automation forecast in the table is technical (see Automation Potential); the current cycle is manual (spec 4.2).

| Functional Module | Test Depth | Priority | Test Method | Automation Forecast |
|---|---|---|---|---|
| Transition from google.com to an empty chat (including a guest without a sign-in prompt, D-24) | Comprehensive | P0 | Scenario-based / state transition; manual cycle; N/A: outside matrix D-1 | ≈100% automatable — UI automation (Playwright/Selenium, conditionally): click, assertions of greeting/history/field from the DOM; no insurmountable remainder |
| Greeting across the account-state matrix (D-2, D-9; invariants D-22/D-24; baseline DEP-1) | Comprehensive | P0/P1 | ECP + decision table 4.1-A | ≈100% automatable — text assertions of invariants; recording baseline strings — capture automatable, baseline approval — the owner |
| Basic message submission and AI reply (D-3) | In-depth | P1 | Scenario-based / state transition | ≈95% automatable — submission, history assertions; awaiting a non-empty AI reply — timings unstable, remainder — observation |
| Negative flows (D-4; oracles D-14…D-18) | In-depth | P1/P2 | Error guessing / state transition | TC-N-001/002/003 ≈100%; TC-N-004 ≈50% (desktop — network emulation; mobile — physical, manual); TC-N-005 ≈40% (simulation on dev scriptable; observational production — manual) |
| Input field boundaries: 32,000 cp limit + indication model (D-5, D-26/D-27; DEP-2) | In-depth | P1/P2 | BVA (basis — code points) + state transition | ≈95% automatable — string injection, cp/UTF-16 counting, highlighting/button assertions, state restoration; remainder — owner's approval of the highlighting-appearance baseline |
| Security: XSS (name + field, D-6) and session security (D-19) | Comprehensive | P0/P1/P2 | Error guessing / security checks via the UI | TC-S-002 ≈100%; TC-S-001 ≈85% (test-payload account preparation — manual); TC-S-003 ≈70% (session expiry mechanism — recorded in the protocol, partially scriptable) |
| Performance: transition time (D-7, D-20) | Focus | P2 | Measurements by markers, median, 10 launches, indicative | ≈90% automatable — measurement script; remainder — indicative metadata and interpretation when repeating per D-20 |
| Compatibility: matrix D-1 (TC-C-001, 6 cells) | In-depth | P1 | Compatibility matrix (OATS not applied — the matrix is explicitly defined) | ≈75% — desktop cells (grid automation); real devices — conditional automation, part manual |

#### Non-Functional Test Scope
- **Performance Testing:** transition time only (TC-PF-001: median ≤ 1,000 ms, 10 launches, indicative, D-7; repeat rule D-20). Load/stress/capacity types are not set by the owner (spec 4.2) → out of scope.
- **Security Testing:** dynamic manual UI checks: XSS in the account name and input field (D-6, TC-S-001/002); session security — session expiry, access to another user's chat (D-19, TC-S-003). Static scanning, penetration testing, and instrumental scanning (OWASP ZAP and the like) are not mentioned in the input → out of scope.
- **Compatibility Testing:** matrix D-1 — 6 cells: Chrome/Firefox/Edge (Windows 11), Safari (macOS 14), Safari iPhone 15 (iOS 17), Chrome Pixel 8 (Android 14); exact versions are recorded in the run protocol (D-1). N/A: platforms/browsers/devices outside D-1.
- **Usability Testing:** the presence and state of the chat elements (spec 2.2): greeting, history, input field; input field highlighting on overflow — a visual state (screenshot); the submit button's activity (D-26/D-27); text verified as text, not by pixels (9.3).

#### Test Exclusion Scope
- **Third-Party Components:** Google's infrastructure and the AI service are not tested directly — only observed through the UI (D-3).
- **Infrastructure:** production APM/monitoring, logs — outside the strategy (not mentioned in the input).
- **Legacy Functions:** the content of existing chats from past sessions is not checked; history is used only as the test-history baseline state (D-25, TC-P-006).
- **Demo Functions:** not mentioned in the input → out of scope.
- **Exclusions inherited from the owner (not re-asked):**
  - Resource consumption (page weight, memory) — D-20 → RR-1 (into the release checklist).
  - Full coverage on production — D-12 → RR-3; production — smoke only (D-13/D-23).
  - Preparation/cleanup of test data on production — D-21 → RR-2 ("as is" + recording the state in the protocol).
  - Search query field limits — out of scope; the field is checked only for visibility in the precondition (spec 2.1, round 5 clarification).
  - Load/stress/capacity performance types — not set by the owner (D-7 — transition time only; spec 4.2).
  - API testing — not applicable (API not mentioned in the input, spec 4.2).
  - OATS — not applied: the compatibility matrix is explicitly defined by the owner (D-1, spec 4.1.1).
- **Not Mentioned in the Input (out of scope, reason "not mentioned in the input"):** native mobile apps and their flows (install/upgrade, OS permission dialogs, app lifecycle — the input describes mobile web); upload/download flows; message brokers/queues; direct database checks; accessibility; localization/internationalization beyond the RU/EN matrix; concurrent users and availability targets; unit level (development's area of responsibility — prompt rule).

---

### Test Methods and Strategies

#### Test Layering Strategy
The unit level is outside the strategy: it is development's responsibility and does not derive from the tester's input (prompt rule). The API layer is not included: APIs are out of scope (spec 4.2).

##### Test Pyramid Model
```text
        /\
       /UI\     UI / E2E Testing — all 19 scenarios (key, negative,
      /____\    boundary, security, performance, compatibility flows)
```
Distribution across layers: 100% of coverage — the UI/E2E layer (other layers outside the area); layer shares determinable from the input — no TBDs.

##### Layer-Specific Test Strategies
- **Interface Test Layer:** not applicable (APIs out of scope, spec 4.2).
- **UI Test Layer:**
  - Cover key business processes: transition to an empty chat (authorized and guest D-24), greeting, empty history, input field, submission, repeated entry, limit boundaries, XSS/sessions.
  - Execution frequency: a full run on dev (D-12) + regression per policy TBD-2 (after every fix — affected scenarios + the smoke suite; before production smoke — a full regression of the dev suite); production smoke — 18 runs (D-23).
  - Tools: the current cycle — a manual run in the browsers of matrix D-1; for the automation roadmap — a conditional recommendation of Playwright or Selenium (the input does not fix a tool; the choice is not imposed).

#### Test Type Strategies
##### Functional Test Strategy
- **Smoke Testing:** the quick suite D-13 — TC-P-001, TC-N-001, TC-N-003.
  - Coverage scope: the entry core (transition to an empty chat with a greeting, absence of duplicates on repeated clicks, resilience to reloading); on production — 3 scenarios × 6 cells = 18 runs (D-23).
  - Automation potential (forecast): ≈100% (all three scenarios — UI-automatable; the priority candidate of roadmap TBD-3).
  - Failure criteria: any Failed scenario of the suite → the stage does not exit (100% Passed per D-11/D-23); the smoke suite is also used as a quick filter after defect fixes (TBD-2).
- **Regression Testing** (TBD-2, closed in round 1, 2026-09-28):
  - Execution cycle: after every defect fix — a repeat of the affected scenarios + the smoke suite (TC-P-001, TC-N-001, TC-N-003); before starting production smoke — one full regression pass of the dev suite (all 19 scenarios).
  - Coverage scope: the areas affected by the defect's scenario + the smoke core; the full suite — before a stand switch (dev → production).
- **Exploratory Testing** (TBD-1, closed in round 1, 2026-09-28):
  - Timebox: 3 charters × 2 sessions of 60 minutes each (6 sessions in total; executed on the dev stand).
  - Charters:
    - **E-1 "Chat entry and greeting":** explore entry paths and variations (alternative entry points, repeated/parallel entries, greeting stability during navigation).
    - **E-2 "Account states":** explore variations around matrix D-2/D-9 (personalization boundaries, RU/EN locales, guest entry D-24).
    - **E-3 "Input field and limit":** explore the field's and the button's behavior around the D-26/D-27 model (pasting from the clipboard at the boundary, emoji/astral characters, editing on overflow, state restoration).
  - Focus areas: user experience and edge cases not covered by the spec's formal scenarios.
  - Documentation requirements: session records (charter, notes, findings) in the run log in the repository (TBD-4); findings are classified per taxonomy D-10 and do not replace the spec's oracles — formal scenarios take priority.

##### Non-Functional Test Strategy
- **Performance Test Strategy:**
  - Baseline testing: 10 launches of TC-PF-001 by markers (click "AI Mode" → greeting visible), median, indicative (D-7); measurements separately per the cells of matrix D-1 (3.5).
  - Repeat rule: median in 1,000–1,200 ms → 10 additional launches (D-20).
  - Load/stress/capacity: out of scope — not set by the owner (spec 4.2); resource consumption excluded (D-20 → RR-1).
- **Security Test Strategy:**
  - Dynamic security testing (runtime, via the UI): XSS rendering of the name and message (D-6, TC-S-001/002); session reactions (D-19, TC-S-003).
  - Static scanning / penetration testing: not mentioned in the input → out of scope.
  - Compliance checking: standards-compliance goals are not mentioned in the input → out of scope.

#### Test Data Strategy
##### Test Data Classification
- **Synthetic Test Data** (primary strategy):
  - Applicable scenarios: functional, boundary, and security testing on dev.
  - Account matrix D-9 (data created by the tester): test-ivan (name "Ivan", RU, empty history), test-noname (RU, name not set), test-en ("John", EN), test-payload (name "<script>alert(1)</script>"), test-history (name "Ivan", RU, baseline history D-25), guest session without signing in (D-24).
  - Boundary sets D-5: strings of 31,999 / 32,000 / 32,001 code points; astral set — 32,000 astral emoji (U+1F600: 1 character = 1 cp = 2 UTF-16 units; DEP-2).
  - Security sets D-6: payload name; input field payloads `<script>alert(1)</script>`, `<img src=x onerror=alert(1)>`.
  - Maintenance cost: low (re-creation by a script/procedure); quality is controllable.
- **Production Data (production accounts):**
  - Applicable scenarios: production smoke (stage 2).
  - Data characteristics: "as is" without preparation/cleanup (D-21, RR-2); the actual state is recorded in the run protocol (input check 8.1).
  - De-identification is not mentioned in the input → out of scope.

##### Test Data Management
- **Data Preparation:** dev — re-creating accounts before every run with matrix states set (D-21), including the test-history baseline history (2–3 completed chats from past sessions with 3–5 messages each, D-25); production — no preparation (D-21).
- **Data Isolation:** dev — isolation through re-creation; production — no changes, recording the actual state (RR-2); guest session — a clean profile without signing in (D-24).
- **Data Cleanup:** dev — re-creation before the next run replaces cleanup; production — cleanup excluded by the owner (D-21 → RR-2).
- **Data Security:** the test-payload payload account is used only in XSS scenarios (TC-S-001); sensitive personal data are not mentioned in the input.
- **Refresh/verification frequency:** before every run on dev (D-21, entry criterion 8.1); on production — verifying and recording the actual state before the run (8.1, RR-2).

---

### Test Environment and Tools

#### Test Environment Strategy
##### Environment Planning
| Environment Type | Purpose | Configuration | Data |
|---|---|---|---|
| Stand 1 — dev.google.com | Full coverage: 19 scenarios, matrix TC-C-001 (6 cells), TC-PF-001 measurements (D-12) | Browsers/OS/devices of matrix D-1; versions recorded in the protocol | Re-creating accounts before every run (D-21), including baseline history D-25 |
| Stand 2 — production https://www.google.com | Smoke suite D-13 (TC-P-001, TC-N-001, TC-N-003) × 6 cells = 18 runs (D-23) | Matrix D-1 | "As is", the actual state recorded in the protocol (D-21, RR-2) |

- Order: dev → production; production smoke is executed after exiting the dev stage (D-12, 8.3).
- Input check of DEP-3 (availability of the "AI Mode" button for test accounts) on every stand (8.1); if unavailable — Blocked status (9.1).
- Development/integration/pre-prod/performance environments from the template are not mentioned in the input → not introduced (exclusion, reason "not mentioned in the input"); the two-stage plan D-12 is authoritative.

#### Test Tool Chain
##### Test Management Tools
- **Test Management** (TBD-4, closed in round 1, 2026-09-28): files in the repository (Markdown tables) next to the spec (specs/0001-google-ai-mode-chat-entry/):
  - test plan and scenarios — inherited from the spec (the D-1…D-27 registry is the source of truth);
  - run log — records per 9.2: stand, platform, browser + version, OS + version, device model (mobile), run date, run ID;
  - defect registry — with D-10 levels and statuses;
  - cycle report — KQI facts, statuses 9.1, observation conversion.
- **Automation Tools** (conditional recommendation for roadmap TBD-3; the current cycle is manual, spec 4.2): Playwright or Selenium — web UI automation; desktop cells — grid; mobile web cells on real devices — conditional automation (remote debugging/device farms); the input does not fix a tool choice — not imposed.

##### Specialized Test Tools
- **Performance Test Tools:** a measurement script using observable markers (click → greeting visible), 10 launches, median, a measurement protocol with metadata (D-7, D-20, 9.3). Load tools (JMeter/LoadRunner/Gatling) are not required — load testing is out of scope.
- **Security Test Tools:** none required beyond browser facilities: interception of alert dialogs (XSS checks D-6), DOM inspection (text as text, 9.3). Instrumental scanning is out of scope.
- **Code Quality Tools:** outside the strategy (unit/code — development's area).
- **Monitoring Analysis Tools:** only the facilities needed for evidence artifacts: recording network conditions for TC-N-004 (weak networks on mobile — conditions recorded in the protocol), counting code points and UTF-16 units by script (dual-basis counting for TC-B-001…003, DEP-2), taking screenshots/video (9.3). Production APM is outside the strategy.

---

### Risk Management and Quality Control

#### Risk Identification and Assessment
##### Quality Risk Matrix
| Risk Category | Risk Description | Impact Level | Occurrence Probability | Risk Level | Response Strategy |
|---|---|---|---|---|---|
| Technical Risk | The basis of the limit count is not confirmed by the implementation (code points vs UTF-16, DEP-2) | Medium (TC-B-001…003) | Medium | Medium | Conditional oracles until confirmed; astral set TC-B-003 — highlighting/button as observable markers of the actual basis (D-26); observation status (9.1) |
| Technical Risk | The exact greeting texts for alternative states (no name, EN, guest) are unknown (DEP-1) | Medium (TC-P-003…005) | Medium | Medium | Invariants D-22/D-24 — deterministic pass rules; exact texts — first-run baseline; observation status |
| Environment Risk | The availability of the "AI Mode" button for test accounts (rollout/flags) is not confirmed (DEP-3) | Medium (precondition of all scenarios, BR-1) | Medium | Medium | Input check on every stand (8.1); if unavailable — Blocked (9.1) |
| Technical Risk | The exact text of the AI service's error — implementation (DEP-4) | Low (TC-N-005) | Low | Low | Pass rule — reaction class D-18; the text — first-run baseline; observation status |
| Data Risk | Production "as is" (D-21): the "history is empty" oracle of TC-P-001 depends on the actual state of test-ivan on production — false Failed/skips (RR-2) | Medium | Medium | Medium | Recording the actual state in the protocol before the run (8.1); RR-2 — into the release checklist |
| Environment Risk | The dev/production environment difference is covered only by smoke (D-12, RR-3) | Medium (production-specific behavior) | Medium | Medium | Smoke across the entire matrix D-1 (D-23); RR-3 — into the release checklist |
| Accepted Risk | Resource consumption is not tested (D-20 → RR-1) | Medium (unnoticed page weight/memory issues on weak devices) | Low | Low | Accepted by the owner; RR-1 — into the release checklist |
| Technical Risk | The hue of the "red" highlighting may differ across platforms (TC-B-001, TC-B-003) | Low | Medium | Low | Highlighting — a visual state (screenshot, 9.3); the appearance baseline is recorded at the first run |
| Process Risk | TC-N-005 on production is deliberately not reproduced — an observational scenario | Low (coverage of the service-error reaction on production) | Low | Low | Observational mode: incident/simulation on dev (D-18); observation status for the baseline text (DEP-4) |
| Measurement Risk | Indicative measurements: the network is not controlled (D-7) — instability of the TC-PF-001 median | Low | Medium | Low | 10 launches + median; repeat rule D-20; indicative label (9.1) |
| Environment Risk | Stand instability/unavailability → Blocked runs | Medium | Low | Low | Entry criteria 8.1 before the start; repeats of Blocked runs; recording reasons in the log |
| Data Risk | Reproducibility of baseline history D-25 when re-creating accounts on dev | Medium (TC-P-006 oracle, BR-9) | Low | Low | Re-creation with the same composition (2–3 chats × 3–5 messages, D-25); verification in entry criteria 8.1 |

##### Risk Response Measures
- **Risk Prevention:** input checks 8.1 (stands, accounts, baseline data, versions, DEP-3) before the start; invariant pass rules (D-22/D-24/D-26/D-27) make the oracles deterministic before baseline confirmation.
- **Risk Mitigation:** the observation-status mechanism with conversion upon DEP confirmation (9.1); smoke D-13/D-23 as a quick filter; regression policy TBD-2 (affected + smoke after fixes); recording the actual state of production data (RR-2).
- **Risk Acceptance:** RR-1…RR-3 — residual risks accepted by the owner; handed over to the release checklist (spec 10.3).

#### Quality Control Mechanisms
##### Process Quality Control
- **Test Plan Review:** the plan = spec v5 (the D-1…D-27 registry); changes — only through the owner; the strategy inherits decisions without reopening closed questions.
- **Test Case Review:** the spec's scenarios TC-1…TC-19 (3.1–3.6) with oracles, N/A rules, and artifacts; the spec's Self-Check (11) passed; redefining oracles — only per the owner's feedback (precedents D-24/D-26/D-27).
- **Test Execution Monitoring:** order P0 → P1 → P2 (4.2); matrix TC-C-001 after the baseline cell; TC-PF-001 measurements separately per cell; run log in the repository (TBD-4) with run IDs and 9.2 metadata.
- **Defect Management Control:** defect registry in the repository; levels D-10; 0 open Criticals at stage exit (D-11); regression after every fix (TBD-2).

##### Product Quality Control
- **Entry Quality Control:** criteria 8.1 (stands available; accounts of matrix D-9 re-created/verified; baseline data ready; versions recorded; DEP-3 checked).
- **Process Quality Monitoring:** statuses 9.1 (Passed/Failed/Blocked/N/A/Excluded/Observation, indicative); evidence chain 9.2; first-run baseline recordings (DEP-1/DEP-4, highlighting appearance, basis markers DEP-2).
- **Exit Quality Control:** 8.3 per stage: dev — 100% P0/P1 Passed + 0 open Criticals + complete artifacts; production — 18/18 smoke Passed + 0 Criticals + complete artifacts.
- **Release Quality Assurance:** RR-1…RR-3 are handed over to the release checklist with an impact assessment (spec 10.3); conditional DEP-2 oracles and DEP-1/DEP-4 baseline strings are recorded in the report with a recommendation for confirmation.

#### Quality Metrics and Improvement
##### Key Quality Indicators (KQI)
1. **Target values from the input (measurable):**
   - 100% P0/P1 Passed at the dev stage (D-11, D-12);
   - 100% of the smoke suite on production: 18 out of 18 runs across 6 cells (D-13/D-23);
   - 0 open Critical defects at the exit of every stage (D-10/D-11);
   - median transition time ≤ 1,000 ms, indicative (D-7), repeat rule D-20.
2. **Observable metrics without targets in the input (cycle facts; baseline — TBD-5, closed in round 1, 2026-09-28):**
   - Defect discovery: the number and severity of defects found in the cycle's phases (per taxonomy D-10);
   - Defect fix: fixes verified within the cycle (accounting for regression TBD-2);
   - Defect escape: defects discovered on production after release;
   - Defect density: defects per functional rule (BR-1…BR-10) / per scenario (TC-ID);
   - Execution stats: executed scenarios and runs (including repeats per D-20), Blocked records, N/A cells.
   - Approach: collected as facts of the first cycle; target values are set after the baseline (not invented).

##### Continuous Improvement Mechanisms
- **Regular Reviews:** cycle results — a report with KQI facts; revision of regression policy TBD-2 and exploratory charters TBD-1 per the baseline.
- **Metrics Analysis:** defect and execution trends — starting from the next cycle (after baseline TBD-5).
- **Best Practices:** baseline strings (DEP-1/DEP-4), the highlighting-appearance baseline, basis markers (DEP-2) — reused by subsequent cycles.
- **Innovation Experiments:** automation roadmap TBD-3 (smoke → P0/P1 → boundary/negative) — piloting tools (conditional recommendation Playwright/Selenium) outside the current manual cycle.

---

### Implementation Plan and Milestones

#### Implementation Phase Planning
The phases form an ordered sequence. Calendar durations, dates, and time points are outside the strategy (not derived).

##### Phase 1: Foundation Building
- **Process Establishment:** run log + defect registry in the repository (TBD-4); record format per 9.2 (run ID, environment metadata); code-point/UTF-16 counting scripts; screenshot/video facilities; recording network conditions for TC-N-004.
- **Environment Setup:** input checks 8.1 — availability of the dev.google.com and production https://www.google.com stands; browser/OS/device versions recorded for the run (D-1); DEP-3 check (the "AI Mode" button is available) on every stand.
- **Tool Selection:** the conditional UI-automation tool recommendation (Playwright/Selenium) recorded for roadmap TBD-3; the current cycle — manual (spec 4.2).

##### Phase 2: Capability Building
- **Case Design:** inherited from the spec (19 scenarios with oracles; design methods 4.1: ECP, BVA, decision table 4.1-A, state transitions 4.1-B, error guessing; OATS not applied).
- **Data Preparation:** dev — re-creating the accounts of matrix D-9 before every run with states set (name, locale; test-history — baseline history D-25: 2–3 chats × 3–5 messages); boundary sets D-5; security payloads D-6; production — recording the actual state of accounts (D-21, RR-2).
- **Script Development:** preparation of auxiliary facilities (not the cycle's UI automated tests): cp/UTF-16 counting scripts, a timing measurement script using markers, run-log record templates.

##### Phase 3: Full Implementation
- **Stage 1 — dev.google.com, full coverage (D-12):** order P0 (TC-P-001, TC-S-001) → baseline cell TC-C-001 → P1 (TC-P-002…006, TC-N-001…003, TC-B-001, TC-S-002, TC-C-001 across cells) → P2 (TC-N-004, TC-N-005, TC-B-002, TC-B-003, TC-S-003, TC-PF-001 across cells) (4.2); exploratory sessions TBD-1 (3 charters × 2 × 60 min); first-run baseline recordings (DEP-1 — exact greeting texts; DEP-4 — error text; highlighting appearance; DEP-2 — basis markers via TC-B-003); regression after every fix (TBD-2).
- **Stage 1 exit (8.3):** 100% P0/P1 Passed, 0 open Criticals, artifacts complete.
- **Stage 2 — production https://www.google.com, smoke (D-12/D-13/D-23):** a full regression of the dev suite (19 scenarios) before the start (TBD-2); smoke TC-P-001, TC-N-001, TC-N-003 × 6 cells = 18 runs; data "as is" (D-21); TC-N-005 — observational (incident/simulation on dev).
- **Stage 2 exit (8.3):** 18 out of 18 Passed, 0 open Criticals, artifacts complete.
- **Cycle report:** KQI facts (TBD-5), statuses 9.1, observation conversion with confirmed DEPs, documented RR-1…RR-3, the baseline for the next cycle.
- **Continuous Optimization / Capability Enhancement:** automation roadmap TBD-3 (outside the current cycle): smoke suite → P0/P1 dev suite → boundary/negative; the partially/not-automatable remainder — documented exclusions with reasons.

#### Key Milestones
| Milestone | Deliverables | Acceptance Criteria |
|---|---|---|
| Entry criteria met | Input-check protocol: stands, accounts (re-creation/state recording), baseline data, versions, DEP-3 check | All items of 8.1 confirmed |
| Dev stage completed | Dev run log: the full suite of 19 scenarios, matrix TC-C-001 (6 cells), TC-PF-001 measurements, exploratory session records | 8.3 dev: 100% P0/P1 Passed, 0 open Criticals, artifacts complete |
| Baselines recorded | Baseline greeting strings (DEP-1), error text (DEP-4), highlighting-appearance baseline, actual-basis markers (DEP-2) | Baselines recorded in the log; observations converted where confirmed |
| Production smoke completed | Log of 18 runs (3 scenarios × 6 cells) + artifacts per 9.2/9.3 | 8.3 production: 18 out of 18 Passed, 0 open Criticals |
| Cycle report and baseline | Report with KQI facts, defect registry (D-10), RR-1…RR-3 in the release checklist | All scenarios executed/accounted per rules 9.1; baseline recorded |

#### Success Criteria
- **Quality Objective Achievement:** the 8.3 thresholds of both stages achieved; BR-1…BR-10 covered (spec traceability 6.1/6.2); conditional oracles resolved (baselines recorded).
- **Efficiency Objective Achievement:** all planned runs executed or accounted (Passed/Failed/Blocked/N/A/Excluded/Observation per 9.1); repeat launches performed per rule D-20; KQI facts collected for the baseline.
- **Process System Establishment:** the evidence chain 9.2 complete for every run; log/defect registry/report maintained in the repository (TBD-4); regression policy TBD-2 followed.

---

## Summary and Recommendations

### Strategy Summary
- **Core Philosophy:** a risk-driven and input-driven strategy: every element traces to decisions D-1…D-27, scenarios TC-*, dependencies DEP-1…DEP-4, and residual risks RR-1…RR-3; no fabrication — oracles are either exact or invariant/conditional with an explicit temporary status (9.1).
- **Key Elements:** two stages (dev full coverage → production smoke, 18 runs); invariant pass rules before baselines (D-22/D-24/D-26/D-27); the observation mechanism for DEP-1…DEP-4; regression policy (TBD-2); exploratory sessions (TBD-1); the repository as the management tool (TBD-4); automation roadmap (TBD-3).
- **Expected Effects:** verification of AI chat entry across the entire matrix D-1 with a measurable outcome (8.3); baseline strings and basis markers for subsequent cycles; the first cycle's KQI baseline (TBD-5).
- **Success Factors:** availability of the "AI Mode" button (DEP-3, input check); data re-creation discipline on dev (D-21/D-25); artifact completeness 9.2/9.3; timely recording of first-run baselines.

### Implementation Recommendations
#### Short-term Recommendations
- **Priority Ranking:** first — P0 (TC-P-001, TC-S-001); the TC-C-001 baseline cell before the full matrix run; the DEP-3 input check on every stand before the start.
- **Quick Wins:** the D-13 smoke suite as a filter after fixes (TBD-2); recording the actual state of production accounts (RR-2) before the production stage.
- **Risk Control:** conditional DEP-2 oracles (the TC-B-003 astral set as a basis marker); invariants D-22/D-24 for greetings; the indicative label for D-7 measurements.

#### Medium-term Recommendations
- **System Improvement:** the automation roadmap (TBD-3, owner: "we aim to automate all scenarios"): stage 1 — smoke suite (TC-P-001, TC-N-001, TC-N-003, ≈100% automatable); stage 2 — P0/P1 dev suite; stage 3 — boundary/negative/compatibility; the partially/not-automatable remainder (physical weak network on devices, the observational production TC-N-005, human approval of baselines) — documented exclusions.
- **Tool Optimization:** conditional recommendation of Playwright/Selenium (the input does not fix a tool); grid for desktop cells; device automation for mobile — conditional.
- **Capability Enhancement:** converting observation statuses as DEP-1…DEP-4 are confirmed; reusing baselines.

#### Long-term Recommendations
- **Continuous Improvement:** KQI target values — after the first cycle's baseline (TBD-5); revising the regression policy and exploratory charters per findings.
- **Innovation Practices:** expanding exploratory charters per discovered areas; automation as a permanent vector (TBD-3).
- **Value Creation:** stable AI chat entry as the foundation of trust in the feature; the baseline base for regression of subsequent releases.

### Risk Reminders
- **Implementation Risks:** baseline recordings require a first run (DEP-1/DEP-4); conditional DEP-2 oracles until confirmation; button availability (DEP-3) may block a run.
- **Technical Risks:** production specifics covered only by smoke (RR-3); resources not tested (RR-1); the highlighting hue across platforms.
- **External Risks:** button rollout/flags; AI service unavailability on production (TC-N-005 — observational); uncontrolled network for indicative measurements.

---

## TBD Questions (registry)

All of the strategy's TBDs were closed in round 1 (2026-09-28) by the requirements owner's answers; open TBDs — 0. Each item appears in the registry exactly once.

| # | TBD Location (section) | Question | Resolution (closure source) |
|---|---|---|---|
| TBD-1 | Test Methods and Strategies — Exploratory Testing | Should exploratory testing be included, and with which charters/timeboxes? | Closed by the owner's answer (round 1, 2026-09-28, Q-1): include — 3 charters (chat entry and greeting; account states; input field and limit) × 2 sessions of 60 minutes each; session records in the run log |
| TBD-2 | Test Methods and Strategies — Regression Testing; Implementation Plan | What are the scope and frequency of regression testing? | Closed by the owner's answer (round 1, 2026-09-28, Q-2): after every defect fix — a repeat of the affected scenarios + the smoke suite (TC-P-001, TC-N-001, TC-N-003); before starting production smoke — a full regression of the dev suite (19 scenarios) |
| TBD-3 | Test Objectives and Scope — Automation Potential (forecast); Summary and Recommendations | Are there automation plans outside the current manual cycle, and any tool preferences? | Closed by the owner's custom answer (round 1, 2026-09-28, Q-3): "we aim to automate all scenarios" — the strategy includes an automation roadmap for the entire suite (smoke → P0/P1 → boundary/negative) + a technical forecast (fully ≈68% / partially ≈32% / not-automatable remainder ≈5–10% with reasons); the tool — a conditional recommendation (Playwright/Selenium) |
| TBD-4 | Test Environment and Tools — Test Management Tools | What tool should manage the plan, scenarios, runs, and defects? | Closed by the owner's answer (round 1, 2026-09-28, Q-4): files in the repository (Markdown tables) next to the spec — the plan, run log, defect registry, cycle report |
| TBD-5 | Risk Management and Quality Control — KQI | How to handle observable metrics that have no target values in the input? | Closed by the owner's answer (round 1, 2026-09-28, Q-5): collect as facts of the first cycle; set target values after the baseline (baseline-after-first-cycle) |

Finalization: the owner confirmed the strategy's readiness to proceed to the plan (final question Q-6 — "yes", 2026-09-28); Version 2 — the final version of the artifact.

Note: items closed neither by the owner's answers nor by analysis are absent from the strategy. The external dependencies DEP-1…DEP-4 (exact greeting texts; the actual basis of the limit count; button rollout availability; the AI service's error text) — are not TBDs: these are implementation confirmations with temporary status rules 9.1 (observation/input check), handled per the spec's mechanism (10.2), converted at the first run.