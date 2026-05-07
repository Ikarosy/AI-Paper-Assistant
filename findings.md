# Findings & Decisions
<!-- 
  WHAT: Your knowledge base for the task. Stores everything you discover and decide.
  WHY: Context windows are limited. This file is your "external memory" - persistent and unlimited.
  WHEN: Update after ANY discovery, especially after 2 view/browser/search operations (2-Action Rule).
-->

## Requirements
<!-- 
  WHAT: What the user asked for, broken down into specific requirements.
  WHY: Keeps requirements visible so you don't forget what you're building.
  WHEN: Fill this in during Phase 1 (Requirements & Discovery).
  EXAMPLE:
    - Command-line interface
    - Add tasks
    - List all tasks
    - Delete tasks
    - Python implementation
-->
<!-- Captured from user request -->
- Build an AI paper assistant identity for this workspace, with PubCrawler as the first managed SDK.
- Deeply master PubCrawler’s functionality, usage, boundaries, maintenance, and extension patterns.
- Write that knowledge down as a reusable skill.
- Create a structured workspace `AGENT.md` that manages current and future paper-related SDKs.
- Design the assistant so future SDKs for paper retrieval, organization, and database building can be onboarded cleanly.
- Make the assistant responsible not only for using SDKs but also for extending them when capabilities are insufficient.
- Store the PubCrawler skill locally in the current workspace as an asset of the AI paper assistant.
- Store the AI paper assistant `AGENT.md` in the current workspace.
- Let `AGENT.md` handle SDK routing and management while each skill only documents one concrete SDK.
- When an SDK capability is insufficient, default to analysis and extension proposal first; do not modify SDK code until the user explicitly approves.
- Add a user-facing intake template or interactive Q&A flow that collects the information needed to drive the SDK correctly for a paper task.

## Research Findings
<!-- 
  WHAT: Key discoveries from web searches, documentation reading, or exploration.
  WHY: Multimodal content (images, browser results) doesn't persist. Write it down immediately.
  WHEN: After EVERY 2 view/browser/search operations, update this section (2-Action Rule).
  EXAMPLE:
    - Python's argparse module supports subcommands for clean CLI design
    - JSON module handles file persistence easily
    - Standard pattern: python script.py <command> [args]
-->
<!-- Key discoveries during exploration -->
- The current workspace root is not a git repository; the actual SDK repository is `PubCrawler/`.
- `PubCrawler` is not just a set of scrapers. Its effective pipeline is:
  task YAML -> task normalization -> source-specific scrapers -> filtering -> markdown/csv/wordcloud outputs -> single-task analysis -> cross-year trends -> SQLite FTS5 index -> Chroma embeddings -> search/AI UI.
- `src/crawlers/run_crawler.py` is the main orchestration entrypoint and the clearest expression of PubCrawler's supported workflow surface.
- `configs/tasks.yaml` contains two conceptual layers:
  `source_definitions` as a source encyclopedia and `tasks` as executable plans.
- The extension seam for new sources is concrete:
  add a new scraper under `src/scrapers/`, register it in `SCRAPER_MAPPING`, and ensure task/source-definition normalization can produce the required `task_info`.
- Search is built on a local `database/` directory containing `papers.db` and `chroma_db`.
- `src/search/search_service.py` is the central backend API for keyword search, semantic search, result formatting, and AI-response generation.
- PubCrawler already has clear feature boundaries in code:
  acquisition, analysis, indexing/search, UI, and AI chat are separate modules, though not yet packaged as a formal Python SDK.

## Technical Decisions
<!-- 
  WHAT: Architecture and implementation choices you've made, with reasoning.
  WHY: You'll forget why you chose a technology or approach. This table preserves that knowledge.
  WHEN: Update whenever you make a significant technical choice.
  EXAMPLE:
    | Use JSON for storage | Simple, human-readable, built-in Python support |
    | argparse with subcommands | Clean CLI: python todo.py add "task" |
-->
<!-- Decisions made with rationale -->
| Decision | Rationale |
|----------|-----------|
| Treat PubCrawler as an SDK-like workflow engine rather than a pip-style library | The real supported surface today is operational and modular, but not packaged with explicit public APIs |
| Put assistant orchestration artifacts at workspace root, not inside `PubCrawler/` | Future SDKs will live alongside PubCrawler, so the assistant needs a higher-level home |
| Base the future skill on actual code paths and constraints, not only the README | The user asked for mastery of boundaries, maintenance, and extension, which requires code-grounded documentation |
| Keep skills SDK-specific and put routing in `AGENT.md` | User chose `AGENT.md` as the central assistant manager; this prevents one oversized global skill as SDK count grows |
| Do not auto-edit SDKs when a capability gap appears | User wants explicit approval after an analysis/proposal step before SDK code changes |
| Use minimal local structure | User selected方案 A: only `AGENT.md` and `skills/pubcrawler/SKILL.md` for the initial assistant foundation |
| Include a user-intake template in the SDK skill | The assistant should be able to collect the exact task parameters needed to operate the SDK via fill-in forms or guided questioning |

## Issues Encountered
<!-- 
  WHAT: Problems you ran into and how you solved them.
  WHY: Similar to errors in task_plan.md, but focused on broader issues (not just code errors).
  WHEN: Document when you encounter blockers or unexpected challenges.
  EXAMPLE:
    | Empty file causes JSONDecodeError | Added explicit empty file check before json.load() |
-->
<!-- Errors and how they were resolved -->
| Issue | Resolution |
|-------|------------|
| Repo bootstrap instructions referenced a removed legacy superpowers command | Switched to the current native Codex skill-discovery installation model after verifying repo release notes and docs |
| Initial git inspection failed at workspace root | Confirmed the real repository is `PubCrawler/` and continued exploration there |

## Resources
<!-- 
  WHAT: URLs, file paths, API references, documentation links you've found useful.
  WHY: Easy reference for later. Don't lose important links in context.
  WHEN: Add as you discover useful resources.
  EXAMPLE:
    - Python argparse docs: https://docs.python.org/3/library/argparse.html
    - Project structure: src/main.py, src/utils.py
-->
<!-- URLs, file paths, API references -->
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/README.md`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/configs/tasks.yaml`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/crawlers/run_crawler.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/crawlers/config.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/scrapers/base_scraper.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/scrapers/iclr_scraper.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/search/search_service.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/search/indexer.py`
- `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler/src/search/embedder_chroma.py`

## Visual/Browser Findings
<!-- 
  WHAT: Information you learned from viewing images, PDFs, or browser results.
  WHY: CRITICAL - Visual/multimodal content doesn't persist in context. Must be captured as text.
  WHEN: IMMEDIATELY after viewing images or browser results. Don't wait!
  EXAMPLE:
    - Screenshot shows login form has email and password fields
    - Browser shows API returns JSON with "status" and "data" keys
-->
<!-- CRITICAL: Update after every 2 view/browser operations -->
<!-- Multimodal content must be captured as text immediately -->
-

---
<!-- 
  REMINDER: The 2-Action Rule
  After every 2 view/browser/search operations, you MUST update this file.
  This prevents visual information from being lost when context resets.
-->
*Update this file after every 2 view/browser/search operations*
*This prevents visual information from being lost*
