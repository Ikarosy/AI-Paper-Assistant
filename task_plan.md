# Task Plan: [Brief Description]
<!-- 
  WHAT: This is your roadmap for the entire task. Think of it as your "working memory on disk."
  WHY: After 50+ tool calls, your original goals can get forgotten. This file keeps them fresh.
  WHEN: Create this FIRST, before starting any work. Update after each phase completes.
-->

## Goal
Establish this workspace as a long-lived AI paper assistant hub by mastering the embedded PubCrawler SDK, designing a reusable SDK-management architecture, and then creating a PubCrawler skill plus a structured AGENT.md for future paper-related SDKs.

## Current Phase
<!-- 
  WHAT: Which phase you're currently working on (e.g., "Phase 1", "Phase 3").
  WHY: Quick reference for where you are in the task. Update this as you progress.
-->
Phase 1

## Phases
<!-- 
  WHAT: Break your task into 3-7 logical phases. Each phase should be completable.
  WHY: Breaking work into phases prevents overwhelm and makes progress visible.
  WHEN: Update status after completing each phase: pending → in_progress → complete
-->

### Phase 1: Requirements & Discovery
<!-- 
  WHAT: Understand what needs to be done and gather initial information.
  WHY: Starting without understanding leads to wasted effort. This phase prevents that.
-->
- [x] Understand user intent
- [x] Identify constraints and requirements
- [x] Document findings in findings.md
- **Status:** complete
<!-- 
  STATUS VALUES:
  - pending: Not started yet
  - in_progress: Currently working on this
  - complete: Finished this phase
-->

### Phase 2: Planning & Structure
<!-- 
  WHAT: Decide how you'll approach the problem and what structure you'll use.
  WHY: Good planning prevents rework. Document decisions so you remember why you chose them.
-->
- [x] Define technical approach for AI paper assistant + SDK registry
- [x] Decide where the PubCrawler skill should live and how AGENT.md should reference it
- [x] Document decisions with rationale
- **Status:** complete

### Phase 3: Implementation
<!-- 
  WHAT: Actually build/create/write the solution.
  WHY: This is where the work happens. Break into smaller sub-tasks if needed.
-->
- [x] Create the PubCrawler usage skill
- [x] Create workspace-level AGENT.md for AI paper assistant and SDK management
- [x] Add any supporting docs or registry files needed for future SDK onboarding
- **Status:** complete

### Phase 4: Testing & Verification
<!-- 
  WHAT: Verify everything works and meets requirements.
  WHY: Catching issues early saves time. Document test results in progress.md.
-->
- [x] Verify the skill content matches actual PubCrawler behavior and boundaries
- [x] Verify AGENT.md is structured for future SDK expansion
- [x] Fix any issues found
- **Status:** complete

### Phase 5: Delivery
<!-- 
  WHAT: Final review and handoff to user.
  WHY: Ensures nothing is forgotten and deliverables are complete.
-->
- [ ] Review all output files
- [ ] Summarize capability boundaries and extension path
- [ ] Deliver the initial AI paper assistant foundation to the user
- **Status:** in_progress

## Key Questions
<!-- 
  WHAT: Important questions you need to answer during the task.
  WHY: These guide your research and decision-making. Answer them as you go.
  EXAMPLE: 
    1. Should tasks persist between sessions? (Yes - need file storage)
    2. What format for storing tasks? (JSON file)
-->
1. Should the generated PubCrawler skill be installed globally in `~/.agents/skills/` or stored locally in this workspace as managed assistant assets? Answer: local workspace asset.
2. What is the default permission boundary when an SDK is insufficient? Answer: analyze and propose an extension plan first; only modify code after explicit user approval.
3. How should future SDKs be registered so the AI paper assistant can discover and choose them consistently? Answer: `AGENT.md` owns routing and SDK management; skills stay SDK-specific.
4. What stable interface of PubCrawler should the skill treat as the supported SDK surface: CLI/YAML workflow only, or also importable Python modules and extension points?

## Decisions Made
<!-- 
  WHAT: Technical and design decisions you've made, with the reasoning behind them.
  WHY: You'll forget why you made choices. This table helps you remember and justify decisions.
  WHEN: Update whenever you make a significant choice (technology, approach, structure).
  EXAMPLE:
    | Use JSON for storage | Simple, human-readable, built-in Python support |
-->
| Decision | Rationale |
|----------|-----------|
| Treat `/home/yaoxingting/yao_labs/PubCrawler_paper_reading` as the assistant workspace and `/home/yaoxingting/yao_labs/PubCrawler_paper_reading/PubCrawler` as the first managed SDK repo | The outer directory is not a git repo and is better suited for cross-SDK orchestration assets like AGENT.md and future registry docs |
| Model PubCrawler as a workflow SDK, not just a scraper library | README and code show stable capabilities across task config, crawling, analysis, indexing, semantic search, and Streamlit/AI surfaces |
| Store generated skills locally in the workspace | User chose workspace-local assets rather than global skill installation |
| Use `AGENT.md` as the assistant router and SDK registry | User chose centralized assistant routing/management with per-SDK skills only |
| Default to extension analysis before modifying SDK code | User explicitly wants a plan/analysis gate before code changes for insufficient SDK capability |
| Use the minimal local structure option | User chose方案 A: workspace root `AGENT.md` plus local `skills/pubcrawler/SKILL.md` |

## Errors Encountered
<!-- 
  WHAT: Every error you encounter, what attempt number it was, and how you resolved it.
  WHY: Logging errors prevents repeating the same mistakes. This is critical for learning.
  WHEN: Add immediately when an error occurs, even if you fix it quickly.
  EXAMPLE:
    | FileNotFoundError | 1 | Check if file exists, create empty list if not |
    | JSONDecodeError | 2 | Handle empty file case explicitly |
-->
| Error | Attempt | Resolution |
|-------|---------|------------|
| Legacy superpowers bootstrap path missing | 1 | Confirmed the checkout uses the newer native Codex skill discovery model instead of the removed `superpowers-codex bootstrap` command |

## Notes
<!-- 
  REMINDERS:
  - Update phase status as you progress: pending → in_progress → complete
  - Re-read this plan before major decisions (attention manipulation)
  - Log ALL errors - they help avoid repetition
  - Never repeat a failed action - mutate your approach instead
-->
- Update phase status as you progress: pending → in_progress → complete
- Re-read this plan before major decisions (attention manipulation)
- Log ALL errors - they help avoid repetition
- Spec written at `docs/superpowers/specs/2026-04-16-ai-paper-assistant-design.md`; waiting for user review before implementation planning
