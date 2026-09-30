# Shiva ADE

## Name

**Shiva ADE** is an agent-driven development environment for testing and QA automation. It is a terminal chat agent that takes a technical specification in any form, clarifies it in a dialogue with the owner, brings the requirements and the test strategy to readiness — and, further down the pipeline, generates test cases and automation code, runs tests across different environments (web, mobile, API, databases, message queues), and builds reports with requirement coverage.

Shiva ADE is written in Rust and runs in the terminal (TUI). The core principle: **requirements are tested before any code is written**.

## The pain we solve

- **Test cases are written by hand.** QA engineers manually parse the spec and compose cases — slow, expensive, and the result depends on the performer.
- **Nobody tests the requirements.** "Green" code tests don't help when the spec itself is wrong: the code honestly passes the checks while doing something the business never asked for.
- **The "case written — code never written" gap.** TMS systems and documents accumulate piles of test cases that will never be automated.
- **Flaky tests.** Unstable runs undermine trust in automation and eat up time investigating false failures.
- **No traceability.** The chain "requirement → case → code → result" is recorded nowhere — it is impossible to prove that a requirement is actually covered.

**How Shiva ADE solves it.** The agent runs an input audit of the spec before any code is written: it finds gaps, contradictions, and stale requirements, asks clarifying questions, and does not finish the work until every open point is closed by the owner's answer or a deliberate decision. The test strategy is then built along the chain, followed by the plan, cases, automation, and runs — all with a single traceability chain "requirement → case → code → result".

## Key features

- Terminal chat agent (TUI in Rust/ratatui): real-time response streaming, session history, every session isolated in its own git branch.
- Own agent core `ade-llm`: agent-loop with tool calls, streaming, retries, auto-summarization when the context window fills up — no external agent frameworks.
- Multi-provider LLM router: OpenAI, Anthropic, and local Ollama; fallback on failures, fine-grained sampling settings per model.
- The workflow is driven by skill commands `/shv-<name>`: commands live in the project repository and are added without touching the core.
- "Requirements testing" command: input audit of the spec (known / missing / conflicting / stale / out-of-scope), rounds of clarifying questions, a report artifact in the project repository.
- "Test strategy" command: audit by seven lists (including main risks), rounds of TBD questions across the ten strategy zones, an artifact report in the repository.
- The `askUser` tool: the agent asks questions right in the TUI — with rationale and answer options.
- A full set of infrastructure agent tools: file operations, bash, HTTP requests, parallel subagents; progressive disclosure of categories to save context.
- Scatter-gather subagents: parallel subtasks with fresh context, transcript into the session history, cancellation from the UI.
- Permission matrix: allow / ask / deny per operation, injection protection through composite commands, unconditional bans on dangerous actions.
- Local SQLite persistence: projects, sessions, messages, tool and command registries.

Key capabilities in development — automation code generation (Screenplay architecture), test runs (Vitest + Allure), self-healing locators, visual regression, a11y checks, and Vision AI. The full set with timeframes is in the roadmap below.

## Roadmap

> Timeframes are estimates: one task ≈ two weeks (sequential development); the plan belongs to the project owner and is revisited as the product evolves.

| Feature | Status / ETA |
|---------|--------------|
| **Core and interface** | |
| Agent core `ade-llm` (agent-loop, tool calls, streaming, retries) | Done |
| Multi-provider LLM router (OpenAI / Anthropic / Ollama, fallback) | Done |
| TUI interface (ratatui, Elm Architecture, response streaming) | Done |
| Local SQLite persistence and git session isolation | Done |
| Infrastructure agent tools (files, bash, HTTP, subagents) | Done |
| Scatter-gather subagents (parallel subtasks) | Done |
| Permission matrix (allow / ask / deny, injection protection) | Done |
| **Workflow: skill commands** | |
| `/shv-<name>` command infrastructure (extension without core changes) | Done |
| `askUser` tool (question rounds with the owner) | Done |
| "Requirements testing" command | Done |
| "Test strategy" command | Done |
| "Test plan" command | Q4 2026 |
| "Test cases" command | Q4 2026 |
| "Test automation" command (code generation) | Q1 2027 |
| **Test framework** | |
| Screenplay architecture (Actor / Task / Question / Ability) | Q1 2027 |
| Data Builders (fluent builders for test data) | Q1 2027 |
| Component Objects and API clients | Q1 2027 |
| Environment configuration (YAML profiles, secrets via vault) | Q2 2027 |
| Multi-target drivers: web (Playwright), mobile (Appium), API, DB, message queues | Q2 2027 |
| Test runner: Vitest + Allure | Q2 2027 |
| Reports with requirement coverage | Q3 2027 |
| "Test run" command (execution and reporting) | Q3 2027 |
| Real-time streaming of run results into the TUI | Q3 2027 |
| **Test reliability** | |
| Self-healing: automatic recovery of broken locators | Q3 2027 |
| Visual regression: UI screenshot comparison | Q3 2027 |
| A11y checks (WCAG) | Q4 2027 |
| Vision AI: finding elements from screenshots | Q4 2027 |
| Flaky detection and quarantine of unstable tests | Q4 2027 |
| **Integrations and distribution** | |
| Case export to TMS (TestRail, Zefir, Xray, CSV) | Q4 2027 |
| MCP server integration (external agent tools) | Q4 2027 |
| Cross-browser grid (BrowserStack, Sauce Labs) | Q1 2028 |
| Cloud test runs (hosted CI integration) | Q1 2028 |
| Video → test (converting a screen recording into a test) | Q1 2028 |
| Distribution: binary, trial, license key, auto-updates | Q1 2028 |