# Task Operations

Common operations for managing tasks: add, update, complete, list, and next.

## Table of Contents

- [Add Task](#add-task)
- [Update Task](#update-task)
- [Complete Task](#complete-task)
- [List Tasks](#list-tasks)
- [Next Task](#next-task)

---

## Add Task

Add a new task with AI-generated details.

### Usage

```
/prd-taskmaster add <task description>
```

### Process

1. **Validate Input**: If no description provided, prompt for it
2. **Read tasks.json**: Load current task database
3. **Determine Next ID**: Find highest existing task ID and add 1
4. **Generate Task Details**:
   - **Title**: Clear, actionable (5-10 words), start with verb
   - **Description**: One-paragraph summary with context
   - **Priority**: high (core/blocking/security), medium (features/enhancements), low (nice-to-have/docs)
   - **Dependencies**: Analyze existing tasks to identify logical dependencies
   - **Details**: Technical approach, key considerations, edge cases
   - **Test Strategy**: Test types, key scenarios, acceptance criteria
5. **Create Task Object** with status "pending"
6. **Update tasks.json** and display created task

### Example

Input:
```
/prd-taskmaster add implement user profile page with avatar upload
```

Output:
```
## Task Created

### Task 5: Implement user profile page with avatar upload

| Field       | Value   |
|-------------|---------|
| ID          | 5       |
| Status      | pending |
| Priority    | medium  |
| Dependencies| 2, 3    |

### Description
Create a user profile page that displays user information and allows users to upload and manage their avatar image...

### Implementation Details
- Create ProfilePage component with form fields
- Implement avatar upload using presigned URLs
- Add image validation (size, format: jpg/png/webp)
- Include image cropping/resizing on client side
...
```

---

## Update Task

Update a task with notes or requirement changes.

### Usage

```
/prd-taskmaster update <task-id> [notes/changes]
```

### Process

1. **Parse Arguments**: Extract task ID and notes
2. **Read tasks.json**: Load task database
3. **Find Task**: Locate by ID (main task or subtask)
4. **Determine Update Type**:
   - Implementation notes → append to `details` with timestamp
   - Requirement changes → update description/details
   - Status changes → detect keywords ("blocked", "started", "done")
5. **Update Task Fields**: Append notes with timestamp, update `updatedAt`
6. **Write tasks.json** and display results

### Example

Input:
```
/prd-taskmaster update 3.2 Implemented login endpoint. Using bcrypt with cost factor 12. Added rate limiting of 5 attempts per 15 minutes.
```

Output:
```
## Task Updated

### Task 3.2: Implement login endpoint

**Updated Field**: details

### Current Details
[original details]

---
Update (2024-01-15 10:30):
Implemented login endpoint. Using bcrypt with cost factor 12.
Added rate limiting of 5 attempts per 15 minutes.
```

---

## Complete Task

Mark a task as complete with verification.

### Usage

```
/prd-taskmaster complete <task-id>
```

### Process

1. **Validate Task ID**: Parse and check format
2. **Read tasks.json**: Load task database
3. **Find Task**: Locate by ID
4. **Check Subtasks** (for parent tasks): Verify all subtasks are complete
5. **Run Verification** (optional): Suggest running tests
6. **Update Status**: Set to "done", add `completedAt` timestamp
7. **Write tasks.json**
8. **Check Parent**: If all sibling subtasks done, offer to complete parent
9. **Show Next Task**: Display next available task

### Example

Input:
```
/prd-taskmaster complete 3.2
```

Output:
```
## Task Completed

### Task 3.2: Implement login endpoint
Status: done
Completed: 2024-01-15T10:30:00Z

---

### Progress Update
- Completed: 5/12 tasks
- Remaining: 7 tasks
- Progress: 42%

### Next Available Task
Task 3.3: Implement logout endpoint
- Priority: high
- Dependencies: [3.1, 3.2] (complete)
```

---

## List Tasks

List all tasks with status overview.

### Usage

```
/prd-taskmaster list [filter]
```

- `filter` (optional): Status filter ("pending", "in-progress", "done", "blocked")

### Process

1. **Read tasks.json**: Load task database
2. **Parse and Filter**: Optionally filter by status
3. **Display Task List**: Format as table with subtasks indented
4. **Calculate Summary**: Count tasks by status
5. **Show Next Available**: Identify task ready to start
6. **Highlight Issues**: Report problems (blocked tasks, circular dependencies)

### Example

Output:
```
## Task List

| ID    | Title                      | Status      | Priority | Dependencies |
|-------|----------------------------|-------------|----------|--------------|
| 1     | Set up project config      | done        | high     | -            |
| 2     | Create database schema     | done        | high     | 1            |
| 3     | Implement authentication   | in-progress | high     | 1, 2         |
|  3.1  | JWT token generation       | done        | -        | -            |
|  3.2  | Login endpoint             | in-progress | -        | 3.1          |
|  3.3  | Password reset             | pending     | -        | 3.1, 3.2     |
| 4     | Build API endpoints        | pending     | medium   | 2, 3         |

## Summary
- Done: 3 | In Progress: 2 | Pending: 4
- Progress: 33% (3/9)

### Next Available
Task 3.3: Password reset (deps 3.1, 3.2 complete)
```

---

## Next Task

Find the next available task and show its details.

### Usage

```
/prd-taskmaster next
```

### Process

1. **Read tasks.json**: Load task database
2. **Build Completed Set**: Create set of all task IDs with status "done"
3. **Find Available Tasks**: Tasks with status "pending" and all dependencies "done"
4. **Sort by Priority**: high > medium > low, then by ID
5. **Select Next**: Take first task from sorted list
6. **Display Details**: Full task information with suggested first step

### Example

Output:
```
## Next Task: 3 - Implement user authentication

### Overview
| Field       | Value          |
|-------------|----------------|
| ID          | 3              |
| Status      | pending        |
| Priority    | high           |
| Dependencies| 1, 2 (done)    |

### Description
Build a complete JWT-based authentication system including login, logout, and token refresh capabilities.

### Implementation Details
- Use bcrypt for password hashing (cost factor 12)
- JWT tokens with 24h expiry
- Refresh tokens stored in httpOnly cookies
- Rate limiting on auth endpoints

### Test Strategy
- Unit tests for token generation/validation
- Integration tests for login flow
- Test invalid credentials handling
- Test token expiration

### Subtasks
| ID  | Title                | Status  |
|-----|----------------------|---------|
| 3.1 | JWT token service    | pending |
| 3.2 | Login endpoint       | pending |
| 3.3 | Logout endpoint      | pending |
| 3.4 | Token refresh        | pending |

---

### Suggested First Step
Start with subtask 3.1 (JWT token service) as other subtasks depend on it.

### Ready to Start
- Mark as in-progress when you begin
- Complete subtasks in order: 3.1 → 3.2 → 3.3 → 3.4
```

### No Available Tasks

```
## No Available Tasks

### Status
- Total tasks: 8
- Completed: 3
- In Progress: 1
- Blocked: 4

### Blocked Tasks
| ID | Title           | Waiting On        |
|----|-----------------|-------------------|
| 4  | API endpoints   | 3 (in-progress)   |
| 5  | Frontend        | 4 (pending)       |

### Suggestion
Complete task 3 (in-progress) to unblock task 4.
```
