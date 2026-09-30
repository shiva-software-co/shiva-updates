Version: 5
Requirement hash: 7ade9ed07d7d970e0f0204baf586a39b4a1b6367d8f00163b0ba90178c712105
Date: 2026-09-24
Summary: Requirements analysis for the "Google AI Mode — entering an empty chat with a personalized greeting" scenario. Rounds 1–5: 27 of 27 owner questions closed (D-1…D-27); the execution-stage conflict resolved (D-12: dev.google.com — full coverage; production www.google.com — smoke, 3 scenarios × 6 cells, D-13/D-23); guest behavior clarified (D-24: entering the chat without authorization); the reference test-history data set defined (D-25: 2–3 completed chats with 3–5 messages each); the input-field overflow model specified (D-26: red highlight + button disabled at >32,000 code points, recovery at ≤32,000 code points; D-27: button disabled when the field is empty). Open questions: 0; readiness is conditional — 4 external dependencies (DEP-1…DEP-4). Scenarios: 19 (P0 — 2, P1 — 11, P2 — 6). Residual risks: 3 (RR-1…RR-3).

## Input Audit

**Known:**
- Target URL: https://www.google.com
- Precondition: the page displays a search query field, an "AI Mode" button, and a "Google Search" button
- Trigger: pressing the "AI Mode" button
- Expected result: transition to an empty chat
- Expected result: greeting — the exact string "Hello, Ivan! What are you interested in?"
- Expected result: the message history is empty
- Expected result: a message input field is displayed
- The greeting is personalized with the name "Ivan", the text is in Russian
- (Round 4, owner's fact) An unauthenticated user can enter the chat without authorization → D-24
- (Round 5, owner's fact) When the input field overflows, it is highlighted in red; the send button is disabled → D-26

**Missing:**
- Target platforms and compatibility matrix → closed by Q-1 (D-1)
- Account states that change the oracle → closed by Q-2 (D-2); greeting oracles for alternative states → closed by Q-22 (D-22, invariants; exact texts — DEP-1); guest behavior → clarified by the owner in Q-24 (D-24)
- Functional coverage (sending messages) → closed by Q-3 (D-3)
- Negative flows → list closed by Q-4 (D-4); oracles for each flow → closed by Q-14…Q-18 (D-14…D-18); exact AI-service error text → DEP-4
- Input field length limit and counting basis → limit closed by Q-5 (D-5); overflow model (highlight/disable/recovery) closed by Q-26 (D-26); button state when the field is empty closed by Q-27 (D-27); the actual counting mechanism → DEP-2
- Security coverage → XSS closed by Q-6 (D-6); session security included by Q-19 (D-19)
- Target performance indicators → closed by Q-7 (D-7); rerun rule closed by Q-20 (D-20); resource consumption excluded by the owner in Q-20 (D-20 → RR-1)
- Stands and execution stages → closed by Q-12 (D-12: dev — full coverage, production — smoke); the conflict D-8 ↔ D-11 resolved by the same answer
- Test data → account matrix closed by Q-9 (D-9); preparation rules closed by Q-21 (D-21: dev — recreation, production — as is); the content of the reference test-history data set → defined by Q-25 (D-25: 2–3 completed chats with 3–5 messages each)
- Defect taxonomy → closed by Q-10 (D-10)
- Exit criteria → thresholds closed by Q-11 (D-11); smoke composition closed by Q-13 (D-13); smoke platform coverage closed by Q-23 (D-23: the entire D-1 matrix, 18 runs)

**Conflicting:**
- Conflict D-8 ↔ D-11 (round 1: "full coverage is not performed" vs thresholds for full coverage) — resolved by the owner's answer to Q-12 (D-12): full coverage is performed on dev.google.com; production — smoke set only. No active conflicts.
- The guest invariant from D-22 ("the button is hidden or the transition comes with a login prompt") contradicted the owner's fact from round 4 ("entering without authorization is possible") — resolved by Q-24 (D-24): the guest enters the chat without a login prompt. No active conflicts.
- The TC-B-001 oracle from v1–v4 ("acceptable outcomes: truncation to 32,000 / additional input is ignored") contradicted the owner's round 5 feedback ("on overflow the field is highlighted red, the button is disabled") — resolved by Q-26 (D-26): the field accepts over-limit text; red highlight + disabled button. No active conflicts.

**Stale/Outdated:**
- D-8 (Q-8, round 1: "smoke set only on production, full coverage is not performed") refined by the answer to Q-12 (D-12): the smoke restriction applies to production; full coverage is performed on the dev stand. The current wording of D-12 is used; D-8 remains in the registry as the source of the conflict.
- The guest invariant from D-22 ("the 'AI Mode' button is either hidden, or the transition is accompanied by a login prompt") replaced by decision D-24 (round 4): the button is visible, transition to an empty chat without a login prompt, greeting without a name. The wording of D-24 is current; D-22 remains in the registry (the "no name" and "EN" invariants remain in effect).
- The overflow oracle "truncation to 32,000 / additional input is ignored" (v1–v4, TC-B-001) replaced by the D-26 model (round 5): the field accepts text beyond the limit; at >32,000 code points — red highlight and a disabled button; upon return to ≤32,000 code points — normal state (the highlight disappears, the button becomes active). The D-26 model is current.

**Out-of-scope (owner exclusions):**
- Resource consumption (page weight, memory) — excluded by the owner in Q-20 (D-20) → RR-1
- Full coverage on production — not performed (D-12: full coverage on dev only; production — smoke) → RR-3
- Data preparation/cleanup on production excluded (Q-21, D-21: "used as is") → RR-2
- Search query field limits — out of scope: the field is only checked for visibility in the precondition (round 5; the clarification concerned the message input field)

# Requirements Analysis Report

## 0. Document Header

- Version: 5; date: 2026-09-24; changes: v1 — round 1 (11 questions); v2 — round 2 (11 questions; the stage conflict resolved by D-12; oracles for negative flows and states); v3 — round 3 (Q-23 → D-23: smoke on the entire D-1 matrix) + Self-Check; v4 — round 4 per feedback (Q-24 → D-24: guest oracle — entering the chat without authorization; Q-25 → D-25: reference test-history data set); v5 — round 5 per feedback (Q-26 → D-26: overflow model — red highlight + button disabled at >32,000 code points, recovery at ≤32,000 code points; Q-27 → D-27: send button disabled when the field is empty).
- Numeric statements (verified in Section 11): scenarios — 19 (P0 — 2, P1 — 11, P2 — 6); decisions in the registry — 27 (D-1…D-27); open questions — 0; external dependencies — 4 (DEP-1…DEP-4); residual risks — 3 (RR-1…RR-3); active conflicts — 0. Readiness is conditional per DEP-1…DEP-4 (zero-questions rule: the statement "open questions: 0" is given with a non-empty dependencies registry — readiness is stated as conditional, affected scenarios keep temporary status rules per 9.1).

## 1. Business Background

### 1.1 Business Objectives
- The input implies a single objective: entering the AI chat ("AI Mode") from the google.com page with a personalized greeting and an empty message history. Business objectives beyond this are not set by the input.

### 1.2 User Roles
- **Authorized user "Ivan" (RU locale, name set):** the primary role of the scenario; greeting oracle — the exact string (test-ivan, D-9).
- **Authorized user without a name (RU):** D-2; oracle — the D-22 invariant (greeting without empty substitution and without someone else's name); exact text — the first-run reference (DEP-1).
- **Authorized user with a non-RU locale (name "John", EN):** D-2; oracle — the D-22 invariant (English-language greeting with the name "John").
- **Unauthorized user (guest):** D-2; oracle — D-24: the "AI Mode" button is visible, transition to an empty chat without a login prompt, greeting without a name and without empty substitution; exact text — the first-run reference (DEP-1).
- **Authorized user with existing chat history:** D-2 (test-history; reference history — D-25: 2–3 completed chats from past sessions with 3–5 messages each); re-entry rule — D-15 (always a new empty chat).

### 1.3 Business Value
- The personalized greeting ("Hello, Ivan! What are you interested in?") lowers the barrier to entering an AI dialogue; an empty chat provides a clean starting point. Access without authorization (D-24) broadens the entry audience. Explicit overflow indication and blocking of invalid sends (D-26/D-27) prevent input loss and invalid messages. (Only what follows from the input and the owner's answers.)

### 1.4 Business Rules
- **BR-1:** on https://www.google.com a search field, an "AI Mode" button, and a "Google Search" button are displayed (precondition). → TC-P-001, TC-C-001
- **BR-2:** pressing "AI Mode" takes the user to an empty chat; for an unauthorized user — without a login prompt (D-24). → TC-P-001, TC-P-005, TC-P-006, TC-C-001
- **BR-3:** in the new chat the greeting "Hello, Ivan! What are you interested in?" is displayed (personalization by name + RU locale; the exact string is the oracle). For alternative states, the invariants of D-22 and D-24 apply (guest: greeting without a name, without empty substitution). → TC-P-001, TC-P-003…006, TC-S-001
- **BR-4:** the message history of the new chat is empty. → TC-P-001, TC-P-006
- **BR-5:** the chat has a message input field. → TC-P-001, TC-B-001, TC-S-002
- **BR-6 (D-3):** basic send — the sent message appears in the history; the AI response appears in the history. → TC-P-002
- **BR-7 (D-5, D-26, D-27):** the message input field limit is 32,000 Unicode code points; the basis is code points. Indication model (D-26): 31,999/32,000 code points — field without highlight, send button active (sending 32,000 code points is possible); 32,001+ code points — the field accepts the text and is highlighted red, the send button is disabled; when the text is reduced to ≤32,000 code points the highlight disappears and the button becomes active. With an empty field (0 code points) the send button is disabled (D-27). Divergence of bases from HTML maxlength (UTF-16 units) → DEP-2; oracles conditional until confirmation. → TC-B-001…003
- **BR-8 (D-6):** XSS protection — the account name (in the greeting) and message text are rendered literally; scripts do not execute. → TC-S-001, TC-S-002
- **BR-9 (D-15):** re-entering "AI Mode" always opens a new empty chat; existing history (reference — D-25) is not loaded. → TC-N-002, TC-P-006
- **BR-10 (D-19):** session security — on session expiry in an open chat, an observable reaction (login prompt/redirect); access to someone else's chat without authorization is impossible. → TC-S-003

---

## 2. Test Scope

### 2.1 Functional Scope
**Included Functional Modules:**
- Transition from google.com to an empty AI Mode chat (verifying precondition elements + click), including for an unauthorized user without a login prompt (D-24).
- Display of the greeting (exact string / invariants D-22, D-24), empty history, input field.
- Basic sending of one message and appearance of the AI response (D-3).
- Account state matrix (D-2) with invariant oracles (D-22, D-24) and the reference test-history data set (D-25).
- Negative flows (D-4) with oracles D-14…D-18.
- Boundary values of the input field (D-5) with the overflow indication and button-disable model (D-26/D-27).
- XSS: account name + input field (D-6); session security (D-19).
- Compatibility: platform matrix (D-1).
- Performance: transition time (D-7) with the rerun rule (D-20).

**Excluded Functional Modules (each with a decision registry entry and an RR):**
- Resource consumption (page weight, memory) — D-20 → RR-1.
- Full coverage on production — D-12 (full coverage is performed on dev.google.com only) → RR-3.
- Test data preparation/cleanup on production — D-21 ("as is") → RR-2.
- Search query field limits — out of scope: the field is only checked for visibility in the precondition (the round 5 clarification concerns the message input field).

**Precondition Coverage Obligation:** precondition elements are covered: search query field → TC-P-001 (visibility), "AI Mode" button → TC-P-001 (visibility + click), "Google Search" button → TC-P-001 (visibility); for matrix cells — TC-C-001. There are no uncovered precondition elements.

### 2.2 Test Types
- **Functional Testing:** transition to the chat (including as a guest without authorization — D-24), greeting, empty history, input field, basic send, re-entry.
- **UI/UX Testing:** presence and state of chat elements, including input field highlight on overflow and send button activity (D-26/D-27); text is verified as text (DOM/transcription), not by pixels; the highlight is a visual state (screenshot).
- **Security Testing:** XSS (account name, input field) — D-6; session security — D-19 (session expiry, access without authorization).
- **Performance Testing:** transition time (median ≤ 1,000 ms, 10 runs, indicative; +20% retry rule — D-20); resource consumption excluded (D-20 → RR-1).
- **Compatibility Testing (D-1):** desktop — Chrome, Firefox, Edge (Windows 11), Safari (macOS 14), latest stable versions (exact versions are recorded in the run report); mobile web — Safari on iPhone 15 (iOS 17), Chrome on Pixel 8 (Android 14).

### 2.3 Test Environment
- **Two-stage plan (D-12):**
  - **Stage 1 — dev stand `https://dev.google.com`:** the full volume of all scenarios.
  - **Stage 2 — production `https://www.google.com`** (in the owner's answer: google.com): smoke set (D-13: TC-P-001, TC-N-001, TC-N-003) on the entire D-1 matrix — 3 scenarios × 6 cells = 18 runs (D-23).
  - Stage order: dev → production (the order is set by the owner's wording in D-12: Dev — then Prod; the production smoke is performed after the dev stage exits).
- Access: test accounts of the D-9 matrix; guest session — without sign-in (D-24); availability of the "AI Mode" button for test accounts (rollout/flags) is not confirmed → DEP-3 (entry check on each stand).
- Data isolation: dev — accounts are recreated before each run; production — accounts are used as is, the state is recorded in the report (D-21; RR-2).

### 2.4 Test Data Requirements
- **Account matrix (D-9; the tester creates the data; on dev — recreation before each run with state setup, D-21):**
  - test-ivan — name "Ivan", RU locale, empty chat history;
  - test-noname — name not set, RU;
  - test-en — name "John", EN locale;
  - test-payload — name "<script>alert(1)</script>";
  - test-history — name "Ivan", RU, reference history (D-25): 2–3 completed AI Mode chats from past sessions, with 3–5 messages each; when recreated on dev, the reference history is restored (the same 2–3 chats are created with the same number of messages);
  - unauthorized (guest) session — without signing into an account; transition to the chat without a login prompt (D-24).
- **Per-stand rules (D-21):** dev — accounts are recreated before each run (matrix states are set at creation, including the reference test-history per D-25); production — accounts are used as is, the actual state is recorded in the run report (RR-2).
- **Boundary sets (D-5):** strings of 31,999 / 32,000 / 32,001 code points; astral set — a string of astral emoji (e.g., U+1F600: 1 character = 1 code point = 2 UTF-16 units) at the boundary (DEP-2).
- **Security sets:** payload for the name "<script>alert(1)</script>"; payloads for the input field: `<script>alert(1)</script>`, `<img src=x onerror=alert(1)>`.
- **Reference devices (D-1):** iPhone 15 (iOS 17, Safari); Pixel 8 (Android 14, Chrome).
- **Expected oracle strings from the input:** greeting "Hello, Ivan! What are you interested in?"; invariants D-22/D-24 for alternative states (exact texts — first-run reference, DEP-1); field/button state invariants (D-26/D-27): red highlight at >32,000 code points, button disabled on overflow and when the field is empty.

---

## 3. Test Scenario Design

**Common column rules:** Expected Result — deterministic (exact text/state/value or an enumeration of acceptable observable outcomes); conditional oracles are marked with a DEP-N reference and a temporary status (9.1). Applicability — platforms of the D-1 matrix; N/A rule — platforms/browsers outside the D-1 matrix. Evidence Artifact — artifact + environment metadata (9.2). Execution stand: full set — dev.google.com (D-12); smoke set (TC-P-001, TC-N-001, TC-N-003) — additionally on production.

### 3.1 Positive Scenarios (Happy Path)
**Scenario Category:** Core business processes

| Scenario ID | Scenario Description | Test Focus | Expected Result (Oracle / Pass rule) | Priority | Design Method | Applicability (N/A rule) | Evidence Artifact |
|---|---|---|---|---|---|---|---|
| TC-P-001 | Transition to an empty chat (test-ivan) | Precondition; clicking "AI Mode"; the 4 expectations of the input | Precondition visible: search field, "AI Mode" button, "Google Search" button. After the click: (a) greeting "Hello, Ivan! What are you interested in?" — exact string, compared as text; (b) message history is empty; (c) message input field is present and available for input | P0 | Scenario / State transition | All platforms of the D-1 matrix; N/A: platforms/browsers outside the D-1 matrix | Screenshot + greeting text (DOM/transcription) + transition video |
| TC-P-002 | Basic message send | Sending one message in an empty chat | The user's message appears in the history with the exact sent text; after it, a non-empty AI response element appears | P1 | Scenario / State transition | All platforms of D-1; N/A: outside the matrix | History screenshot + message text as text |
| TC-P-003 | Account without a name (test-noname) | State: name not set | Transition to an empty chat; the greeting is displayed and (invariant, D-22) contains no empty substitution like "Hello, !" and no someone else's name; the exact text is recorded as the reference on the first run (DEP-1) | P1 | ECP (state classes) | All platforms of D-1; N/A: outside the matrix | Screenshot + greeting text as text |
| TC-P-004 | Non-RU locale (test-en, John) | State: EN locale | Transition to an empty chat; the greeting is in English and contains the name "John" (invariant, D-22); exact text — first-run reference (DEP-1) | P1 | ECP | All platforms of D-1; N/A: outside the matrix | Screenshot + greeting text as text |
| TC-P-005 | Unauthorized user (guest) | State: guest session without sign-in | The "AI Mode" button is visible on the google.com page; the click performs the transition to an empty chat **without a login prompt** (D-24); the greeting is displayed without a name and without empty substitution like "Hello, !" (D-24); message history is empty; input field present; exact greeting text — first-run reference (DEP-1) | P1 | ECP / access check | All platforms of D-1; N/A: outside the matrix | Screenshot + greeting text as text + transition video |
| TC-P-006 | Existing chat history (test-history) | State: reference history — 2–3 completed chats from past sessions with 3–5 messages each (D-25) | Transition to a new empty chat (BR-9, D-15): greeting "Hello, Ivan! What are you interested in?" (exact string); the new chat's history is empty — past chats' content (D-25) is not loaded | P1 | ECP / State transition | All platforms of D-1; N/A: outside the matrix | Screenshot + greeting text |

### 3.2 Negative Scenarios (Negative Path)
**Scenario Category:** Exception handling, error handling

| Scenario ID | Scenario Description | Test Focus | Expected Result (Oracle / Pass rule): trigger → reaction and timing → what next | Priority | Design Method | Applicability (N/A rule) | Evidence Artifact |
|---|---|---|---|---|---|---|---|
| TC-N-001 | Double/triple press of "AI Mode" | Fast repeated clicks | Trigger: ≥2 fast clicks on the button. Reaction (D-14): exactly one chat opens; no duplicate chats are created; one greeting is displayed. Next: the user can work in one chat | P1 | Error guessing | All platforms of D-1; N/A: outside the matrix | Transition video + screenshot of the final state |
| TC-N-002 | "Back" from the chat and re-entry | S0→S1→S0→S1 | Trigger: "back" from the empty chat, then a repeated click on "AI Mode". Reaction: return to the google.com page (precondition elements visible); the repeated click opens a new empty chat with the greeting "Hello, Ivan! What are you interested in?" (D-15, BR-9). Next: the chat is functional | P1 | State transition | All platforms of D-1; N/A: outside the matrix | Video |
| TC-N-003 | Page refresh in the chat | Reload in an empty chat | Trigger: page refresh in an empty chat. Reaction (D-16): the chat remains empty — the greeting, empty history, and input field are displayed after the reload. Next: input/send is possible | P1 | Error guessing | All platforms of D-1; N/A: outside the matrix | Before/after screenshots + greeting text as text |
| TC-N-004 | Network interruption during transition | Connection loss at the moment of the click | Trigger: connection loss at the moment of clicking "AI Mode". Reaction (D-17): no infinite loading indicator; the chat does not open in a partial state; after the network is restored, a repeated click opens the empty chat with the greeting. Next: the retry is available | P2 | Error guessing | All platforms of D-1; mobile web — weak networks; N/A: outside the matrix | Video + screenshots |
| TC-N-005 | AI service unavailability | Transition with the service unavailable | Trigger: pressing "AI Mode" with the AI service unavailable. Reaction (D-18): a visible error message in the interface (exact text — implementation, DEP-4; recorded as the reference); a retry is possible; no infinite indicator. Next: the retry leads to a transition once the service recovers. On production it is intentionally not reproduced — the scenario is observational (an incident/simulation on dev) | P2 | Error guessing | All platforms of D-1; N/A: outside the matrix | Screenshots + error text as text |

**Key Negative Scenarios:**
- **Input Validation Exceptions:** TC-B-002 (empty field — button disabled, D-27), TC-B-001 (overflow — red highlight + button disabled, D-26).
- **System Exceptions:** TC-N-004 (network interruption), TC-N-005 (AI service unavailability).
- **Operation Exceptions:** TC-N-001 (double press), TC-N-002 (reverse operation), TC-N-003 (interruption by reload).
- Each scenario defines: trigger → observable reaction and its timing → what the user can do next (filled in per D-14…D-18; for the input field — per D-26/D-27).

### 3.3 Boundary Scenarios (Boundary Cases)
**Scenario Category:** Boundary values, critical conditions

| Scenario ID | Scenario Description | Test Focus | Boundary Values (basis — code points, D-5) | Expected Result (Oracle / Pass rule) | Priority | Design Method | Applicability (N/A rule) | Evidence Artifact |
|---|---|---|---|---|---|---|---|---|
| TC-B-001 | Input field limit (max) and overflow indication model | Limit enforcement; highlight; button state; recovery | 31,999 / 32,000 / 32,001 code points | (D-26): 31,999 code points — field without highlight, send button active; 32,000 code points — no highlight, button active, sending is possible (a message with the exact 32,000-code-point text appears in the history); 32,001+ code points — the field accepts the text in full and is highlighted red, the send button is disabled; when the text is reduced to ≤32,000 code points the highlight disappears, the button becomes active. Basis — code points (D-5); oracle conditional until DEP-2 is confirmed | P1 | BVA (basis — code points) + State transition | All platforms of D-1; N/A: outside the matrix | Field value (code-point count) + screenshots of the highlight/its absence + button state (active/disabled) + history screenshot when sending 32,000 code points |
| TC-B-002 | Empty message (min) | Button state with an empty field | 0 code points | (D-27): with an empty field the send button is disabled (inactive); clicking is impossible; the message is not sent — no new message appears in the history | P2 | BVA / ECP | All platforms of D-1; N/A: outside the matrix | Screenshot of the button state + history screenshot |
| TC-B-003 | Astral characters at the boundary | Checking the counting basis at the boundary | A string of 32,000 astral emoji (U+1F600): 32,000 code points = 64,000 UTF-16 units | If the implementation counts code points (D-5): no overflow — the field is without highlight, the send button active (sending possible). If it counts UTF-16 units: the state is recorded as overflow — the field is highlighted red, the button is disabled (D-26). Oracle conditional: recorded after DEP-2 is confirmed (the basis divergence is marked per the Limit Basis Rule); the highlight and button state are observable markers of the actual basis during the run | P2 | BVA (basis) | All platforms of D-1; N/A: outside the matrix | Field value (counted by both bases) + screenshot of the highlight/its absence + button state |

### 3.4 Security Scenarios (Security Cases)
**Scenario Category:** Security vulnerabilities, permission control, session security

| Scenario ID | Scenario Description | Test Focus | Expected Result (Oracle / Pass rule) | Priority | Design Method | Applicability (N/A rule) | Evidence Artifact |
|---|---|---|---|---|---|---|---|
| TC-S-001 | XSS in the account name (test-payload) | Rendering the name in the greeting | alert(1) does not execute (no alert dialogs); the name "<script>alert(1)</script>" is displayed in the greeting as literal text; the main chat elements are visible (greeting, history, input field) | P0 | Error guessing / Security | All platforms of D-1; N/A: outside the matrix | Screenshot + greeting text as text (DOM) |
| TC-S-002 | XSS in the message input field | Sending a payload in a message | The message in the history is displayed literally; the script does not execute; the main chat elements are visible | P1 | Error guessing / Security | All platforms of D-1; N/A: outside the matrix | Screenshot + text as text |
| TC-S-003 | Session security | Session expiry; access without authorization | (a) User session expiry in an open chat — observable reaction (D-19): a login prompt or redirect to the login page; the chat does not remain accessible without authorization. (b) Attempt to access someone else's chat without authorization — access is impossible (login prompt/denial). The method of triggering session expiry is recorded in the run report. Does not contradict D-24: the guest opens their own new empty chat, but not someone else's | P2 | Error guessing | All platforms of D-1; N/A: outside the matrix | Screenshots of the reactions + texts as text |

### 3.5 Performance Scenarios (Performance Cases)
**Scenario Category:** Response time

| Scenario ID | Scenario Description | Start Marker | End Marker | Runs & Aggregation | Threshold | Priority | Applicability | Evidence Artifact |
|---|---|---|---|---|---|---|---|---|
| TC-PF-001 | Transition time to the chat (test-ivan) | Click on the "AI Mode" button | Greeting visible on screen (an observable element with the greeting text) | 10 runs, median; repeat rule (D-20): median in 1,000–1,200 ms → 10 additional runs | ≤ 1,000 ms (D-7); indicative (the network is not controlled) | P2 | All platforms of D-1 (separate measurements per cell) | Measurement report: values + median + metadata |

### 3.6 Compatibility Scenarios (Compatibility Cases)
**Scenario Category:** Browser, device, OS compatibility

| Scenario ID | Scenario Description | Test Focus | Expected Result (Oracle / Pass rule) | Priority | Design Method | Applicability (N/A rule) | Evidence Artifact |
|---|---|---|---|---|---|---|---|
| TC-C-001 | TC-P-001 oracle on the entire D-1 matrix | Cells: Chrome/Win 11, Firefox/Win 11, Edge/Win 11, Safari/macOS 14, Safari/iPhone 15 (iOS 17), Chrome/Pixel 8 (Android 14) — on the dev stand (D-12) | On each cell of the matrix the TC-P-001 oracle is executed; the greeting text is identical on all cells (compared as text, not by pixels) | P1 | Compatibility matrix (OATS is not applied — the matrix is set explicitly by the owner, D-1) | 6 cells of the D-1 matrix; N/A: platforms/browsers/devices outside D-1 | Per-cell artifact: screenshot + text + browser/OS version in metadata |

### 3.7 Platform Applicability Matrix

| Scenarios | Desktop web (Chrome/Firefox/Edge, Win 11) | Desktop web (Safari, macOS 14) | Mobile web (Safari, iPhone 15, iOS 17) | Mobile web (Chrome, Pixel 8, Android 14) |
|---|---|---|---|---|
| TC-P-001…TC-P-006 | executed (dev) | executed (dev) | executed (dev; touch input) | executed (dev; touch input) |
| TC-N-001…TC-N-005 | executed (dev) | executed (dev) | executed (dev) | executed (dev) |
| TC-B-001…TC-B-003 | executed (keyboard) | executed (keyboard) | executed (touch input) | executed (touch input) |
| TC-S-001…TC-S-003 | executed | executed | executed | executed |
| TC-PF-001 | executed (separate measurements) | executed | executed (indicative, mobile networks) | executed (indicative) |
| TC-C-001 | reference cells (dev) | reference cell (dev) | reference cell (dev) | reference cell (dev) |
| Smoke on production (TC-P-001, TC-N-001, TC-N-003) | executed (production, D-23) | executed (production, D-23) | executed (production, D-23; touch input) | executed (production, D-23; touch input) |

- **Shared coverage:** all functional scenarios are executed on all platforms of D-1 (dev stand) with a single oracle; text is compared as text.
- **Platform-specific:** input modality (keyboard/touch) for TC-B, TC-S; TC-PF-001 measurements per each cell; production smoke is executed on all 6 cells (D-23).
- **Cross-platform consistency:** the greeting text is identical on all platforms (compared as text); highlight/button behavior (D-26/D-27) is uniform on all platforms — the visual state is recorded by screenshot, the button state by observable clickability.
- **N/A rules:** platforms/browsers/devices outside the D-1 matrix — N/A; the N/A condition is stated explicitly.

---

## 4. Test Methods

### 4.1 Test Design Method Application

| Test Method | Application Scenario | Specific Application Description |
|---|---|---|
| Scenario Testing | End-to-end flow google.com → empty chat → message (authorized and guest — D-24) | TC-P-001, TC-P-002, TC-P-005 (artifact: 4.1-B) |
| Equivalence Class Partitioning | Input field: valid — 1…32,000 code points (regular text; astral characters) — field without highlight, button active; invalid — empty string (button disabled, D-27), >32,000 code points (red highlight + disabled button, D-26), XSS payload (security class). Account states: {name set/not set} × {RU/EN} × {authorized/guest} × {history empty/present (reference D-25)} → matrix D-2/D-9; invariant oracles — D-22, D-24 | Classes listed; TC-P-003…006, TC-B-001…003, TC-S-001…002 |
| Boundary Value Analysis | 0, 1, 31,999, 32,000, 32,001 code points + astral set; basis — code points (D-5); divergence from HTML maxlength (UTF-16) → DEP-2; basis markers — highlight/button (D-26) | TC-B-001…003 (values in 3.3) |
| Decision Table/Cause-Effect | Greeting by conditions (authorization × name × locale) | Table 4.1-A (cells filled: exact string / invariants D-22, D-24) |
| State Transition Diagram | S0 google.com → S1 empty chat → S2 chat with messages; back/retry/reload/errors; input field states (normal/overflow/empty) | Table 4.1-B; rules D-15, D-16, D-26/D-27 |
| Orthogonal Array Testing | Not applied: the compatibility matrix is set explicitly by the owner (D-1), no reduction required | — (honest indication of non-application) |
| Error Guessing | Double press, page refresh, network interruption, empty field/field overflow, session expiry | TC-N-001, TC-N-003, TC-N-004, TC-B-001/002, TC-S-003 |

**Table 4.1-A. Decision Table (greeting):**

| Authorized? | Name set? | Locale | Expected greeting (oracle) |
|---|---|---|---|
| yes | yes ("Ivan") | RU | "Hello, Ivan! What are you interested in?" (exact string) |
| yes | no | RU | Invariant (D-22): the greeting is displayed, without the empty substitution "Hello, !" and without someone else's name; exact text — first-run reference (DEP-1) |
| yes | yes ("John") | EN | Invariant (D-22): English-language greeting with the name "John"; exact text — reference (DEP-1) |
| no (guest) | — | — | Invariant (D-24): the "AI Mode" button is visible; transition to an empty chat without a login prompt; greeting without a name and without empty substitution; exact text — first-run reference (DEP-1) |
| yes | yes ("Ivan") | RU, reference history present (D-25) | The greeting is the same (exact string); the new chat is empty — past chats' content (D-25) is not loaded (D-15, BR-9) |

**Table 4.1-B. State Transitions (observable markers):**

| From state | Event | To state | Observable marker |
|---|---|---|---|
| S0: google.com page | Click "AI Mode" | S1: empty chat | Greeting (exact text), empty history, input field |
| S0 (guest session, D-24) | Click "AI Mode" | S1: empty chat | Transition without a login prompt; greeting without a name; empty history; input field |
| S1: empty chat | Send message | S2: chat with messages | User's message with the exact text + non-empty AI response |
| S1: empty chat | "Back" | S0 | google.com page with precondition elements |
| S0 | Repeated click "AI Mode" (including after back) | S1: new empty chat | New chat: greeting, empty history (D-15) |
| S1 | Page refresh | S1 | The chat remains empty: greeting, empty history, input field (D-16) |
| S0 → S1 | Network interruption | S0 | No infinite indicator; no partial chat; retry after network restoration works (D-17) |
| S0 → S1 | AI service unavailability | S0 | Visible error; retry possible; no infinite indicator (D-18; text — DEP-4) |
| S1 (field: normal) | Enter text >32,000 code points (D-26) | S1 (field: overflow) | The field accepts the text and is highlighted red; the send button is disabled |
| S1 (field: overflow) | Reduce text to ≤32,000 code points (D-26) | S1 (field: normal) | The highlight disappears; the send button becomes active |
| S1 (field: empty) | — (stable state, D-27) | S1 (field: empty) | The send button is disabled; clicking is impossible |

### 4.1.1 OATS Combination Table
Not filled in: the OATS method is not declared (see 4.1) — no reduction is applied, the compatibility matrix is set explicitly by the owner (D-1).

### 4.2 Test Execution Methods

**Manual Testing:**
- **Applicable Scenarios:** all scenarios (verification through the UI; text is compared as text).
- **Execution Strategy:** first P0 (TC-P-001, TC-S-001), then P1, then P2; on the dev stand — the full set; the TC-C-001 matrix after the reference cell; TC-PF-001 measurements separately per cell.

**API Testing:** not applied — the API is not mentioned in the input.

**Performance Testing:**
- **Test Methods:** transition response-time measurements (10 runs, median; indicative); repeat rule: median 1,000–1,200 ms → 10 additional runs (D-20). Load/stress types are not set by the owner (D-7 sets only the transition time).
- **Performance Metrics:** ≤ 1,000 ms (D-7); resource consumption excluded (D-20 → RR-1).

**Execution plan per environment:**
- **Stage 1 — dev.google.com (full coverage, D-12):** all 19 scenarios; the TC-C-001 matrix (6 cells); TC-PF-001 per cell. Data: accounts are recreated before each run (D-21), including the reference test-history (D-25).
- **Stage 2 — production https://www.google.com (smoke, D-12/D-13/D-23):** TC-P-001, TC-N-001, TC-N-003 on the entire D-1 matrix — 3 scenarios × 6 cells = 18 runs (D-23); data — as is (D-21, RR-2). Performed after the dev stage exits (8.3).
- Isolation: dev — account recreation; production — no preparation, the state is recorded in the report (D-21).

---

## 5. Test Strategy Recommendations

### 5.1 Test Focus
- Greeting oracle accuracy (the string "Hello, Ivan! What are you interested in?", compared as text).
- Empty chat invariants (history empty, input field present) and the new-chat rule (BR-9, reference history D-25).
- Guest transition without a login prompt (D-24) and access boundaries (TC-S-003).
- Limit indication model: red highlight on overflow, send button disable/enable, state recovery (D-26/D-27).
- XSS rendering (account name, message); session reactions.
- Limit counting basis (code points vs UTF-16) — DEP-2; basis markers during the run — highlight/button (D-26).
- Data reproducibility: dev — recreation (including reference history D-25); production — recording the actual state (RR-2).

### 5.2 Risk Assessment

| Risk Item | Risk Level | Impact Scope | Mitigation Measures (testing/analytical actions) |
|---|---|---|---|
| The limit basis is not confirmed by the implementation (code points vs UTF-16) | Medium | TC-B-001…003 | DEP-2; astral set TC-B-003 — highlight/button as observable markers of the actual basis (D-26); conditional oracles until confirmation |
| Availability of the "AI Mode" button for test accounts (rollout) is not confirmed | Medium | Precondition of all scenarios (BR-1) | DEP-3; entry criterion (check on each stand) |
| The exact greeting texts of alternative states (no name, EN, guest) are not known in advance | Medium | TC-P-003…005 | Invariants D-22/D-24 — deterministic pass rules; first-run reference (DEP-1) |
| The exact AI-service error text — implementation | Low | TC-N-005 | The reaction class is set (D-18); text — reference (DEP-4) |
| AI service unavailability on production is intentionally not reproduced | Low | TC-N-005 | Observational scenario; simulation/incident |
| The shade of the "red" highlight may differ between platforms | Low | TC-B-001, TC-B-003 | The highlight is checked as a visual state (screenshot); the highlight appearance reference is recorded on the first run |
| The dev/production environment difference is covered only by smoke | Medium | Production-specific behavior | RR-3; smoke D-13 on the entire D-1 matrix (D-23) |

---

## 6. Test Coverage Analysis

### 6.1 Functional Coverage
- **Core Function Coverage:** transition to the chat (authorized and guest), greeting, empty history, input field (TC-P-001, TC-P-005, TC-C-001), basic send (TC-P-002), re-entry (TC-N-002, BR-9).
- **Edge Function Coverage:** account states (TC-P-003…006), boundaries with the indication model (TC-B-001…003), XSS and sessions (TC-S-001…003), negative flows (TC-N-001…005).

### 6.2 Scenario Coverage
- **Positive Scenarios:** 6 (TC-P-001…006); oracles: exact string (Ivan) + invariants D-22/D-24.
- **Negative Scenarios:** 5 (TC-N-001…005); oracles D-14…D-18 (trigger → reaction → what next).
- **Boundary Scenarios:** 3 (TC-B-001…003); values and the indication model are set (D-5, D-26, D-27), the basis is conditional until DEP-2.
- **Security Scenarios:** 3 (TC-S-001…003); session security included (D-19).
- **Performance:** 1 (TC-PF-001, repeat rule D-20); **Compatibility:** 1 (TC-C-001, 6 cells, dev).
- **Total: 19 (P0 — 2, P1 — 11, P2 — 6).**
- **Precondition elements ↔ TC mapping:** search field → TC-P-001/TC-C-001; "AI Mode" button → TC-P-001/TC-C-001 (visibility + click); "Google Search" button → TC-P-001/TC-C-001. None uncovered.
- **Decision Registry ↔ exclusions mapping:** resources → D-20/RR-1; full coverage on production → D-12/RR-3; data preparation on production → D-21/RR-2; search query field limits → out of scope (visibility only; round 5).
- All traceability rows reference a source (section, D-N, Q-N, DEP-N, RR-N).

### 6.3 Risk Coverage
- The risks of 5.2 are covered by scenarios or are managed through dependencies (DEP-1…DEP-3) and residual risks (RR-1…RR-3).

### 6.4 Test Method Coverage
- Every declared method has an artifact: ECP classes (4.1, including D-26/D-27 markers), BVA values with a basis (3.3), decision table 4.1-A (filled in, including the guest row D-24), transitions 4.1-B (rules D-15/D-16, guest transition D-24, input field states D-26/D-27), compatibility matrix (3.6). OATS is not declared.

---

## 7. Open Questions

> All questions Q-1…Q-27 (rounds 1–5) are closed by the requirements owner's answers — entries D-1…D-27 in the Decision Registry (10.1). There are no open questions: (a) all input gaps are closed, (b) no TBDs remain in the body of the report, (c) test data is defined (D-9, D-21, D-25), (d) the environment is defined (D-12). The remaining uncertainty concerns implementation mechanisms and is managed as external dependencies DEP-1…DEP-4 (10.2) with temporary status rules (9.1) — such items are closed neither by the owner's answers nor by analytical assumptions.

---

## 8. Entry and Exit Criteria

### 8.1 Entry Criteria (verify before execution starts)
- [ ] Stands available and confirmed: dev.google.com (stage 1) and production https://www.google.com (stage 2); access confirmed.
- [ ] Accounts of the D-9 matrix: on dev — recreated before the run with the matrix states (name, locale; test-history — with the reference history: 2–3 completed chats with 3–5 messages each, D-25); on production — they exist, actual states checked and recorded (D-21). The guest session is available without sign-in (D-24).
- [ ] Reference data ready: the string "Hello, Ivan! What are you interested in?"; invariants D-22/D-24; field/button indication model (D-26/D-27); boundary sets (31,999/32,000/32,001 code points; astral set); security payloads.
- [ ] Browser/OS/device versions recorded for the run (matrix D-1: exact versions at the time of the run).
- [ ] Availability of the "AI Mode" button for test accounts confirmed on each stand (DEP-3; rollout/flags).

### 8.2 Defect Severity Taxonomy
- Approved by the owner (D-10), 5 levels: **Blocking / Critical / Major / Minor / Trivial**. The phrase "critical defect" in the exit criteria means the "Critical" level of this taxonomy.

### 8.3 Exit Criteria (per environment stage, each measurable)
- **Stage 1 — dev.google.com, full coverage (D-12, D-11):** 100% P0/P1 Passed; 0 open Critical defects (D-10); artifacts complete for every executed scenario; residual risks documented. The criterion covers the dev cycle.
- **Stage 2 — production https://www.google.com, smoke (D-12, D-13, D-23):** 100% of the smoke set (TC-P-001, TC-N-001, TC-N-003) Passed on each of the 6 cells of the D-1 matrix (D-23) — 18 out of 18 runs; 0 open Criticals; artifacts complete. The criterion covers only the production smoke, not full coverage.
- Order: the production smoke is performed after the dev stage exits (the order comes from D-12).
- No thresholds are invented beyond the owner's answers; all stage parameters are set by the owner (D-11, D-12, D-13, D-23).

---

## 9. Result Status Rules, Artifacts, and Evidence Chain

### 9.1 Status rules
- **Passed** — the oracle/pass rule is met (for indicative timings: the median within the threshold, accounting for the repeat rule D-20).
- **Failed** — the oracle is violated.
- **Blocked** — an entry/critical precondition is not met; the blocker is recorded.
- **N/A** — the applicability rule says the scenario is not applicable (the condition is stated: platforms/browsers outside the D-1 matrix).
- **Excluded** — the owner's decision; reference to a Decision Registry entry (RR-1…RR-3).
- **Observation** — a temporary status for items that depend on open dependencies: the exact greeting texts of alternative states (TC-P-003…005 → DEP-1; pass rule — invariants D-22/D-24, deterministic), the limit basis (TC-B-001…003 → DEP-2; pass rule — the D-26/D-27 model, deterministic given a known basis), the exact AI-service error text (TC-N-005 → DEP-4), button availability (DEP-3 → entry check). Converted to a final status upon confirmation.
- The **indicative** label — TC-PF-001 timings (the network is not controlled).

### 9.2 Evidence chain and metadata
- Chain: scenario → oracle (pass rule) → artifact → report row. Each report row carries: stand (dev.google.com / production www.google.com), platform, browser + version, OS + version (for mobile — device model), run date, run ID.

### 9.3 Artifact feasibility rules
- Text is compared as text (extracted from the DOM/accurate transcription); screenshots prove only the visual/layout state.
- Timings — by observable markers (click → greeting visible), 10 runs, median, repeat rule D-20.
- Flows (navigation, transitions) — video.
- "The main chat elements are visible" (TC-S-001/002) — an enumeration: greeting, history, input field.
- Field highlight (red on overflow, D-26) — a visual state: screenshot; the highlight appearance is recorded as the reference on the first run. Send button state (active/disabled) — observable clickability + style screenshot.

---

## 10. Decision Registry, External Dependencies, Residual Risks

### 10.1 Decision Registry
| D-N | Decision | Owner attribution | Affected BR/TC | Closed question (Q-N) |
|---|---|---|---|---|
| D-1 | Platforms: desktop web — Chrome, Firefox, Edge (Windows 11), Safari (macOS 14), latest stable (versions recorded in the run report); mobile web — Safari on iPhone 15 (iOS 17), Chrome on Pixel 8 (Android 14) | Requirements owner (round 1) | BR-1, TC-C-001; applicability of all scenarios | Q-1 |
| D-2 | Full state matrix: name set/not set; RU/non-RU; authorized/unauthorized; history empty/exists | Requirements owner | BR-3, TC-P-003…006 | Q-2 |
| D-3 | Coverage includes basic send: one message is sent, the AI response appears in the history | Requirements owner | BR-6, TC-P-002 | Q-3 |
| D-4 | Full set of negative flows: network interruption; AI service unavailability; double press; back + re-entry; page refresh | Requirements owner | TC-N-001…005 | Q-4 |
| D-5 | Input field limit — 32,000 Unicode code points; boundary 31,999/32,000/32,001; basis — code points | Requirements owner | BR-7, TC-B-001…003 | Q-5 |
| D-6 | XSS coverage: account name (greeting) + input field; scripts do not execute, the payload is rendered literally | Requirements owner | BR-8, TC-S-001, TC-S-002 | Q-6 |
| D-7 | Transition time: indicative, median ≤ 1 s, 10 runs; markers: click → greeting rendered | Requirements owner | TC-PF-001 | Q-7 |
| D-8 | Smoke set only on production; full coverage is not performed (refined by D-12: the restriction applies to production) | Requirements owner | 2.3, 4.2, 8.3 | Q-8 |
| D-9 | Account matrix: test-ivan, test-noname, test-en, test-payload, test-history, unauthorized session | Requirements owner | 2.4, TC-P-001…006, TC-S-001 | Q-9 |
| D-10 | Taxonomy: 5 levels — Blocking / Critical / Major / Minor / Trivial | Requirements owner | 8.2, 8.3 | Q-10 |
| D-11 | Exit thresholds: full coverage — 100% P0/P1 Passed, 0 open Criticals; smoke — 100% of the set Passed | Requirements owner | 8.3 | Q-11 |
| D-12 | Two-stage plan: dev.google.com — full volume; production google.com (input: https://www.google.com) — smoke set; order dev → production. Resolves the conflict D-8 ↔ D-11: full coverage is performed on dev; the D-11 thresholds apply to the dev stage | Requirements owner (custom answer, round 2) | 2.3, 4.2, 8.3, RR-3 | Q-12 |
| D-13 | Smoke composition: TC-P-001 + TC-N-001 + TC-N-003 | Requirements owner | 8.3, 4.2 | Q-13 |
| D-14 | Double press: exactly one chat opens; no duplicates; one greeting | Requirements owner | TC-N-001 | Q-14 |
| D-15 | Re-entry always opens a new empty chat with the greeting "Hello, Ivan! What are you interested in?"; existing history is not loaded | Requirements owner | BR-9, TC-N-002, TC-P-006 | Q-15 |
| D-16 | Page refresh in the chat: the chat remains empty — the greeting, empty history, and input field are displayed after the reload | Requirements owner | TC-N-003 | Q-16 |
| D-17 | Network interruption: no infinite indicator; the chat does not open in a partial state; after the network is restored, a repeated click opens the empty chat with the greeting | Requirements owner | TC-N-004 | Q-17 |
| D-18 | AI service unavailability: a visible error message (exact text — implementation, DEP-4), a retry is possible, no infinite indicator | Requirements owner | TC-N-005 | Q-18 |
| D-19 | Session security included: session expiry in an open chat — observable reaction (login prompt/redirect); access to someone else's chat without authorization is impossible | Requirements owner | BR-10, TC-S-003 | Q-19 |
| D-20 | TC-PF-001 rerun rule: median in 1,000–1,200 ms → 10 additional runs; resource consumption excluded from coverage | Requirements owner | TC-PF-001, RR-1 | Q-20 |
| D-21 | Data preparation: on dev accounts are recreated before each run; on production accounts are used as is, the state is recorded in the report | Requirements owner (custom answer, round 2) | 2.4, 4.2, RR-2 | Q-21 |
| D-22 | Greeting invariants: no name → no empty substitution ("Hello, !") and no someone else's name; EN → English-language text with the name "John"; the part about the guest ("the button is hidden or a login prompt") replaced by decision D-24 | Requirements owner | TC-P-003…005, 4.1-A | Q-22 |
| D-23 | Smoke platform coverage on production: the entire D-1 matrix — 3 scenarios (TC-P-001, TC-N-001, TC-N-003) × 6 cells = 18 runs | Requirements owner (round 3) | 8.3, 4.2, 3.7 | Q-23 |
| D-24 | Guest: the "AI Mode" button is visible; the transition to an empty chat happens without a login prompt; the greeting is displayed without a name and without empty substitution (not "Hello, !"); exact text — first-run reference (DEP-1). Replaces the guest invariant from D-22 | Requirements owner (feedback + round 4) | BR-2, BR-3, TC-P-005, 4.1-A | Q-24 |
| D-25 | Reference test-history: several completed AI Mode chats from past sessions (2–3 chats with 3–5 messages each); a new chat does not load their content | Requirements owner (feedback + round 4) | 2.4, TC-P-006, BR-9 | Q-25 |
| D-26 | Input field overflow model: the field accepts text beyond the limit; 31,999/32,000 code points — no highlight, send button active (sending 32,000 code points is possible); 32,001+ code points — the field is highlighted red, the send button is disabled; when the text is reduced to ≤32,000 code points the highlight disappears, the button becomes active. Replaces the former "truncation/ignoring" oracle | Requirements owner (feedback + round 5) | BR-7, TC-B-001, TC-B-003, 4.1-B | Q-26 |
| D-27 | The send button is disabled with an empty field; clicking is impossible; the message is not sent | Requirements owner (round 5) | BR-7, TC-B-002 | Q-27 |

### 10.2 External Dependencies (implementation confirmations)
| DEP-N | What must be confirmed | Why it matters / affected scenario IDs | Temporary status rule until confirmed | Confirming role (or Open Question) |
|---|---|---|---|---|
| DEP-1 | The exact greeting texts for the states: no name, EN locale, guest (the guest transition behavior is set by D-24: without a login prompt) | TC-P-003…005; table 4.1-A | Pass rule — invariants D-22/D-24 (deterministic); exact texts are recorded as the reference on the first run; observation status for the reference strings | Implementation (AI Mode); reference recording during the run |
| DEP-2 | The actual mechanism for counting the input field limit: code points, UTF-16 units, or bytes. The indication model is set by D-26 (red highlight + disabled button at >32,000 code points) — the highlight and button state serve as observable markers of the actual basis during the run | TC-B-001…003 | "observation"; oracles conditional until confirmed (Limit Basis Rule: D-5 — code points; HTML maxlength — UTF-16) | Implementation; confirmation during the run (astral set TC-B-003) |
| DEP-3 | Availability of the "AI Mode" button for test accounts (absence of rollout/flag restrictions) on each stand | Precondition of all scenarios (BR-1) | Entry check; if unavailable — "Blocked" | Owner/implementation support; check at entry |
| DEP-4 | The exact text of the error message when the AI service is unavailable | TC-N-005 | Pass rule — the D-18 reaction class (deterministic); the text is recorded as the reference; observation status for the string | Implementation; reference recording during the run |

### 10.3 Residual Risks Registry (owner-accepted exclusions)
| RR-N | Excluded item | Reason / Decision ID | Impact if it fails in production | Goes to release checklist |
|---|---|---|---|---|
| RR-1 | Resource consumption (page weight, memory) is not tested | D-20 | Unnoticed weight/memory problems on weak devices; degradation on mobile | yes |
| RR-2 | Data preparation/cleanup on production excluded ("as is", D-21) | D-21 | The smoke-scenario oracle TC-P-001 ("history empty") depends on the actual state of test-ivan on production; false Failed/skips are possible | yes |
| RR-3 | Full coverage is performed on dev.google.com only; on production — smoke only (3 scenarios, D-13) | D-12 | Behavior that depends on the production environment (data, scale, configuration) is verified in a limited way | yes |

---

## 11. Self-Check and Consistency Verification (mandatory, run last)

- [x] Scenarios recounted: 19 total; P0 — 2 (TC-P-001, TC-S-001); P1 — 11 (TC-P-002…006, TC-N-001…003, TC-B-001, TC-S-002, TC-C-001); P2 — 6 (TC-N-004, TC-N-005, TC-B-002, TC-B-003, TC-S-003, TC-PF-001). Matches the header and 6.2.
- [x] The header's numeric statements verified: 27 decisions (D-1…D-27); 0 open questions; 4 dependencies (DEP-1…DEP-4); 3 residual risks (RR-1…RR-3); 0 active conflicts.
- [x] Every BR (BR-1…BR-10) is mapped to ≥1 TC (traceability in 6.2 and 1.4); there is no BR without a scenario or an exclusion.
- [x] Every precondition element is covered by a TC (search field, "AI Mode", "Google Search" → TC-P-001, TC-C-001).
- [x] Every scenario has: a deterministic oracle (exact text / invariants D-22/D-24 / the D-26/D-27 model / an enumeration of outcomes; conditional ones — with a DEP-2 reference and a temporary status), an applicability/N-A rule, an artifact.
- [x] Every conditional item has traceability: limit basis → DEP-2; closed Q-1…Q-27 → D-1…D-27; no TBDs remain in the body.
- [x] Every external dependency lists the affected scenarios and the temporary status rule (10.2).
- [x] Exit criteria are measurable and separated by stage (dev — full coverage; production — smoke, 18 runs, D-13/D-23); taxonomy D-10; no unclosed details.
- [x] No careless oracle wordings: "layout is not broken" is decomposed into an enumeration; "works correctly" is absent.
- [x] Every declared method has an artifact: ECP classes (including D-26/D-27 markers), BVA values with a basis, decision table 4.1-A (including the guest row D-24), transitions 4.1-B (including input field states D-26/D-27), compatibility matrix; OATS is honestly indicated as not applied.
- [x] Residual risks are not masked: RR-1…RR-3 with impact and release checklist; search query field limits are explicitly out of scope (2.1, 6.2).
- [x] Conflicting/stale items: the conflict D-8 ↔ D-11 resolved via D-12; the guest invariant from D-22 replaced by D-24; the overflow oracle "truncation/ignoring" replaced by the D-26 model — all three recorded in Conflicting/Stale and kept in the registry as sources.

**Corrections made during self-check:**
1. BR-9 (new-chat rule, D-15) and BR-10 (session security, D-19) added to 1.4 — every owner decision is reflected by a business rule with traceability.
2. Table 4.1-A filled in with the invariants D-22/D-24 (cells of alternative states, including the guest).
3. The RR registry filled in: RR-1 (resources), RR-2 (production "as is"), RR-3 (full coverage on dev only); the "Decision Registry ↔ exclusions mapping" row added to 6.2.
4. D-8 marked as refined (Stale/Outdated) by decision D-12 — the current wording is used, D-8 kept as the source of the conflict.
5. Round 3: Q-23 closed by the owner (D-23) — all "→ Q-23" placeholder references replaced with final values (smoke = 3 scenarios × 6 cells = 18 runs); section 7 rewritten (0 open questions); numeric statements recounted.
6. The zero number of open questions is stated as conditional readiness (the external dependencies registry is non-empty — DEP-1…DEP-4), per the zero-questions rule.
7. Round 4 (owner's feedback): the guest invariant from D-22 replaced by decision D-24 — TC-P-005, 4.1-A, 4.1-B, 1.2, BR-2/BR-3, DEP-1, 2.1, 2.3, 9.1 updated; D-22 marked as partially stale.
8. Round 4: the test-history data set defined (D-25: 2–3 completed chats from past sessions with 3–5 messages each) — 2.4, TC-P-006, 8.1, 4.2 updated.
9. Round 5 (owner's feedback on limits): the TC-B-001 overflow oracle "truncation/ignoring" replaced by the D-26 model (the field accepts over-limit text; red highlight + disabled button at >32,000 code points; recovery at ≤32,000 code points); TC-B-002 — button disabled with an empty field (D-27); TC-B-003 — highlight/button as observable basis markers (DEP-2); BR-7, ECP classes (4.1), transitions (4.1-B), 2.2 (UI/UX), 9.3, 5.2 (highlight-shade risk added) updated; header Version 5, numeric statements recounted (27 decisions); the former oracle recorded in Conflicting/Stale.
10. Round 5: search query field limits explicitly recorded as out of scope (2.1, 6.2) — the field is only checked for visibility in the precondition.
