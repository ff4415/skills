# RPG PRD Partitioning

Split large PRDs into context-preserving partitioned files with progressive disclosure.

## Usage

```
/rpg-prd partition <prd-path>
```

- `prd-path`: Path to PRD file or directory

## When to Use

- PRD has >= 1500 total lines
- Auto-triggered by generate for PRDs >= 2000 lines
- Manual partitioning to improve navigation

## Process

### 1. Load and Analyze PRD

1. Read source PRD file(s)
2. Count total lines
3. If < 1500 lines, skip partitioning (not needed)
4. Parse document structure:
   - Section boundaries (## headers)
   - Phase boundaries (### Phase N)
   - Module definitions
   - Dependency graph location

### 2. Extract Core Components

#### Dependency Graph

Find and extract:
- Mermaid diagram
- Dependency chain text
- Module dependency listings

Save to `dependency-graph.md`:

```markdown
# Dependency Graph: [Project Name]

## Visual Overview

```mermaid
graph TD
    [extracted diagram]
```

## Dependency Chain

### Foundation Layer (Phase 0)
[Foundation modules]

### [Layer Name] (Phase N)
[Layer definitions]

## Module Dependency Matrix

| Module | Depends On | Depended By |
|--------|-----------|-------------|
| [module] | [deps] | [reverse deps] |
```

#### Overview

Extract non-phase content:
- Problem statement
- Target users
- Success metrics
- Full capability tree
- Repository structure
- Test strategy overview
- Architecture overview
- Risks

Save to `overview.md` with navigation header.

### 3. Create Phase Partitions

For each phase in Implementation Roadmap:

1. **Identify phase boundaries**
   - Start: `### Phase N:` header
   - End: Next phase header or section end

2. **Extract phase content**
   - Goal, entry criteria
   - All tasks with full details
   - Exit criteria, delivers

3. **Include relevant module definitions**
   - Modules mentioned in phase tasks
   - Full definitions from structural decomposition

4. **Add navigation**
   - Header with links to manifest and adjacent phases
   - Footer with next steps

Save to `phase-N-[sanitized-name].md`:

```markdown
# Phase [N]: [Phase Name]

> **Navigation**: [Manifest](manifest.md) | [Previous](phase-N-1-name.md) | [Next](phase-N+1-name.md)
>
> **Dependencies**: Requires completion of Phase [N-1]

---

## Goal
[Phase goal]

## Entry Criteria
[What must be true before starting]

---

## Tasks

### Task [N].1: [Task Name]
**Dependencies**: [task-ids or "none"]

**Description**:
[Full task description]

**Acceptance Criteria**:
- [ ] [Criterion 1]
- [ ] [Criterion 2]

**Test Strategy**:
[How to verify completion]

---

## Module Definitions

### Module: [Name]
**Maps to capability**: [Capability]
**Responsibility**: [Purpose]

**File structure**:
```
[file tree]
```

**Exports**:
- `function()` - [description]

---

## Exit Criteria
[Observable outcomes proving phase complete]

## Delivers
[What becomes possible after this phase]

---

> **Navigation**: [Manifest](manifest.md) | [Previous](phase-N-1-name.md) | [Next](phase-N+1-name.md)
```

### 4. Generate Manifest

Create `manifest.md` as always-load-first index:

```markdown
# PRD Manifest: [Project Name]

## Quick Context
[One paragraph summary of the project and its goals]

## Visual Overview

```mermaid
[Dependency graph - high level only]
```

## Project Phases

| Phase | Name          | Status  | Tasks | Entry Criteria        |
|-------|--------------|---------|-------|-----------------------|
| 0     | Foundation   | pending | 8     | None (starting point) |
| 1     | Core Layer   | pending | 12    | Phase 0 complete      |
| 2     | Integration  | pending | 6     | Phase 1 complete      |

## Module Index

- **foundation/** - Error handling, config, types (Phase 0)
- **data/** - Models, database layer (Phase 1)
- **core/** - Business logic (Phase 1)
- **api/** - HTTP layer (Phase 2)

## Files

### Always Load First
1. **manifest.md** (this file) - Project overview and navigation
2. **dependency-graph.md** - Complete dependency information

### Progressive Loading
3. **overview.md** - Problem, users, capabilities, structure
4. **phase-N-[name].md** - Individual phase implementations

## Loading Instructions

**For implementation work:**
1. Load manifest.md (context and navigation)
2. Load dependency-graph.md (dependencies always visible)
3. Load specific phase file you're working on

```bash
/rpg-prd load [project]:phase-1
```

**Context formula:**
- manifest + dependency-graph + phase file = complete working context
- Total: ~500-800 lines per phase (efficient)

**For planning:**
Load overview.md for full capability and architecture view

**For complete view:**
Load all files (warning: large context)

## Statistics
- Total capabilities: [N]
- Total features: [N]
- Total modules: [N]
- Total tasks: [N]
- Phases: [N]
```

### 5. Output Summary

```
## PRD Partitioned

### Original Size
- [N] lines

### Created Files
- manifest.md (quick reference)
- dependency-graph.md (dependencies)
- overview.md (problem, capabilities, architecture)
- [N] phase files (phase-0-foundation.md, phase-1-xxx.md, ...)

### Benefits
- Load only needed context
- Navigate between phases
- Dependencies always visible
- Each phase ~500-800 lines

### Next Steps

**Load specific phase:**
```
/rpg-prd load [project]:phase-1
```

**Initialize Task Master:**
```
/prd-taskmaster init .prd/[project]/overview.md
```
```

## Progressive Disclosure Pattern

**Tier 1 (Always Load):**
- manifest.md (~200 lines)

**Tier 2 (Load for Implementation):**
- dependency-graph.md (~300 lines)

**Tier 3 (Load as Needed):**
- phase-N-xxx.md (~500-800 lines each)
- overview.md (full context, ~800-1000 lines)

**Total context for implementation:**
- manifest + dependency-graph + 1 phase = ~1000-1300 lines
- Much better than loading 3000+ line monolithic PRD

## Error Handling

- PRD too small: Report partitioning not needed
- Missing sections: Attempt best-effort extraction
- Write failures: Report specific file that failed

## Notes

- Partitioning preserves all content
- Navigation links enable easy movement
- Each phase file is self-contained with module definitions
- Manifest provides quick orientation
