---
name: prd-taskmaster
description: "RPG-style task management system for software projects. Use when users need to: (1) Initialize task tracking in a project, (2) Parse PRD documents into actionable tasks, (3) Add, update, or complete tasks, (4) Expand tasks into subtasks, (5) Analyze task complexity, (6) List tasks or get the next available task, (7) Track task dependencies and priorities."
---

# Task Master

Task management system optimized for software development projects, with built-in support for RPG-style PRD parsing.

## Quick Start

Initialize task management in a project:
```
/prd-taskmaster init [prd-file]
```

Common operations:
```
/prd-taskmaster add "implement user authentication"
/prd-taskmaster list
/prd-taskmaster next
/prd-taskmaster complete 1
```

## Core Concepts

Task Master uses a `.taskmaster/` directory structure:
```
.taskmaster/
├── tasks/
│   └── tasks.json          # Task database
├── docs/                   # Documentation
├── reports/                # Analysis reports
└── templates/              # Templates
```

### Task Schema

Each task includes:
- **id**: Integer (1, 2, 3) or dotted for subtasks (1.1, 1.2)
- **title**: Clear, actionable name
- **description**: One-paragraph summary
- **status**: "pending", "in_progress", "completed", "blocked"
- **priority**: "high", "medium", "low"
- **dependencies**: Array of task IDs
- **details**: Implementation guidance
- **testStrategy**: Verification approach
- **subtasks**: Array of sub-tasks

## Operations

### Initialize
See [references/init.md](references/init.md) for:
- Creating directory structure
- Parsing PRD documents into tasks
- Inferring task dependencies
- Priority assignment

### Task Operations
See [references/task-operations.md](references/task-operations.md) for:
- **add**: Create new tasks with AI-generated details
- **update**: Add notes or modify task properties
- **complete**: Mark tasks complete with verification
- **list**: Display tasks with filtering
- **next**: Find the next available task

### Task Analysis
See [references/task-analysis.md](references/task-analysis.md) for:
- **analyze**: Assess task complexity and recommend subtask breakdown
- **expand**: Split tasks into detailed subtasks

## PRD Integration

Task Master works seamlessly with RPG-style PRDs:

1. **From RPG PRD**: Initialize with `/prd-taskmaster init .prd/my-project/overview.md`
2. **Automatic parsing**: Extracts phases, tasks, and dependencies
3. **Preserves structure**: Phase 0 → Foundation tasks, Phase 1 → Core tasks, etc.

## Task Selection Logic

When using `/prd-taskmaster next`:
1. Filter for tasks with status "pending"
2. Exclude tasks with unmet dependencies
3. Prioritize by: high → medium → low
4. Return first eligible task

## Error Handling

- **No tasks.json**: Initialize first with `/prd-taskmaster init`
- **Invalid task ID**: Report error and suggest `/prd-taskmaster list`
- **Unmet dependencies**: Report blocking tasks
- **PRD parse errors**: Report specific issue and continue with partial results

## Notes

- Task Master is self-contained (no external CLI dependencies)
- Works with or without RPG PRDs
- Task IDs are permanent (never reused after deletion)
- Dependencies are validated before task execution
