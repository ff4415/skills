# RPG PRD Loading

Load specific PRD partitions with necessary context for implementation work.

## Usage

```
/rpg-prd load <project>:<partition>
```

Formats:
- `project-name:phase-N` - Load specific phase
- `project-name:overview` - Load overview document
- `project-name:all` - Load everything (warning: large context)
- `project-name` - Show available partitions

Examples:
```
/rpg-prd load auth-system:phase-1
/rpg-prd load api-refactor:phase-0
/rpg-prd load my-feature:overview
```

## Process

### 1. Parse Arguments

Extract:
- **Project name**: Text before the colon
- **Partition**: Text after the colon (or empty)

Construct path:
```
.prd/[project-name]/
```

### 2. Validate Project

1. Check if directory exists
2. Check if `manifest.md` exists
3. If not found, list available projects:
   ```bash
   ls .prd/
   ```
   Output options and exit.

### 3. Always Load Context Files (Tier 1 + Tier 2)

**Always read these first:**

1. **manifest.md** - Project index and overview
   ```
   Read .prd/[project]/manifest.md
   ```

2. **dependency-graph.md** - Full dependency information
   ```
   Read .prd/[project]/dependency-graph.md
   ```

These provide essential context regardless of partition.

### 4. Load Requested Partition (Tier 3)

#### If `phase-N`:

1. Find matching phase file:
   ```
   .prd/[project]/phase-N-*.md
   ```
   Use Glob pattern: `phase-N-*.md`

2. Read the phase file

3. If not found, list available phases from manifest

#### If `overview`:

1. Read `.prd/[project]/overview.md`

#### If `all`:

1. Read all files in directory
2. Warn about large context size

#### If no partition specified:

1. Show available partitions from manifest
2. Suggest command format

### 5. Output Combined Context

Format to clearly show what was loaded:

```
## PRD Context Loaded: [Project Name]

### Context Summary
- **Project**: [name]
- **Partition**: [phase-N / overview / all]
- **Total Context**: ~[N] lines

---

## Manifest (Always Loaded)

[Full content of manifest.md]

---

## Dependency Graph (Always Loaded)

[Full content of dependency-graph.md]

---

## Phase [N]: [Phase Name]

[Full content of phase file]

---

## Ready for Implementation

### Current Phase Context
- Phase [N] loaded
- Dependencies visible in graph above
- Navigate to adjacent phases using links in phase file

### Next Steps
1. Review phase tasks and entry criteria
2. Implement tasks in order
3. Use Task Master to track progress:
   ```
   /prd-taskmaster init .prd/[project]/overview.md
   ```
```

## Context Efficiency

**Typical loads:**

| Load Type | Files | Approximate Lines | Use Case |
|-----------|-------|------------------|----------|
| Phase work | manifest + deps + phase-N | ~1000-1300 | Implementation |
| Planning | manifest + overview | ~1000-1200 | Architecture review |
| Complete | all files | ~3000-5000 | Full context |

**Recommendation:**
Load only what you need. The manifest + dependency-graph + single phase provides complete working context for implementation.

## Examples

### Load Phase for Implementation

```
/rpg-prd load auth-system:phase-1

## PRD Context Loaded: Authentication System

### Context Summary
- Project: auth-system
- Partition: phase-1
- Total Context: ~1200 lines

[manifest content]
[dependency-graph content]
[phase-1 content]

### Ready for Implementation
Phase 1: Core Authentication
- 12 tasks identified
- Entry criteria: Phase 0 complete
- Use Task Master to track work
```

### Show Available Partitions

```
/rpg-prd load auth-system

## Available Partitions: Authentication System

### Project Files
- overview.md - Complete project overview
- phase-0-foundation.md - Foundation layer (8 tasks)
- phase-1-core-auth.md - Core authentication (12 tasks)
- phase-2-integrations.md - API integrations (6 tasks)

### Usage
Load specific partition:
```
/rpg-prd load auth-system:phase-1
/rpg-prd load auth-system:overview
```

### Summary
- Total phases: 3
- Total tasks: 26
- See manifest.md for full project overview
```

### Load Overview

```
/rpg-prd load auth-system:overview

## PRD Context Loaded: Authentication System

[manifest content]
[dependency-graph content]
[overview content - problem, users, capabilities, structure, risks]

Use for:
- Architecture planning
- Understanding full scope
- Making design decisions
```

## Navigation Between Phases

After loading a phase, use navigation links in the phase file:

```markdown
> **Navigation**: [Manifest](manifest.md) | [Previous: Phase 0](phase-0-foundation.md) | [Next: Phase 2](phase-2-integrations.md)
```

Load adjacent phases as needed:
```
/rpg-prd load auth-system:phase-0
/rpg-prd load auth-system:phase-2
```

## Error Handling

- Project not found: List available projects
- Phase not found: List available phases
- File read error: Report specific file issue
- Malformed partition: Show correct format

## Notes

- Manifest + dependency-graph always loaded for context
- Each phase is self-contained with module definitions
- Loading single phase keeps context efficient
- Use overview for planning, phases for implementation
- Task Master can parse overview to create task database
