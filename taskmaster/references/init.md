# Task Master Initialization

Initialize task management in a project, optionally parsing PRD documents into tasks.

## Usage

```
/prd-taskmaster init [prd-file]
```

- `prd-file` (optional): Path to PRD document to parse

## Process

### 1. Create Directory Structure

Create the `.taskmaster/` directory structure:

```bash
mkdir -p .taskmaster/tasks .taskmaster/docs .taskmaster/reports .taskmaster/templates
```

### 2. Initialize tasks.json

Check if `.taskmaster/tasks/tasks.json` exists. If not, create it:

```json
{
  "tasks": [],
  "metadata": {
    "createdAt": "[current ISO timestamp]",
    "version": "1.0.0"
  }
}
```

### 3. Parse PRD (if provided)

If a PRD file path is provided:

**Read the PRD file** using the Read tool

**Extract tasks** by analyzing:
- "Implementation Roadmap" or "Phase" sections
- "Features" or "Requirements" sections
- Bullet points describing work items
- Numbered lists of tasks

**For each extracted task, generate:**
- `id`: Sequential integer starting from 1
- `title`: Clear, actionable title (5-10 words)
- `description`: One-paragraph summary
- `status`: "pending"
- `priority`: "high", "medium", or "low" based on phase/importance
- `dependencies`: Array of task IDs (from earlier phases)
- `details`: Detailed implementation guidance
- `testStrategy`: How to verify completion
- `subtasks`: Empty array (to be expanded later)

**Infer dependencies from structure:**
- Phase 0 tasks have no dependencies
- Phase 1 tasks depend on relevant Phase 0 tasks
- Tasks within a phase may depend on each other based on context

### 4. Write Updated tasks.json

After extracting tasks, read the current tasks.json, add the new tasks, and write back.

### 5. Display Summary

```
## Task Master Initialized

### Directory Structure Created
- .taskmaster/tasks/tasks.json - Task database
- .taskmaster/docs/ - Documentation
- .taskmaster/reports/ - Analysis reports
- .taskmaster/templates/ - Templates

### Tasks
[If PRD was parsed:]
- Extracted [N] tasks from PRD
- Tasks organized into [N] phases
- High priority: [count]
- Medium priority: [count]
- Low priority: [count]

[If no PRD:]
- Empty task list created
- Add tasks with /prd-taskmaster add

### Next Steps
1. Review tasks: /prd-taskmaster list
2. Get next task: /prd-taskmaster next
3. Add more tasks: /prd-taskmaster add [description]
```

## PRD Parsing Examples

### From Implementation Roadmap (RPG-style PRD)

Input:
```markdown
### Phase 0: Foundation
- Task: Set up error handling framework
- Task: Create configuration system

### Phase 1: Core Features
- Task: Implement user model
- Task: Add authentication
```

Output tasks:
- Task 1: "Set up error handling framework" (priority: high, deps: [])
- Task 2: "Create configuration system" (priority: high, deps: [])
- Task 3: "Implement user model" (priority: high, deps: [1, 2])
- Task 4: "Add authentication" (priority: high, deps: [1, 2, 3])

### From Features Section

Input:
```markdown
## Features

### User Authentication
- Implement login/logout
- JWT token management
- Password reset flow

### Dashboard
- Display user metrics
- Real-time updates
```

Output tasks:
- Task 1: "Implement login/logout" (priority: high)
- Task 2: "JWT token management" (priority: high, deps: [1])
- Task 3: "Password reset flow" (priority: medium, deps: [1, 2])
- Task 4: "Display user metrics" (priority: medium)
- Task 5: "Real-time updates" (priority: low, deps: [4])

## Error Handling

- PRD file not found: Report error and continue with empty task list
- tasks.json already exists: Preserve existing tasks, append new ones (report merge)
- Directory creation fails: Report specific error
