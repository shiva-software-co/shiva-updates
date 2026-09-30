# Feature Specification: Publishing Shiva Content to GitHub

**Feature Branch**: `002-github-content-publishing`
**Created**: 2026-09-30
**Status**: Draft
**Input**: User description: "I need to publish to GitHub: the results of Shiva's work, so my followers can see the value of Shiva for themselves; a roadmap, so that I and my followers can see the full feature set and approximate readiness timeframes. I was thinking of creating a folder under content for this task, where we would put: the specs used when developing features; the roadmap — I think it belongs in README.md; besides that, README.md should contain the project description: the name, the pain we solve, the list of key features, the roadmap. I want a skill that takes all the files in that folder, translates them into English if needed, and publishes the files to the repository."

## Result Image

The author has finished a feature and wants to show it to their followers.

1. As part of the task, we write the project description — a `README.md` with four sections: name (Shiva ADE), the pain we solve, the list of key features, the roadmap (the full feature set + approximate readiness timeframes). The file goes into the source folder (working name `content/github/`).
2. The author also puts the feature spec there.
3. The author runs the publishing skill with a single command.
4. The skill scans the folder, determines that both files are in Russian, translates them into English while preserving the markdown structure, and shows the author a summary (what was translated and what was not).
5. The skill publishes the files to the target public repository: `README.md` to the repository root, the spec to the specs directory. One commit per run.
6. The author opens the link and sees: the main page with the README in English (name, pain, features, a roadmap table with features and timeframes) and the specs directory with translated specs.
7. The author shares the link with their followers; a follower without access to the private code reads the README and the specs and sees the value of Shiva for themselves.

Acceptance criterion (verified by hand): the link opens a README with the 4 sections (name / pain / features / roadmap), entirely in English; the specs are readable, the markdown structure is intact; the public repository contains no Russian-language text and no secrets.

## User Scenarios & Testing *(mandatory)*

### User Story 1 — Content source folder (Priority: P1)

As part of the task, the project description is written — a `README.md` (name, the pain we solve, list of key features, roadmap) — and a source folder is created inside `content/`, where the author puts the materials for the public repository: this `README.md` and the specs used during feature development.

**Why this priority**: without a structured source there is neither a publication nor a roadmap — this is the core of the MVP.

**Independent Test**: verified by hand — the folder structure follows the convention, the README contains all the mandatory sections; already delivers value (a ready showcase draft).

**Acceptance Scenarios**:

1. **Given** the source folder is created, **When** the author adds materials, **Then** the structure follows the convention: `README.md` + a subdirectory for specs (+ optionally media).
2. **Given** the `README.md` in the source folder, **When** the file is opened, **Then** it contains the 4 mandatory sections: project name; the pain we solve; list of key features; roadmap (the full feature set + approximate readiness timeframes).
3. **Given** the editorial structure exists in `content/` (`publications/`, `plan/`, `_templates/`), **When** the source folder is created, **Then** it does not break the existing structure or the conventions of `content/README.md`; the source folder is a separate GitHub-showcase track (not a demand stage, no frontmatter matrix), connected to the editorial matrix at two points: (a) the roadmap from the source folder's `README.md` feeds the editorial matrix's list of topics, (b) the materials in the source folder (agent results, specs) are the raw material for posts.

---

### User Story 2 — Publishing skill: translation + publication (Priority: P1)

The author runs the skill; the skill takes all the files from the source folder, determines for each whether translation is required, translates the ones that require it into English, and publishes all the files to the target repository.

**Why this priority**: the core automation of the feature — it removes manual translation and manual push; without it US1 has no visible result.

**Independent Test**: run the skill on a folder with a Russian README and a Russian spec → English versions appear in the target repository; files already in English are published unchanged.

**Acceptance Scenarios**:

1. **Given** the source folder contains a Russian-language `README.md`, **When** the skill is run, **Then** the README is translated into English (markdown structure preserved) and published to the root of the target repository.
2. **Given** the source folder contains a Russian-language spec, **When** the skill is run, **Then** the spec is translated and published to the target repository's specs directory.
3. **Given** a file is already in English, **When** the skill is run, **Then** the file is published as is, without translation.
4. **Given** a file is non-textual (image, GIF), **When** the skill is run, **Then** the file is copied to the repository without translation.
5. **Given** a repeat run with no changes in the folder, **When** the skill completes, **Then** the target repository has no duplicates and no empty commits.

---

### User Story 3 — A follower sees the Shiva showcase (Priority: P2)

A follower opens the public repository via the link and sees the value of Shiva for themselves: reads the README (name, pain, features), sees the roadmap with approximate timeframes, and, if they want to go deeper, opens the specs.

**Why this priority**: this is the target outcome of the feature; it depends on US1 and US2, but is verified independently — by the fact of publication.

**Independent Test**: open the public repository without access to the private code → the README and specs are available and entirely in English.

**Acceptance Scenarios**:

1. **Given** the publication is done, **When** a follower opens the repository, **Then** they see a README with the 4 sections (name, pain, features, roadmap) in English.
2. **Given** the follower reads the roadmap, **When** they look through the list, **Then** they see the full feature set and the approximate readiness timeframe of each.
3. **Given** the follower wants to go deeper, **When** they open the specs directory, **Then** the specs are readable in English and show how the development is run (the results of Shiva's work).

---

### Edge Cases

- **LLM unavailable during translation** (timeout, network error, 5xx): the skill does NOT silently publish the file in an untranslated form — it interrupts the processing of the affected file with a clear error; already-processed files are not lost.
- **Partial translation failure** (some files translated, some failed): the publication is atomic — "all or nothing". No file is pushed; the translated results are saved locally for a repeat run; the summary reports which files caused the failure.
- **LLM returned an invalid result** (truncated response, broken markdown, empty translation): structure and completeness validation; retry; on repeated failure — fail with a message, the file is not published.
- **Empty source folder**: the skill reports "nothing to publish" and exits without a commit.
- **Empty or degraded file** (0 bytes, a binary where markdown was expected): the file is skipped with a warning.
- **Duplicates**: a file unchanged since the last publication → skipped (no-op), no empty commit is created.
- **Target repository out of sync with the source folder**: the skill is the sole owner of the showcase; mirror synchronization — files absent from the source folder are deleted from the target repository (including manually added ones). Manual edits in the target repo are lost — edits go only through the source folder.
- **Sensitive data leak**: explicit secrets detected in a file → the skill blocks the publication of the file and reports to the author; the public repository receives no secrets. Explicit secrets (blocker): GitHub tokens (`ghp_`, `github_pat_`), OpenAI/Anthropic keys (`sk-`), AWS (`AKIA`), PEM blocks, passwords in env assignments. Suspicious patterns (warning, publication continues after an explicit mention in the summary): long base64 strings, `api_key`-like identifiers without a secret value. Only the files of the current publication are checked, not the whole target repository.
- **Target repository unavailable** (no push rights, repository does not exist, network): a clear error; translation results are saved for a repeat run.
- **Mixed language in a file** (Russian text + English identifiers): only the natural language is translated; code identifiers, paths, and command names are not translated.
- **File edited by the author while the skill is running**: the version as of the scan is processed (the scan takes a snapshot: the file list and their contents are fixed before translation begins); the detected change is noted in the summary.
- **File does not fit into the LLM context entirely**: translation is chunked by markdown sections; the chunks are translated sequentially and glued together; the structure (no lost headings) and completeness (the last chunk is not truncated) are validated.
- **Translation quality control**: the skill does not stop for a preview — the author checks the translation via the links from the summary (blob links to the published files on GitHub); if needed, they edit the source folder and re-run the skill.
- **Many files (20+)**: sequential processing, one file at a time, no parallelism or batching; progress is reflected in the summary per file; there is no limit on the number of files.

#### Brainstorm Prompts

- ~~**Boundary conditions**: what is the maximum file size for translation? what to do with very long specs (LLM context limits)?~~ — resolved: chunking by markdown sections (see Edge Cases).
- ~~**Error scenarios**: translation partially succeeded — push everything or nothing? is a dry-run mode needed?~~ — resolved: "all or nothing" atomicity; dry-run — YAGNI, not implemented.
- ~~**Scale**: what if the folder has 20+ files? batch translation, progress indication?~~ — resolved: sequential processing without batches, progress in the summary.
- ~~**Security**: which secret patterns are a blocker and which are a warning? audit the whole target repo or only the new files?~~ — resolved: explicit secrets — blocker, suspicious — warning; audit only of the current files.
- ~~**User confusion**: how does the author know what exactly changed in the repository after the run?~~ — resolved: a summary with blob links; fixes — via re-run, not preview.
- ~~**Data integrity**: the target repo has files that are not in the source folder — delete them or keep them?~~ — resolved: mirror synchronization, the skill is the sole owner of the showcase.
- ~~**Backwards compatibility**: does the new folder break the editorial workflow of `content/`?~~ — resolved: a separate GitHub-showcase track; the roadmap feeds the matrix topics, the materials are raw material for posts.

## Open Questions

| # | Question | Status | Resolution |
|---|----------|--------|------------|
| Q1 | Which repository do we publish to? | Resolved | A new public repository in the `shiva-software-co` organization, working name `shiva-updates`. Genre-wise, "updates" is the established expression for news and work results (note: a changelog is releases only, an engineering blog is articles only, a roadmap is the plan only; the showcase is broader than each genre). The final name is fixed when the repository is created; the name is parameterized in the skill's configuration. |
| Q2 | What type of skill? | Resolved | A project-level opencode skill, versioned in the monorepo (option A). |

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST provide a source folder `content/github/` inside `content/` with a fixed structure convention: `README.md` (the project showcase), the specs subdirectory `specs/`, optionally `media/`, the configuration file `publish.config.json` (see FR-009), and the service translation staging `.en/` (see FR-008, Key Entities).
- **FR-002**: The source folder's `README.md` MUST contain the 4 mandatory sections: project name; the pain we solve; list of key features; roadmap (the full feature set + approximate readiness timeframes).
- **FR-003**: The skill MUST scan the source folder recursively and build the full list of files to publish (markdown, media, nested directories).
- **FR-004**: The skill MUST determine for each text file whether translation into English is required, and fully translate the files that require it (headings, tables, lists), preserving the markdown structure, formatting, and links; code identifiers, paths, and command names are not translated. The "translation required" criterion: the file contains Cyrillic (`[А-Яа-яЁё]`); a file without Cyrillic is published as is.
- **FR-005**: The skill MUST publish the processed files to the target repository with the fixed mapping: `README.md` → repository root; specs → the specs directory; media → as is.
- **FR-006**: The skill MUST be idempotent: a repeat run updates the same files; unchanged files produce no commit. Synchronization is mirror-style: the skill is the sole owner of the showcase; files absent from the source folder are deleted from the target repository.
- **FR-007**: The skill MUST check the files for secrets before publication and block the publication of a file with an explicit secret, reporting to the author; suspicious patterns are a warning in the summary. Only the files of the current publication are checked. The exact list of blocker and warning patterns is fixed once — in the Edge Cases "Sensitive data leak".
- **FR-008**: On a translation failure (LLM unavailable / invalid or empty result after retry — the number of attempts is read from `ade.config.json → limits.retryLimit`, no hardcoded values) the skill MUST interrupt the publication with a clear error. The publication is atomic: no file is pushed until all the files of the run are processed successfully; the translated results are saved locally in the `.en/` staging (a rebuildable cache: not scanned as a source, not published, excluded from the monorepo's git) for a repeat run; untranslated content is never published silently.
- **FR-009**: The skill MUST publish the files to a new public showcase repository in the `shiva-software-co` organization; the target repository's address is set by the configuration file `content/github/publish.config.json` (fields: `target_repo` — `org/name` or URL, `branch`, optionally `commit_prefix` — default `Publish updates:`), not hardcoded in the skill's logic — renaming the repository does not require editing the skill. The repository's working name before creation is `shiva-updates` (the final name is fixed in the config when the repository is created).
- **FR-010**: The skill MUST be implemented as a project-level opencode skill, versioned in the monorepo (not a `shv-…` shiva command and not a user skill outside the repository).
- **FR-011**: The skill MUST show the author a summary on completion: how many files were processed, which were translated, which were published, which were skipped/blocked and why; for every published file — a GitHub blob link.
- **FR-012**: The skill MUST process files sequentially, one at a time; when the LLM context size is exceeded — translate in chunks by markdown sections with reassembly validation.

### Key Entities

- **Source folder** (`content/github/`): the publication staging zone inside `content/`; contains `README.md` (the project description + roadmap) and development materials (specs, media). The source of truth for the showcase.
- **Publication configuration** (`content/github/publish.config.json`): the target repository parameters — `target_repo` (required, `org/name` or URL), `branch` (required), `commit_prefix` (optional, default `Publish updates:`). The only place where the showcase address is set (FR-009); an invalid config → a clear error, the publication does not start.
- **Translation staging** (`content/github/.en/`): a local cache of translated English versions, mirroring the source structure; survives a failure for a repeat run (FR-008); rebuildable (not the source of truth), not scanned as a source, not published, excluded from the monorepo's git.
- **Target repository**: a public GitHub showcase repository in the `shiva-software-co` organization (working name before creation — `shiva-updates`), accessible to followers without access to the private code.
- **Publishing skill**: the "scan → language detection → translation → publication" automation; launched by the author with a single command.
- **Roadmap**: the README section with the full feature set and approximate readiness timeframes; maintained by the author manually.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A follower without access to the private code opens the public repository and sees a README in English with all 4 sections (name, pain, features, roadmap with approximate timeframes).
- **SC-002**: The full cycle "put the files in the folder → run the skill → the files are in the repository" takes the author no more than 10 minutes and requires no manual translation or manual push.
- **SC-003**: All published text files are entirely in English: the target repository has no leftover Russian-language natural text.
- **SC-004**: A repeat run of the skill with no changes in the source folder creates no new commit; a run with changes — no more than one commit per run.
- **SC-005**: The target repository contains no secrets (key/token pattern checks on all published files).

## Assumptions

- The author selects and places the specs into the source folder themselves; the skill does not scan the monorepo's `specs/` automatically.
- The roadmap is maintained manually by the author in the source folder's `README.md` (not auto-generated from the master spec).
- The source folder materials are regular Russian-language markdown files (not the bilingual `## RU` / `## EN` format from `content/publications/`).
- Publication uses the author's already-configured git credentials (push/gh CLI); the skill does not manage GitHub authorization.
- The target repository is created once before the first publication; repeat runs of the skill publish to the existing repository.
- Translation is performed by a real LLM provider from the environment's working configuration (real LLMs, no mocks — per the constitution).
- The source folder does not violate the editorial structure of `content/` (`publications/`, `plan/`, `_templates/`): it is a separate GitHub-showcase track connected to the editorial matrix at two points — the source folder's roadmap feeds the matrix's list of topics, and the materials (agent results, specs) serve as raw material for posts.
- Edits go only into the source folder; direct edits in the target repository are overwritten by mirror synchronization.
- No dry-run mode is implemented (YAGNI): quality control is via the summary with blob links and a re-run.

## Brainstorm Log

### 2026-09-30 — Session 2 (edge cases & design refinement)

The result image was clarified: writing the project description (`README.md`: name, pain, features, roadmap) is part of the task, not a prerequisite.

All open Brainstorm Prompts were worked through:

1. **Partial translation failure** → atomic "all or nothing" publication: no file is pushed until all are processed successfully; the translated content is saved locally for a repeat run (FR-008).
2. **Data integrity** → mirror synchronization: the skill is the sole owner of the showcase; files outside the source folder (including manually added ones) are deleted; edits only through the source folder (FR-006).
3. **Backwards compatibility with `content/`** → `content/github/` is a separate GitHub-showcase track (not a demand stage, no frontmatter matrix); the connection to the editorial matrix is two-way: the roadmap feeds the matrix topics, the materials are raw material for posts.
4. **Long files** → translation chunking by markdown sections with reassembly validation (FR-012).
5. **Secrets** → a two-level policy: explicit secrets (ghp_, github_pat_, sk-, AKIA, PEM, env passwords) — blocker; suspicious (long base64, api_key identifiers) — warning; audit only of the current publication's files (FR-007).
6. **Translation quality control** → no preview and no dry-run (YAGNI): a summary with GitHub blob links, fixes via re-run (FR-011).
7. **Scale of 20+ files** → sequential processing without batches or parallelism; no limit on the count (FR-012).

All Brainstorm Prompts are closed. Open Questions (Q1, Q2) were resolved earlier. The spec is ready for the plan.