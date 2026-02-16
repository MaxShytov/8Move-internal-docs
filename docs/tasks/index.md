# Tasks

## Workflow Overview

Development follows a **7-phase methodology** where each task goes through a structured lifecycle:

```mermaid
graph LR
    P0[Phase 0<br>Task Definition] --> P1[Phase 1<br>Research]
    P1 --> P2[Phase 2<br>Planning]
    P2 --> P3[Phase 3<br>Implementation]
    P3 --> P4[Phase 4<br>Refactoring]
    P4 --> P5[Phase 5<br>Testing]
    P5 --> P6[Phase 6<br>Documentation]
```

| Phase | Purpose | Output |
|-------|---------|--------|
| **Phase 0** | Define the task, ask clarifying questions, find benchmarks | Refined task description with success criteria |
| **Phase 1** | Research current architecture, dependencies, patterns | Architecture overview and constraints |
| **Phase 2** | Design the implementation plan | Step-by-step plan with risks |
| **Phase 3** | Write the code | Working implementation |
| **Phase 4** | Review and refactor | Clean, maintainable code |
| **Phase 5** | Write and run tests | All tests passing |
| **Phase 6** | Create documentation for 4 audiences | Docs for AI chatbot, devs, admins, users |

## Task Files

Each task exists in **one place only** — no duplication:

| What | Where | Purpose |
|------|-------|---------|
| **Full working file** | `docs/_ai_context/CURRENT_TASK_*.md` (main repo) | Single source of truth. Contains research, plan, implementation tracking. Used by developer and Claude Code. |
| **Summary card** | `docs_internal/docs/tasks/current/XX-NN.md` (this site) | Compact overview for quick reference. Links to the full file. |

```
Main repo (docs/_ai_context/):
├── CURRENT_TASK_*.md              # Full working files (single source of truth)
├── COMPLETED_TASKS/               # Archived completed tasks
├── 01. Workflow.md                # Full methodology reference
├── 02. Current task template.md   # Task file template
└── ...

Docs site (docs_internal/docs/tasks/):
├── current/
│   ├── index.md                   # Summary table of all active tasks
│   ├── BE-01.md                   # Summary card → links to full file
│   └── BE-01.1.md                 # Summary card → links to full file
├── completed.md                   # Archive of completed tasks
└── templates/                     # Documentation templates (4 audiences)
```

## Phase 6: Documentation for 4 Audiences

When a task is completed, documentation must be created for **4 audiences**:

| Audience | What they need | Location |
|----------|---------------|----------|
| [AI Chatbot](templates/ai-chatbot.md) | FAQ-style answers for support bot + Claude Code context | `docs/support/chatbot/` + `docs/_ai_context/` |
| [Developers](templates/developers.md) | Architecture, API, models, code examples | `docs/architecture/django-backend/workflows/` |
| [Platform Admins](templates/admins.md) | Django admin setup, configuration, management | `docs/features/[app]/[feature]/admin-guide.md` |
| [End Users](templates/users.md) | Step-by-step guides with screenshots | `docs/features/[app]/[feature]/user-guide.md` |

See [Templates](templates/ai-chatbot.md) for ready-to-use templates for each audience.

## Quick Links

- [Current Tasks](current/index.md) — what's being worked on right now
- [Bug Tracker](bugs/index.md) — known bugs with investigation and status
- [Backlog](backlog.md) — planned tasks not yet in development
- [Completed Tasks](completed.md) — archive of all finished tasks
