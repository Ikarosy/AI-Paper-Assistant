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
