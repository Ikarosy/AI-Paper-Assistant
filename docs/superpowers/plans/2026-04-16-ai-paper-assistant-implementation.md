# AI Paper Assistant Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a workspace-level `AGENT.md` and a local `skills/pubcrawler/SKILL.md` that establish this repository as an extensible AI paper assistant hub with PubCrawler as the first managed SDK.

**Architecture:** Keep the implementation minimal and local to the workspace root. `AGENT.md` will hold assistant identity, routing rules, SDK inventory, and change-control policy. The PubCrawler skill will hold SDK-specific workflow knowledge, capability boundaries, intake templates, and extension guidance based on the actual code structure in `PubCrawler/`.

**Tech Stack:** Markdown, YAML frontmatter, local workspace documentation grounded in the PubCrawler Python codebase

---

### Task 1: Create the Workspace AGENT.md

**Files:**
- Create: `AGENT.md`
- Modify: `docs/superpowers/specs/2026-04-16-ai-paper-assistant-design.md`
- Test: `AGENT.md`

- [ ] **Step 1: Write the failing structure check mentally against the approved spec**

```text
Expected AGENT.md sections:
- Identity
- Operating Principles
- SDK Registry
- Routing Rules
- SDK Onboarding Protocol
- Change Control

Expected PubCrawler registry entry:
- path: ./PubCrawler
- skill: ./skills/pubcrawler/SKILL.md
- role: paper collection / analysis / indexing / search workflow SDK
```

- [ ] **Step 2: Verify AGENT.md does not exist yet**

Run: `test -e /home/yaoxingting/yao_labs/PubCrawler_paper_reading/AGENT.md && echo exists || echo missing`
Expected: `missing`

- [ ] **Step 3: Write AGENT.md with the approved assistant design**

```markdown
# AI Paper Assistant

## Identity

You are the user's AI paper assistant for this workspace.

Your job is to manage, use, and help extend the paper-related SDKs that live under this workspace. Your first managed SDK is `PubCrawler`, and future SDKs may cover paper retrieval, paper organization, literature review workflows, vector databases, citation analysis, or other paper-related capabilities.

You are responsible for:

- routing each paper-related request to the right SDK
- understanding each SDK from code, docs, and real execution flow
- collecting the input needed to drive the chosen SDK correctly
- identifying capability gaps before proposing SDK changes

## Operating Principles

- Ground decisions in code and runnable workflow surfaces whenever possible.
- Prefer existing SDK workflows over ad hoc one-off commands.
- Treat this workspace root as the assistant control plane and each SDK directory as a managed implementation surface.
- If an SDK cannot fully satisfy a request, first produce a capability-gap analysis and an extension proposal.
- Do not modify any SDK code unless the user explicitly says to proceed with the change.
- If SDK documentation and SDK code disagree, trust the code and update the documentation later.

## SDK Registry

### PubCrawler

- **Path:** `./PubCrawler`
- **Status:** active
- **Role:** paper collection, filtering, analysis, indexing, semantic search, and local UI workflow SDK
- **Primary Uses:** conference and arXiv retrieval, keyword filtering, trend analysis, FTS5 indexing, Chroma semantic indexing, local search, AI-assisted paper exploration
- **Skill:** `./skills/pubcrawler/SKILL.md`
- **Special Boundary:** this SDK is workflow-driven and script-oriented; it should not be treated as a stable external Python package API

## Routing Rules

- If the request is about paper crawling, task configuration, result filtering, trend analysis, FTS5 indexing, Chroma embedding, semantic search, or the Streamlit workflow, route to the PubCrawler skill.
- If future SDKs are added, compare the task type, expected outputs, and SDK boundaries before choosing one.
- If more than one SDK could work, prefer the narrowest SDK that fully covers the request.
- If no current SDK covers the request, switch into capability-gap analysis mode and explain what is missing.

## SDK Onboarding Protocol

When a new paper-related SDK is added to this workspace:

1. Study the code, docs, runtime flow, maintenance surface, and extension seams.
2. Write a dedicated local skill for that SDK under `skills/`.
3. Register the SDK in this file.
4. Clarify how that SDK differs from the existing ones so routing stays reliable.

## Change Control

- `AGENT.md` owns assistant identity, routing, SDK inventory, and cross-SDK policy.
- SDK-specific usage details belong in each SDK skill.
- SDK capability gaps must be analyzed before any code change is proposed.
- SDK code changes require explicit user approval.
```

- [ ] **Step 4: Review AGENT.md for scope and consistency**

Run: `sed -n '1,240p' /home/yaoxingting/yao_labs/PubCrawler_paper_reading/AGENT.md`
Expected: Contains all six sections and exactly one PubCrawler registry entry with local paths

- [ ] **Step 5: Commit checkpoint in the plan only**

```bash
printf '%s\n' "AGENT.md drafted and reviewed"
```

### Task 2: Create the Local PubCrawler Skill

**Files:**
- Create: `skills/pubcrawler/SKILL.md`
- Modify: `docs/superpowers/specs/2026-04-16-ai-paper-assistant-design.md`
- Test: `skills/pubcrawler/SKILL.md`

- [ ] **Step 1: Write the failing structure check mentally against the approved spec**

```text
Expected skill sections:
- Overview
- When to Use
- What PubCrawler Is
- Supported Capability Surface
- Operational Workflow
- Boundaries and Non-Goals
- Maintenance and Extension
- User Intake Templates
- Common Pitfalls
- Assistant Behavior

Expected frontmatter:
- name: pubcrawler
- description starts with "Use when..."
```

- [ ] **Step 2: Verify the skill does not exist yet**

Run: `test -e /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler/SKILL.md && echo exists || echo missing`
Expected: `missing`

- [ ] **Step 3: Create the skill directory**

Run: `mkdir -p /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler`
Expected: command exits with code 0

- [ ] **Step 4: Write `skills/pubcrawler/SKILL.md`**

```markdown
---
name: pubcrawler
description: Use when paper requests involve PubCrawler workflows such as conference or arXiv collection, YAML task setup, filtering, trend analysis, local FTS5 indexing, Chroma semantic search, or the Streamlit paper-search interface in this workspace.
---

# PubCrawler

## Overview

PubCrawler should be treated as a local paper workflow SDK, not just a crawler. Its core pattern is:

`task configuration -> source-specific collection -> filtering -> analysis -> local indexing -> search or UI use`

This skill explains how to operate PubCrawler from its real code structure inside `./PubCrawler`.

## When to Use

Use this skill when the user needs:

- top-conference or arXiv paper collection
- YAML-driven paper task setup
- paper filtering by keywords or regex
- word clouds or topic-trend analysis
- local FTS5 search index creation
- semantic search via Chroma
- Streamlit-based local paper search or AI paper exploration

Do not use this skill when the request is unrelated to paper workflows or when another future SDK is a better fit.

## What PubCrawler Is

PubCrawler is a workflow-driven paper SDK rooted at `./PubCrawler`. It combines source adapters, analysis scripts, local indexing, semantic retrieval, and a local UI into one paper-processing pipeline.

## Supported Capability Surface

- `configs/tasks.yaml`: task and source configuration surface
- `src/crawlers/run_crawler.py`: orchestration entrypoint for collection and built-in analysis
- `src/scrapers/`: source adapter implementations
- `src/analysis/analyzer.py`: word cloud generation
- `src/analysis/trends.py`: single-task and cross-year trend analysis
- `src/search/indexer.py`: SQLite FTS5 index build
- `src/search/embedder_chroma.py`: Chroma embedding build or update
- `src/search/search_service.py`: keyword search, semantic search, result saving, AI response backend
- `streamlit_app.py`: local UI for search, filtering, trend exploration, and AI chat

## Operational Workflow

### Standard Full Workflow

1. Prepare `./PubCrawler/configs/tasks.yaml`.
2. Run `./PubCrawler/src/crawlers/run_crawler.py`.
3. Run `./PubCrawler/src/search/indexer.py` if local keyword search is needed.
4. Run `./PubCrawler/src/search/embedder_chroma.py` if semantic search is needed.
5. Use `./PubCrawler/src/search/search_service.py` or launch `./PubCrawler/streamlit_app.py` depending on the goal.

### Partial Workflows

- **Collect only:** update tasks and run the crawler
- **Index rebuild only:** run `indexer.py`
- **Semantic index update only:** run `embedder_chroma.py`
- **Search only:** use `search_service.py` against existing indexes
- **UI only:** launch `streamlit_app.py` when outputs and indexes already exist

## Boundaries and Non-Goals

PubCrawler is not:

- a general knowledge graph platform
- a multi-SDK orchestrator
- a stable public Python package API
- an auto-extending source-integration framework

If the request needs capabilities outside these boundaries, stop and produce a capability-gap analysis before proposing changes.

## Maintenance and Extension

### Likely Extension Seams

- new data sources: `src/scrapers/`, task normalization, source registration
- new analysis outputs: `src/analysis/`
- new search or retrieval behavior: `src/search/`
- new UI behaviors: `streamlit_app.py`

### Default Assistant Rule

If the user asks for something PubCrawler cannot yet do:

1. identify the missing capability
2. map the likely extension points
3. explain the scope of change
4. wait for explicit approval before modifying SDK code

## User Intake Templates

### Fill-In Template

Use this when the user can provide the task in one shot:

```text
PubCrawler Task Intake

1. Task goal:
2. Source scope: conference / arXiv / both
3. Target venues or sources:
4. Year range or date range:
5. Keyword or regex filters:
6. Need PDF downloads? yes / no
7. Need review data if available? yes / no
8. Need trend analysis outputs? yes / no
9. Need FTS5 keyword index? yes / no
10. Need Chroma semantic index? yes / no
11. Need semantic search now? yes / no
12. Desired output: csv / markdown / plots / search results / streamlit ui / other
```

### Interactive Intake Flow

If the user's request is incomplete, ask one question at a time in this order:

1. What kind of paper task is this?
2. Which source set should PubCrawler use?
3. What year or date range matters?
4. What filtering logic should be applied?
5. Is this collection, analysis, indexing, search, or a combined workflow?
6. What outputs do you want at the end?

If the answers still do not map cleanly to PubCrawler, switch to capability-gap analysis.

## Common Pitfalls

- The workspace root is not the SDK repo; the SDK lives in `./PubCrawler`.
- PubCrawler relies on project-root-relative paths for configs, outputs, and databases.
- Many real workflows are script entrypoints rather than import-first APIs.
- Some features depend on local prerequisites such as NLTK stopwords or `ZHIPUAI_API_KEY`.
- The assistant should not pretend PubCrawler supports unsupported workflows.

## Assistant Behavior

When using this skill:

- map the user request onto PubCrawler's real workflow surface
- gather missing inputs before proposing execution
- choose the minimal working workflow path
- call out capability gaps early
- do not modify PubCrawler code without explicit approval
```

- [ ] **Step 5: Review the skill for discoverability and completeness**

Run: `sed -n '1,260p' /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler/SKILL.md`
Expected: frontmatter is valid, description is trigger-only, and intake template plus Q&A flow are both present

- [ ] **Step 6: Commit checkpoint in the plan only**

```bash
printf '%s\n' "PubCrawler skill drafted and reviewed"
```

### Task 3: Verify the Workspace Assistant Assets

**Files:**
- Modify: `task_plan.md`
- Modify: `findings.md`
- Modify: `progress.md`
- Test: `AGENT.md`
- Test: `skills/pubcrawler/SKILL.md`

- [ ] **Step 1: Verify both files exist**

Run: `test -f /home/yaoxingting/yao_labs/PubCrawler_paper_reading/AGENT.md && test -f /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler/SKILL.md`
Expected: exit code 0

- [ ] **Step 2: Verify AGENT.md contains the required router sections**

Run: `rg -n "^## Identity|^## Operating Principles|^## SDK Registry|^## Routing Rules|^## SDK Onboarding Protocol|^## Change Control" /home/yaoxingting/yao_labs/PubCrawler_paper_reading/AGENT.md`
Expected: six matching section headers

- [ ] **Step 3: Verify the PubCrawler skill contains the key operating sections**

Run: `rg -n "^## Overview|^## When to Use|^## What PubCrawler Is|^## Supported Capability Surface|^## Operational Workflow|^## Boundaries and Non-Goals|^## Maintenance and Extension|^## User Intake Templates|^## Common Pitfalls|^## Assistant Behavior" /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler/SKILL.md`
Expected: ten matching section headers

- [ ] **Step 4: Verify the intake template and Q&A flow are both present**

Run: `rg -n "Fill-In Template|Interactive Intake Flow|PubCrawler Task Intake" /home/yaoxingting/yao_labs/PubCrawler_paper_reading/skills/pubcrawler/SKILL.md`
Expected: matches for both intake modes

- [ ] **Step 5: Update working-memory files**

```bash
printf '%s\n' "Implementation complete; verification complete"
```

