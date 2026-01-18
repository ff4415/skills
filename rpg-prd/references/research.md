# RPG PRD Research

Organize and analyze research materials for PRD generation.

## Usage

```
/rpg-prd research <files-or-urls>
```

- `files-or-urls`: Space-separated list of files or URLs to analyze

## Process

### 1. Create Research Directory

```bash
mkdir -p .prd/research/raw .prd/research/analyzed/requirements .prd/research/analyzed/technical .prd/research/analyzed/constraints
```

### 2. Collect Materials

For each provided file or URL:

**Local files:**
- Copy to `.prd/research/raw/`
- Read and analyze content

**URLs:**
- Fetch content using WebFetch
- Save to `.prd/research/raw/url-[hash].md`
- Analyze fetched content

### 3. Analyze and Categorize

For each material, analyze and extract:

**Requirements** → `.prd/research/analyzed/requirements/`
- User stories and use cases
- Functional requirements
- Feature requests
- Success criteria

**Technical** → `.prd/research/analyzed/technical/`
- API specifications
- Database schemas
- Architecture patterns
- Technology constraints
- Integration requirements

**Constraints** → `.prd/research/analyzed/constraints/`
- Business constraints (timeline, budget, scope)
- Technical constraints (platform, compatibility)
- Resource constraints (team, infrastructure)
- Compliance and security requirements

### 4. Extract Key Information

For each category, create analyzed files with:

```markdown
# [Source Name] - [Category]

## Source
- File: [original filename or URL]
- Analyzed: [timestamp]

## Key Points
- [Extracted point 1]
- [Extracted point 2]

## Details
[Relevant details, quotes, or data]

## Cross-References
- Related to: [other analyzed files]
```

### 5. Create Manifest

Write `.prd/research/manifest.json`:

```json
{
  "createdAt": "2024-01-15T10:30:00Z",
  "sources": [
    {
      "type": "file",
      "path": "design-doc.md",
      "analyzedAs": ["requirements/design-doc.md"]
    },
    {
      "type": "url",
      "url": "https://example.com/api-spec",
      "analyzedAs": ["technical/api-spec.md"]
    }
  ],
  "summary": {
    "requirements": 5,
    "technical": 3,
    "constraints": 2
  }
}
```

### 6. Display Summary

```
## Research Organized

### Sources Analyzed: [N]

| Source              | Type | Category         |
|---------------------|------|------------------|
| design-doc.md       | file | requirements     |
| api-spec.md         | file | technical        |
| project-brief.md    | file | requirements     |
| tech-constraints.md | file | technical, constraints |

### Categories
- Requirements: 5 files
- Technical: 3 files
- Constraints: 2 files

### Research Location
.prd/research/

### Next Steps
Generate PRD from research:
```
/rpg-prd generate <project-name>
```
```

## Analysis Guidelines

**For requirements materials:**
- Extract user needs and goals
- Identify must-have vs nice-to-have features
- Note success metrics
- Capture use cases

**For technical materials:**
- Document API contracts
- Note technology stack decisions
- Capture architectural patterns
- List integration points

**For constraints:**
- Identify hard constraints (immovable)
- Note soft constraints (preferences)
- Document trade-offs
- Capture dependencies on external systems

## Error Handling

- File not found: Report error, continue with other files
- URL fetch fails: Report error, continue
- Invalid format: Attempt best-effort parsing
- Write failure: Report specific directory issue

## Notes

- Research is optional - can generate PRD directly from conversation context
- Multiple materials can map to multiple categories
- Cross-references help connect related concepts
- Manifest enables reproducible PRD generation
