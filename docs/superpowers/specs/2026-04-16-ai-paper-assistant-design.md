# AI Paper Assistant Workspace Design

## Overview

This workspace will become a local AI paper assistant hub. It will manage paper-related SDKs that live under the workspace, starting with `PubCrawler` as the first SDK. The assistant itself will be defined by a workspace-level `AGENT.md`, while each SDK will be documented through its own local skill.

The assistant must do four things well:

1. Understand the real capability boundary of each SDK from code and runnable workflows, not only from README files.
2. Route paper-related requests to the right SDK skill.
3. Collect the information needed to operate the chosen SDK through either a fill-in template or guided Q&A.
4. When an SDK is insufficient, stop at capability-gap analysis and an extension proposal unless the user explicitly approves code changes.

## Goals

- Establish a reusable assistant identity for paper retrieval, filtering, organization, trend analysis, local paper search, and paper-database construction.
- Treat `PubCrawler` as the first managed SDK and document its real operating surface.
- Keep the initial structure minimal and local to this workspace.
- Make the assistant extensible so future paper-related SDKs can be added without redesigning the workspace.

## Non-Goals

- Do not turn this workspace into a general-purpose coding agent framework.
- Do not auto-modify SDK code when a capability gap is found.
- Do not force all SDK knowledge into one monolithic skill.
- Do not repackage `PubCrawler` into a formal pip-installable SDK as part of this design.

## Confirmed Constraints

- Assistant assets must live in the current workspace, not in global skill directories.
- The assistant router must be defined in a workspace-level `AGENT.md`.
- Skills must remain SDK-specific. They are not the place for cross-SDK routing policy.
- When an SDK is insufficient, the default behavior is: analyze the gap, propose an extension path, and wait for explicit approval before changing code.
- The initial structure must follow the minimal local option.

## Current Project Context

### Workspace Shape

The workspace root is:

`/home/yaoxingting/yao_labs/PubCrawler_paper_reading`

This root is not a git repository. The actual first SDK repository is:

`/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler`

This matters because assistant management assets should live at the workspace root, while SDK code should stay inside the SDK repository.

### PubCrawler Reality Model

`PubCrawler` is not only a collection of scrapers. Its actual operating surface is a workflow engine with these linked stages:

1. `configs/tasks.yaml` defines source metadata and executable paper-collection tasks.
2. `src/crawlers/run_crawler.py` normalizes tasks, dispatches source-specific scrapers, filters papers, and writes outputs.
3. `src/analysis/` generates word clouds, single-task topic analysis, and cross-year trend outputs.
4. `src/search/indexer.py` builds a local SQLite FTS5 index from collected CSV files.
5. `src/search/embedder_chroma.py` builds or updates a Chroma vector store for semantic retrieval.
6. `src/search/search_service.py` exposes keyword search, semantic search, result saving, and AI response generation.
7. `streamlit_app.py` provides a local UI for search, filtering, trend exploration, and AI-assisted interaction.

The assistant must therefore describe `PubCrawler` as a paper workflow SDK rather than as a single-purpose crawler.

## Minimal Local Structure

The initial workspace structure will be:

```text
/home/yaoxingting/yao_labs/PubCrawler_paper_reading/
  AGENT.md
  skills/
    pubcrawler/
      SKILL.md
  PubCrawler/
```

### Why This Structure

- `AGENT.md` is the assistant control plane.
- `skills/pubcrawler/SKILL.md` is the PubCrawler SDK operating manual.
- `PubCrawler/` remains the SDK codebase itself.

This is intentionally minimal. It avoids new registry files or extra documentation directories while still leaving room for future SDKs by adding more skill folders and extending the `AGENT.md` registry section.

## AGENT.md Design

The workspace-level `AGENT.md` will be the assistant router and policy file. It should not duplicate detailed PubCrawler usage instructions. Its job is to manage identity, routing, and control flow across SDKs.

### Required Sections

#### 1. Identity

Define the assistant as the user's AI paper assistant for paper retrieval, filtering, structuring, trend analysis, local search, paper database construction, and SDK extension planning.

#### 2. Operating Principles

State the global rules:

- Ground recommendations in code and runnable workflows when possible.
- Prefer using existing SDK capabilities over inventing ad hoc workflows.
- When a capability gap exists, produce analysis and a proposal before editing SDK code.
- Only modify SDK code after explicit user approval.

#### 3. SDK Registry

Maintain a structured list of known SDKs directly inside `AGENT.md`. The initial entry for `PubCrawler` should include:

- Name
- Path
- Status
- Role
- Covered task types
- Associated local skill
- Notes on special boundaries

This makes `AGENT.md` extensible without adding more workspace infrastructure.

#### 4. Routing Rules

Describe how the assistant chooses an SDK:

- If a request is covered by `PubCrawler`'s workflow surface, route to the PubCrawler skill.
- If future SDKs exist, compare fit by task type, capability boundary, and output requirements.
- If more than one SDK could apply, choose the narrowest capable SDK first.
- If no SDK is sufficient, enter capability-gap analysis mode.

#### 5. SDK Onboarding Protocol

When a new SDK is added to the workspace, the assistant should:

1. Study code, docs, execution flow, boundaries, and extension seams.
2. Write an SDK-specific skill for it.
3. Register it in `AGENT.md`.
4. Clarify how it differs from existing SDKs.

#### 6. Change Control

Clarify that:

- `AGENT.md` owns routing, policy, and SDK inventory.
- SDK-specific operating detail belongs in each SDK skill.
- If code reality and skill text diverge, the assistant must trust code reality and update the skill later.

## PubCrawler Skill Design

`skills/pubcrawler/SKILL.md` will be a code-grounded operating guide for PubCrawler as an SDK-like paper workflow engine.

### Required Sections

#### 1. When to Use

Trigger conditions should include:

- Top-conference or arXiv paper collection
- YAML-driven crawl task setup
- Keyword filtering
- Single-task analysis
- Cross-year trend analysis
- Local FTS5 indexing
- Semantic search with Chroma
- Streamlit-based local search and AI interaction

#### 2. What PubCrawler Is

Define it as:

A YAML-driven paper workflow SDK for collection, filtering, analysis, indexing, semantic retrieval, and local interactive exploration.

#### 3. Supported Capability Surface

Document the real stable surfaces the assistant can rely on:

- `configs/tasks.yaml` as the main task configuration surface
- `src/crawlers/run_crawler.py` as the orchestration entrypoint
- `src/scrapers/` as the source adapter layer
- `src/analysis/` as the analysis layer
- `src/search/indexer.py` for FTS5 indexing
- `src/search/embedder_chroma.py` for Chroma embedding generation
- `src/search/search_service.py` for search and AI backend services
- `streamlit_app.py` for local UI access

#### 4. Operational Workflow

Define the standard flow:

1. Prepare or update task configuration.
2. Run paper collection and built-in analysis.
3. Build or rebuild the local FTS5 index when needed.
4. Build or update the Chroma semantic index when needed.
5. Use search services or launch Streamlit depending on the user's goal.

Also describe partial workflows:

- crawl only
- index rebuild only
- semantic index update only
- search only
- UI only

#### 5. Boundaries and Non-Goals

Document what PubCrawler should not be treated as:

- not a general knowledge graph platform
- not a multi-SDK orchestrator
- not a formal public Python package with stable external APIs
- not an auto-extending crawler framework

Requests beyond these boundaries should trigger capability-gap analysis.

#### 6. Maintenance and Extension

Summarize extension seams:

- new source support mainly touches `src/scrapers/`, source registration, and task normalization
- new analysis features mainly touch `src/analysis/`
- new search features mainly touch `src/search/`
- assistant-led extension begins with analysis, not immediate code modification

#### 7. Common Pitfalls

Include code-grounded pitfalls such as:

- the workspace root is not the SDK repo
- PubCrawler depends heavily on script entrypoints and relative project paths
- task, output, database, and UI flows assume project-root-relative directories
- some features depend on optional local setup such as NLTK stopwords or API keys

#### 8. Assistant Behavior

When this skill is active, the assistant should:

- inspect user goals against PubCrawler's actual workflow surface
- identify the minimal usable path through the SDK
- gather missing parameters before proposing execution
- stop at analysis when the user's request exceeds current PubCrawler capability

#### 9. User Intake Templates

The skill must provide a user-facing intake layer so the assistant can collect the parameters needed to use PubCrawler correctly.

It should support both:

- a fill-in template
- an interactive question flow

##### Fill-In Template

The template should collect at least:

- task goal
- source scope
- conference or arXiv target
- year or date range
- keyword or regex filters
- whether PDFs are needed
- whether reviews are needed
- whether trend analysis is needed
- whether FTS5 index build is needed
- whether Chroma semantic index is needed
- whether semantic search is needed
- desired output format

##### Interactive Intake Flow

If the user gives incomplete information, the assistant should ask one question at a time in this order:

1. What kind of paper task is this?
2. What source set should be searched or crawled?
3. What year or date range matters?
4. What filtering logic should be applied?
5. Is this collection-only, analysis-only, indexing, or search?
6. What outputs are expected?

If the request cannot fit PubCrawler's supported surface, the intake flow should surface that early and switch to capability-gap analysis.

## End-to-End Request Workflow

Once the design is implemented, the assistant should handle future paper requests like this:

1. `AGENT.md` performs SDK routing.
2. The chosen SDK skill starts intake.
3. The skill checks whether the request fits the SDK boundary.
4. The assistant produces an execution plan if the SDK is sufficient.
5. The assistant either executes the supported workflow or stops at extension analysis.
6. The assistant returns a result summary with locations, outputs, limitations, and next steps.

## Error Handling Design

The assistant should standardize three types of failure handling:

### 1. Missing Input Data

If the user has not provided enough information to safely drive PubCrawler, do not guess hidden task parameters. Use the intake flow to fill the gaps.

### 2. Capability Mismatch

If the user asks for something beyond PubCrawler's current surface, do not attempt improvised code changes. Produce:

- a short capability-gap diagnosis
- likely extension points
- the scope of expected code changes
- the recommended next step

### 3. Environment or Workflow Failures

If PubCrawler setup or runtime prerequisites are missing, the assistant should identify which stage failed:

- configuration
- crawl
- analysis
- FTS5 index build
- Chroma build
- Streamlit/UI
- AI backend setup

The assistant should report the failure at the workflow stage level, not only as a raw traceback.

## Verification Strategy

The first implementation pass should be verified through document checks rather than SDK mutation:

1. Confirm `AGENT.md` contains the required sections and PubCrawler registry entry.
2. Confirm `skills/pubcrawler/SKILL.md` covers the real code-grounded workflow surface.
3. Confirm the skill includes both fill-in and guided-intake modes.
4. Confirm the extension-control rule is explicit: analyze first, modify only after approval.
5. Confirm the minimal local structure is preserved.

## Open Decisions Resolved by This Spec

- The assistant assets live in the workspace.
- `AGENT.md` is the routing and management layer.
- Skills are SDK-specific.
- The initial structure is minimal.
- SDK extension starts with analysis and requires explicit approval before code changes.
- PubCrawler skill must include user intake templates.

## Delivery Plan After Spec Approval

After this spec is approved, the next planning step should produce implementation tasks for:

1. creating the workspace `AGENT.md`
2. creating `skills/pubcrawler/SKILL.md`
3. validating that both files reflect PubCrawler's actual workflow surface

