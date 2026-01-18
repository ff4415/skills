# Task Analysis

Analyze task complexity and expand tasks into subtasks.

## Table of Contents

- [Analyze Complexity](#analyze-complexity)
- [Expand into Subtasks](#expand-into-subtasks)

---

## Analyze Complexity

Analyze task complexity and recommend subtask counts.

### Usage

```
/prd-taskmaster analyze [task-id|range|all]
```

- No arguments: Analyze all tasks
- `task-id`: Analyze specific task
- `range`: Analyze range (e.g., "3-7")

### Process

1. **Read tasks.json**: Load task database
2. **Select Tasks**: Determine which tasks to analyze
3. **Analyze Each Task**: Evaluate complexity using weighted factors
4. **Determine Recommendations**: Suggest subtask counts based on score
5. **Generate Report**: Create analysis report JSON
6. **Write Report**: Save to `.taskmaster/reports/task-complexity-report.json`
7. **Display Results**: Show summary and recommendations

### Complexity Factors (1-10 scale)

1. **Technical Scope** (weight: 30%)
   - 1-2: Simple CRUD, config changes
   - 3-4: Standard feature with known patterns
   - 5-6: Multiple components, integrations
   - 7-8: Complex algorithms, new architecture
   - 9-10: Research required, cutting-edge tech

2. **Dependencies** (weight: 20%)
   - 1-2: No dependencies
   - 3-4: Few clear dependencies
   - 5-6: Multiple dependencies
   - 7-8: Complex dependency chain
   - 9-10: External system dependencies

3. **Risk/Uncertainty** (weight: 25%)
   - 1-2: Well-understood, done before
   - 3-4: Minor unknowns
   - 5-6: Some research needed
   - 7-8: Significant unknowns
   - 9-10: High uncertainty, prototyping needed

4. **Testing Complexity** (weight: 15%)
   - 1-2: Simple unit tests
   - 3-4: Standard test coverage
   - 5-6: Integration tests needed
   - 7-8: Complex test scenarios
   - 9-10: Performance/load testing required

5. **Integration Points** (weight: 10%)
   - 1-2: Isolated change
   - 3-4: Few touch points
   - 5-6: Multiple integrations
   - 7-8: API contracts, migrations
   - 9-10: Breaking changes, coordination needed

**Calculate weighted score:**
```
score = (technical * 0.30) + (deps * 0.20) + (risk * 0.25) + (testing * 0.15) + (integration * 0.10)
```

### Recommended Subtasks by Score

| Score | Complexity | Recommended Subtasks |
|-------|------------|---------------------|
| 1-2   | Trivial    | 0 (atomic task)     |
| 3-4   | Low        | 2-3                 |
| 5-6   | Medium     | 4-5                 |
| 7-8   | High       | 6-8                 |
| 9-10  | Very High  | 8-10 + consider splitting |

### Example Output

```
## Complexity Analysis Complete

### Summary
| Metric           | Value |
|------------------|-------|
| Tasks Analyzed   | 8     |
| Average Score    | 5.2   |
| High Complexity  | 3     |
| Medium Complexity| 3     |
| Low Complexity   | 2     |

---

### High Complexity Tasks (7+)

#### Task 3: Implement user authentication
| Factor      | Score | Notes                    |
|-------------|-------|--------------------------|
| Technical   | 8     | Security-critical        |
| Dependencies| 6     | Needs user model, config |
| Risk        | 8     | Auth is high-stakes      |
| Testing     | 7     | Needs security tests     |
| Integration | 7     | Middleware, routes       |

**Complexity Score: 7.5**
**Recommended Subtasks: 7**

Suggested breakdown:
1. JWT token infrastructure
2. User model with password hashing
3. Login endpoint
4. Logout endpoint
5. Token refresh mechanism
6. Auth middleware
7. Security tests
```

---

## Expand into Subtasks

Expand a task into detailed subtasks.

### Usage

```
/prd-taskmaster expand <task-id|all>
```

- `task-id`: Specific task to expand
- `all`: Expand all eligible tasks

### Process

1. **Parse Arguments**: Determine expansion mode
2. **Read tasks.json**: Load task database
3. **Find Tasks**: Locate tasks to expand
4. **Check Existing**: Verify task doesn't already have subtasks
5. **Analyze and Generate**: Create appropriate subtasks
6. **Update tasks.json**: Add subtasks to parent task
7. **Display Results**: Show created subtasks

### Subtask Generation Guidelines

- **Count**: 3-6 subtasks per task (adjust based on complexity)
- **Scope**: Each subtask completable in a focused work session
- **Order**: Subtasks follow logical sequence
- **Dependencies**: Earlier subtasks may be dependencies for later ones

**Generate for each subtask:**
- `id`: Parent ID + "." + sequential number (e.g., "3.1", "3.2")
- `title`: Clear, actionable title
- `description`: Brief description
- `status`: "pending"
- `dependencies`: Array of subtask IDs within same parent

### Example

Input task:
```json
{
  "id": 3,
  "title": "Implement user authentication",
  "description": "Build JWT-based auth with login, logout, and token refresh",
  "details": "Use bcrypt for passwords, JWT with 24h expiry..."
}
```

Generated subtasks:
```
| ID  | Title                      | Description                           | Deps |
|-----|----------------------------|---------------------------------------|------|
| 3.1 | Set up JWT infrastructure  | Token generation, validation, config  | -    |
| 3.2 | Create user model          | User schema with hashed password      | -    |
| 3.3 | Implement login endpoint   | POST /auth/login with validation      | 3.1,3.2 |
| 3.4 | Implement logout endpoint  | POST /auth/logout, invalidate token   | 3.1  |
| 3.5 | Add token refresh          | POST /auth/refresh for new tokens     | 3.1,3.3 |
| 3.6 | Create auth middleware     | Protect routes requiring auth         | 3.1  |
```

Output:
```
## Task Expanded

### Task 3: Implement user authentication

Expanded into 6 subtasks:

| ID   | Title                    | Dependencies |
|------|--------------------------|--------------|
| 3.1  | Set up JWT infrastructure| -            |
| 3.2  | Create user model        | -            |
| 3.3  | Implement login endpoint | 3.1, 3.2     |
| 3.4  | Implement logout endpoint| 3.1          |
| 3.5  | Add token refresh        | 3.1, 3.3     |
| 3.6  | Create auth middleware   | 3.1          |

### Next Steps
- Start with first subtask: Task 3.1
- Use /prd-taskmaster next to see full details
```

### Expand All Example

```
/prd-taskmaster expand all

## Expansion Results

### Tasks Expanded: 3

#### Task 2: Database setup
Expanded into 4 subtasks (2.1 - 2.4)

#### Task 3: User authentication
Expanded into 6 subtasks (3.1 - 3.6)

#### Task 5: Product API
Expanded into 5 subtasks (5.1 - 5.5)

### Tasks Skipped: 2
- Task 1: Already has subtasks
- Task 4: Marked as atomic (no expansion needed)

### Summary
- Original tasks: 5
- New subtasks: 15
- Total items: 20
```

## Workflow Recommendation

Use analyze before expand for guided subtask counts:

```
/prd-taskmaster analyze 3
# Review complexity score and recommendation
/prd-taskmaster expand 3
# Task expanded with appropriate subtask count
```
