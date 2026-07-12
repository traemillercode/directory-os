# DirectoryOS — MVP Engineering Backlog (Official Build Plan)

This directory is the **official, approved build plan** for DirectoryOS, generated from every approved document in `docs/`:

- `docs/Product/01_PROJECT.md`
- `docs/Product/02_MVP.md`
- `docs/Product/03_ROADMAP.md`
- `docs/Product/04_IDEAS.md`
- `docs/Product/PRODUCT_REVIEW.md`
- `docs/Architecture/ARCHITECTURE.md`
- `docs/Architecture/DATABASE.md`
- `docs/Architecture/API.md`
- `docs/Automation/AUTOMATION.md`
- `docs/AI/CLAUDE_SYSTEM_PROMPT.md`, `docs/AI/CURSOR_RULES.md`, `docs/AI/GEMINI_GUIDE.md`

MVP scope and reclassification follow `PRODUCT_REVIEW.md` Section 3 (deferred items are moved to the Phase 2 backlog — **nothing is deleted**).

## Contents

| File | Purpose |
|---|---|
| [`01_MILESTONES.md`](./01_MILESTONES.md) | GitHub Milestones mapped to the 12-week MVP schedule |
| [`02_EPICS.md`](./02_EPICS.md) | GitHub Epics (tracking issues) grouping related work |
| [`03_LABELS.md`](./03_LABELS.md) | Full GitHub label taxonomy (type, phase, area, priority, status) |
| [`04_ISSUES.md`](./04_ISSUES.md) | Complete backlog of GitHub Issues, grouped by epic, each referencing source documentation |
| [`05_DEPENDENCIES.md`](./05_DEPENDENCIES.md) | Dependency graph between epics and issues |
| [`06_IMPLEMENTATION_ORDER.md`](./06_IMPLEMENTATION_ORDER.md) | Recommended build order across milestones |

## How to Use This Plan

This backlog is intended to be turned into live GitHub objects (milestones, labels, issues) by a maintainer or an automation script with repository write access (for example, via `gh milestone create`, `gh label create`, and `gh issue create`, or the GitHub REST/GraphQL API). Each issue below is written so it can be copy-created as-is, including:

- A clear title
- The epic and milestone it belongs to
- Suggested labels
- The exact source documentation section it references
- Any known dependencies

No code is generated as part of this plan — it is a project-management artifact only.
