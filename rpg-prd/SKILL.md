---
name: rpg-prd
description: "RPG-style PRD (Product Requirements Document) generator with progressive disclosure. Use when users need to: (1) Research and organize materials for a PRD, (2) Generate comprehensive PRDs following RPG methodology (dual-semantics, dependency graphs, phased roadmaps), (3) Partition large PRDs for context efficiency, (4) Load specific PRD sections for implementation."
---

# RPG PRD Generator

Generate comprehensive, structured PRDs using the RPG (Requirements, Planning, Graph) methodology with built-in progressive disclosure for large documents.

## Quick Start

Complete workflow:
```
/rpg-prd research <files-or-urls>
/rpg-prd generate <project-name>
/rpg-prd load <project>:phase-1
```

## RPG Methodology

The RPG approach structures PRDs with:

1. **Dual-Semantics**: Separate WHAT (functional capabilities) from HOW (structural implementation)
2. **Explicit Dependencies**: Every module states its dependencies clearly
3. **Topological Order**: Foundation first, then progressive layers
4. **Progressive Refinement**: Broad capabilities refined into specific features and tasks

## PRD Structure

Generated PRDs include:

### Core Sections
- **Overview**: Problem statement, target users, success metrics
- **Capability Tree**: Functional decomposition (WHAT)
- **Repository Structure**: Code organization (HOW)
- **Dependency Graph**: Module dependencies with Mermaid diagram
- **Implementation Roadmap**: Phased tasks with entry/exit criteria
- **Test Strategy**: Test pyramid, coverage requirements
- **Architecture**: System components, data models, tech stack
- **Risks**: Technical, dependency, and scope risks

### File Organization

**Small PRDs (< 2000 lines):**
```
.prd/project-name/
├── manifest.md        # Quick context and navigation
└── overview.md        # Complete PRD
```

**Large PRDs (>= 2000 lines):**
```
.prd/project-name/
├── manifest.md           # Summary and navigation
├── dependency-graph.md   # Full dependency visualization
├── overview.md           # Problem, users, capabilities
├── phase-0-foundation.md # Foundation tasks
├── phase-1-xxx.md        # Phase 1 tasks
└── phase-N-xxx.md        # Additional phases
```

## Operations

### Research
See [references/research.md](references/research.md) for:
- Organizing research materials
- Analyzing requirements, technical docs, constraints
- Creating categorized research structure

### Generate
See [references/generate.md](references/generate.md) for:
- Synthesizing research into RPG structure
- Creating capability trees and dependency graphs
- Generating phased implementation roadmaps
- Automatic partitioning for large PRDs

### Partition
See [references/partition.md](references/partition.md) for:
- Splitting large PRDs into manageable files
- Context-preserving navigation
- Progressive disclosure patterns

### Load
See [references/load.md](references/load.md) for:
- Loading specific PRD phases for implementation
- Manifest + dependency-graph + phase = complete context
- Navigating between phases

## Integration with Task Master

RPG PRDs work seamlessly with Task Master:

```
# Generate PRD
/rpg-prd generate my-feature

# Initialize tasks from PRD
/prd-taskmaster init .prd/my-feature/overview.md
```

Task Master automatically:
- Parses phases into tasks
- Infers task dependencies from phase structure
- Assigns priorities based on phase order
- Preserves task hierarchy

## Progressive Disclosure Benefits

- **Context efficiency**: Load only needed sections
- **Navigation**: Manifest provides quick orientation
- **Dependencies always visible**: Dependency graph in separate file
- **Phase isolation**: Implement one phase without loading entire PRD
- **Scalability**: Handle projects with 100+ tasks

## Error Handling

- **No research**: Suggest running research first or provide content directly
- **Invalid project name**: Sanitize to filesystem-safe name
- **Write failures**: Report specific file that failed
- **Missing phases**: Validate phase identifiers

## Notes

- Works standalone or with Task Master
- Large PRDs auto-partition at 2000 lines
- All PRDs include dependency visualization
- Phases include entry/exit criteria for clear boundaries
