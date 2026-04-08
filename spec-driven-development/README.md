# Spec Driven Development - Complete Guide

> **Your Learning Path**: Principles → Practical Implementation → Specification Quality → Validation

---

## 🎯 What Is Spec Driven Development?

Imagine you're building a user authentication system. Usually, you'd start coding immediately:

**The old way:**

```typescript
// Just start coding...
function login(email, password) {
  // Um... what should this do?
  // What if email is invalid?
  // What if password is wrong?
  // What about rate limiting?
  // Write code first, figure out requirements later... 🤔
}
```

**Spec Driven Development way:**

```typescript
// First, write a SPECIFICATION
/**
 * SPECIFICATION: User Authentication System
 *
 * Goal: Allow users to securely log into the system
 *
 * Requirements:
 * - Accept email and password
 * - Validate email format
 * - Check password against database
 * - Return JWT token on success
 * - Return error message on failure
 * - Implement rate limiting (5 attempts per minute)
 * - Log all login attempts
 *
 * Success Criteria:
 * - ✅ Valid user logs in successfully
 * - ✅ Invalid credentials are rejected
 * - ✅ Rate limiting prevents brute force
 * - ✅ All attempts are logged
 */

// NOW write the code based on the spec
async function login(email: string, password: string): Promise<LoginResult> {
  // Implementation follows the specification
}
```

**Spec Driven Development (SDD) is a methodology where you write detailed specifications BEFORE writing code** - defining what the system should do, how it should behave, and what success looks like.

Think of it as: **Write the blueprint before building the house.**

---

## 💡 Why Spec Driven Development? (The Problem It Solves)

### The Problem Without Specifications

```typescript
// Developer 1's understanding:
// "Build a search feature"
function search(query) {
  // Just search through items...
  return items.filter((item) => item.includes(query));
}

// Product Manager's expectation:
// Search should:
// - Be case-insensitive
// - Support fuzzy matching
// - Filter by category
// - Sort by relevance
// - Paginate results
// - Log search queries for analytics

// Result: Miscommunication, rework, wasted time! 😱
```

**Problems:**

1. **Unclear Requirements** - Everyone has different expectations
2. **Scope Creep** - Features keep getting added mid-development
3. **Missing Edge Cases** - Error scenarios discovered too late
4. **No Success Criteria** - How do you know when it's "done"?
5. **Difficult to Test** - No clear definition of correct behavior
6. **Poor Documentation** - Code is the only documentation

### With Spec Driven Development

```typescript
/**
 * SPECIFICATION: Product Search Feature
 *
 * Context: Users need to find products quickly in our e-commerce platform
 *
 * Functional Requirements:
 * 1. Accept search query (string, 1-100 characters)
 * 2. Case-insensitive matching
 * 3. Support fuzzy matching (Levenshtein distance ≤ 2)
 * 4. Optional category filter
 * 5. Sort by relevance score (weighted: name 60%, description 40%)
 * 6. Paginate results (20 per page)
 *
 * Non-Functional Requirements:
 * 1. Response time < 200ms for 95th percentile
 * 2. Handle 1000 concurrent searches
 * 3. Log all queries for analytics
 *
 * Edge Cases:
 * - Empty query → return recent/popular products
 * - No results → suggest similar queries
 * - Special characters → sanitize input
 *
 * Success Criteria:
 * - ✅ All functional requirements met
 * - ✅ Performance targets achieved
 * - ✅ 95% user satisfaction in testing
 */

// Now everyone is on the same page!
function search(params: SearchParams): SearchResult {
  // Implementation is clear
}
```

**Benefits:**

1. ✅ **Clear Communication** - Everyone understands the requirements
2. ✅ **Reduced Rework** - Catch issues in planning phase
3. ✅ **Better Testing** - Specs define expected behavior
4. ✅ **Living Documentation** - Specs evolve with the code
5. ✅ **Faster Onboarding** - New developers understand the system
6. ✅ **Quality Assurance** - Clear success criteria

---

## 🏗️ SpecKit: Your Spec Driven Development Tool

SpecKit is a tool that helps you practice Spec Driven Development by providing AI-assisted specification writing and validation.

### Installation & Setup

```bash
# SpecKit uses uvx (Python package runner)
# For NEW projects:
uvx --from git+https://github.com/github/spec-kit.git specify init <PROJECT_NAME>

# For EXISTING projects:
uvx --from git+https://github.com/github/spec-kit.git specify init --here
```

### The Complete SpecKit Command Flow

| Command                 | Purpose                            | When to Use                     |
| ----------------------- | ---------------------------------- | ------------------------------- |
| `specify init`          | Initialize SpecKit in project      | **First step** - Project setup  |
| `/speckit.constitution` | Set project principles & standards | After init - Define standards   |
| `/speckit.specify`      | Write detailed specifications      | Before implementing features    |
| `/speckit.validate`     | Check if spec is complete          | After writing specs             |
| `/speckit.clarify`      | Clarify ambiguities in spec        | After validation, before coding |
| `/speckit.plan`         | Create implementation plan         | After clarification             |
| `/speckit.analysis`     | Analyze dependencies & risks       | Before implementation           |
| `/speckit.implement`    | Generate code from specs           | After planning & analysis       |
| `/speckit.review`       | Review implementation against spec | After coding                    |
| `/speckit.refine`       | Improve existing specs             | When requirements change        |

---

## 🚀 Step-by-Step Guide: Using SpecKit

Let's build a complete feature using Spec Driven Development!

### Step 0: `specify init` - Initialize Your Project

**What it does:** Sets up SpecKit in your project, creates configuration files, and initializes the specification structure.

**When to use:** The very first step when starting a new project or adding SpecKit to an existing one.

**For a New Project:**

```bash
# Navigate to where you want your project
cd ~/projects

# Initialize new project with SpecKit
uvx --from git+https://github.com/github/spec-kit.git specify init TaskFlowApp

# What this creates:
# TaskFlowApp/
# ├── .speckit/
# │   ├── config.yaml
# │   └── templates/
# ├── specs/
# │   └── README.md
# ├── src/
# └── README.md
```

**For an Existing Project:**

```bash
# Navigate to your existing project
cd ~/projects/my-existing-app

# Initialize SpecKit here
uvx --from git+https://github.com/github/spec-kit.git specify init --here

# What this adds:
# my-existing-app/
# ├── .speckit/           # ← Added
# │   ├── config.yaml     # ← Added
# │   └── templates/      # ← Added
# ├── specs/              # ← Added
# │   └── README.md       # ← Added
# ├── src/                # ← Your existing code
# └── package.json        # ← Your existing files
```

**Output: Configuration File (.speckit/config.yaml)**

```yaml
# SpecKit Configuration
project:
  name: TaskFlowApp
  version: 1.0.0
  created: 2026-04-08

specifications:
  directory: specs/
  template: templates/default.md
  versioning: true

standards:
  # Will be populated by /speckit.constitution
  constitution: null

workflow:
  require_validation: true
  require_review: true
  auto_version: true

integrations:
  git: true
  github: false
  jira: false
```

**Why this matters:**

- ✅ Sets up proper directory structure
- ✅ Configures SpecKit for your project
- ✅ Creates spec template system
- ✅ Enables version tracking
- ✅ Integrates with version control

---

### Step 1: `/speckit.constitution` - Set Project Standards

**What it does:** Defines the principles, standards, and guidelines for your entire project.

**When to use:** At the very start of a project or when establishing coding standards.

**Example:**

```typescript
// Command in your AI assistant:
/speckit.constitution

// Input your project context:
"We're building a task management application with React, TypeScript, and Node.js.
Focus on security, performance, and user experience."
```

**Output: Project Constitution**

```markdown
# Task Management Application - Constitution

## Core Principles

1. **Security First**
   - All user data encrypted at rest and in transit
   - JWT-based authentication
   - Role-based access control (RBAC)
   - Input validation on all endpoints

2. **Performance**
   - API responses < 200ms (95th percentile)
   - Client-side bundle < 500KB
   - Lazy loading for non-critical features
   - Database queries optimized with indexes

3. **User Experience**
   - Mobile-responsive design
   - Accessibility (WCAG 2.1 AA)
   - Intuitive navigation
   - Clear error messages

4. **Code Quality**
   - TypeScript strict mode
   - 80% test coverage minimum
   - ESLint + Prettier
   - Code reviews required

## Specification Standards

Every spec must include:

- Clear problem statement
- Functional requirements (numbered)
- Non-functional requirements
- Edge cases
- Success criteria
- API contracts (if applicable)

## Tech Stack

**Frontend:** React 18, TypeScript, TailwindCSS
**Backend:** Node.js, Express, PostgreSQL
**Testing:** Jest, React Testing Library, Supertest
**Deployment:** Docker, AWS ECS
```

**Why this matters:**

- ✅ Everyone follows same standards
- ✅ Specs are consistent across project
- ✅ Quality gates are clear
- ✅ Technical decisions are documented

---

### Step 2: `/speckit.specify` - Write Detailed Specifications

**What it does:** Creates comprehensive specifications for a specific feature or component.

**When to use:** Before writing ANY code for a new feature.

**Example:**

```typescript
// Command:
/speckit.specify

// Input:
"Create a specification for a task creation feature where users can add new tasks
with title, description, due date, and priority level."
```

**Output: Detailed Specification**

````markdown
# SPECIFICATION: Task Creation Feature

## 1. Problem Statement

Users need a quick and intuitive way to create new tasks in the task management system
with essential details to organize their work effectively.

## 2. Context

- Target users: Teams and individuals managing projects
- Current state: No task creation mechanism exists (new feature)
- Expected usage: 50-100 task creations per user per day

## 3. Functional Requirements

### FR-1: Task Form

The system shall provide a form with the following fields:

- **Title** (required)
  - Type: Text input
  - Max length: 200 characters
  - Validation: Non-empty, trimmed
- **Description** (optional)
  - Type: Textarea
  - Max length: 2000 characters
  - Supports: Plain text (no markdown in v1)
- **Due Date** (optional)
  - Type: Date picker
  - Validation: Cannot be in the past
  - Default: None (no due date)
- **Priority** (required)
  - Type: Dropdown
  - Options: Low, Medium, High, Urgent
  - Default: Medium

### FR-2: Task Submission

When user clicks "Create Task":

1. Validate all fields
2. Show validation errors if any
3. Call POST /api/tasks endpoint
4. Show loading state during submission
5. On success: Show success message, clear form, redirect to task list
6. On error: Show error message, keep form data

### FR-3: Auto-save Draft

- Save form data to localStorage every 5 seconds
- Restore draft on next visit
- Clear draft after successful submission
- Show "Draft saved" indicator

## 4. Non-Functional Requirements

### NFR-1: Performance

- Form renders in < 100ms
- Auto-save doesn't block UI
- Task creation API response < 300ms

### NFR-2: Accessibility

- Keyboard navigation support (Tab, Enter, Escape)
- ARIA labels on all form fields
- Screen reader compatible
- Focus management (auto-focus title on open)

### NFR-3: User Experience

- Inline validation (show errors on blur)
- Character counters for title and description
- Confirm before discarding unsaved changes
- Mobile-responsive layout

## 5. API Contract

### POST /api/tasks

**Request:**

```json
{
  "title": "Complete project proposal",
  "description": "Draft and review the Q2 project proposal document",
  "dueDate": "2026-04-15T00:00:00Z",
  "priority": "high"
}
```
````

**Response (201 Created):**

```json
{
  "id": "task_abc123",
  "title": "Complete project proposal",
  "description": "Draft and review the Q2 project proposal document",
  "dueDate": "2026-04-15T00:00:00Z",
  "priority": "high",
  "status": "todo",
  "createdAt": "2026-04-08T10:30:00Z",
  "createdBy": "user_xyz789"
}
```

**Error Response (400 Bad Request):**

```json
{
  "error": "VALIDATION_ERROR",
  "message": "Invalid task data",
  "details": [
    {
      "field": "title",
      "error": "Title is required"
    }
  ]
}
```

## 6. Edge Cases

| Scenario             | Expected Behavior                                |
| -------------------- | ------------------------------------------------ |
| Empty title          | Show error: "Title is required"                  |
| Title > 200 chars    | Prevent input, show character count in red       |
| Past due date        | Show error: "Due date cannot be in the past"     |
| Network error        | Show: "Failed to create task. Please try again." |
| Duplicate submission | Prevent with loading state + button disable      |
| Browser back button  | Prompt: "Discard unsaved changes?"               |
| Slow network         | Show loading spinner, timeout after 30s          |

## 7. Success Criteria

- ✅ User can create task with all fields in < 30 seconds
- ✅ Validation errors are clear and actionable
- ✅ No data loss (draft auto-save works)
- ✅ Mobile users can create tasks easily
- ✅ Screen reader users can navigate form
- ✅ 90% task creation success rate (no errors)
- ✅ Zero data validation bugs in production

## 8. Testing Requirements

### Unit Tests

- Form validation logic
- Auto-save mechanism
- API client functions

### Integration Tests

- Full form submission flow
- Error handling scenarios
- Draft persistence

### E2E Tests

- Create task happy path
- Validation error scenarios
- Mobile responsiveness

## 9. Dependencies

- React Hook Form (form management)
- Zod (validation schema)
- date-fns (date handling)
- React Query (API calls)

## 10. Acceptance Criteria

**Given** I am a logged-in user
**When** I navigate to the task creation page
**Then** I should see an empty form with all fields

**Given** I fill in all required fields correctly
**When** I click "Create Task"
**Then** The task should be created and I should see it in my task list

**Given** I enter invalid data
**When** I try to submit the form
**Then** I should see clear validation errors and the form should not submit

````

**Why this matters:**
- ✅ Complete picture before coding
- ✅ API contract defined (frontend + backend aligned)
- ✅ Edge cases identified early
- ✅ Testing strategy clear
- ✅ Success criteria measurable

---

### Step 3: `/speckit.validate` - Check Specification Quality

**What it does:** Analyzes your specification for completeness, clarity, and quality.

**When to use:** After writing a spec, before starting implementation.

**Example:**

```typescript
// Command:
/speckit.validate

// Input: Paste your specification
````

**Output: Validation Report**

```markdown
# Specification Validation Report

## ✅ Strengths

1. **Clear Success Criteria** - Measurable and testable
2. **Complete API Contract** - Request/response well-defined
3. **Edge Cases Covered** - 7 scenarios identified
4. **Accessibility Considered** - WCAG compliance mentioned

## ⚠️ Issues Found

### 🔴 Critical Issues

1. **Missing Error Recovery**
   - Location: Section 3.2 (Task Submission)
   - Issue: What happens if localStorage is full? (auto-save)
   - Recommendation: Add fallback behavior or error handling

2. **Unclear Performance Budget**
   - Location: Section 4.1 (Performance)
   - Issue: "< 100ms render" - on what hardware? Network conditions?
   - Recommendation: Specify baseline (e.g., "on 4G network, mid-tier device")

### 🟡 Moderate Issues

3. **Incomplete Security Requirements**
   - Location: Missing section
   - Issue: No mention of CSRF protection, XSS prevention
   - Recommendation: Add security requirements section

4. **Vague User Feedback**
   - Location: Section 3.2
   - Issue: "Show success message" - what message? Where? How long?
   - Recommendation: Specify exact message and display duration

### 🟢 Minor Suggestions

5. **Add Diagrams**
   - Consider adding: User flow diagram, state diagram for form
   - Makes spec easier to understand visually

6. **Version History**
   - Add: Spec version number, author, last updated date
   - Helps track changes over time

## 📊 Specification Score: 82/100

| Category     | Score  | Status        |
| ------------ | ------ | ------------- |
| Completeness | 85/100 | ✅ Good       |
| Clarity      | 90/100 | ✅ Excellent  |
| Testability  | 80/100 | ✅ Good       |
| Security     | 60/100 | ⚠️ Needs Work |
| Performance  | 75/100 | ✅ Good       |

## 🎯 Next Steps

1. Add security requirements section
2. Clarify error recovery for auto-save
3. Specify exact user feedback messages
4. Add performance baseline context
5. Consider adding user flow diagram

**Recommendation:** Address critical issues before implementation.
```

**Why this matters:**

- ✅ Catches missing requirements early
- ✅ Improves spec quality
- ✅ Reduces surprises during coding
- ✅ Saves time and rework

---

### Step 4: `/speckit.clarify` - Clarify Ambiguities

**What it does:** Identifies ambiguous or unclear parts of your specification and helps you clarify them through interactive questioning.

**When to use:** After validation, when spec has vague requirements or when stakeholders have questions.

**Example:**

```typescript
// Command:
/speckit.clarify

// Input: Your specification (especially sections marked as unclear by validate)
```

**Output: Clarification Questions & Answers**

````markdown
# Specification Clarification Report

## 🤔 Ambiguities Found

### 1. Performance Requirement Clarification

**Ambiguous Statement:**
"Form renders in < 100ms"

**Questions:**
Q: On what device/network conditions?
Q: Does this include API calls or just UI rendering?
Q: What is the measurement point (start of render to fully interactive)?

**Recommended Clarification:**
"Form UI renders in < 100ms (measured from component mount to paint)
on mid-tier device (4-core CPU, 8GB RAM) without network latency.
API calls measured separately."

### 2. Auto-Save Behavior Clarification

**Ambiguous Statement:**
"Save form data to localStorage every 5 seconds"

**Questions:**
Q: What happens if user is typing during the 5-second mark?
Q: Should we debounce to avoid interrupting user input?
Q: What if localStorage quota is exceeded?

**Recommended Clarification:**
"Debounce auto-save: trigger 5 seconds after user stops typing.
If localStorage full, show warning toast and disable auto-save for session.
Don't interrupt user during active input."

### 3. Success Message Unclear

**Ambiguous Statement:**
"Show success message"

**Questions:**
Q: What exact message should be shown?
Q: Where on the screen (toast, inline, modal)?
Q: How long should it display?
Q: Should it auto-dismiss or require user action?

**Recommended Clarification:**
"Display green toast notification at top-right:
'✓ Task created successfully'
Auto-dismiss after 3 seconds
Optional: Include 'View Task' action button"

### 4. Error Handling Scope

**Ambiguous Statement:**
"Handle errors gracefully"

**Questions:**
Q: What types of errors (validation, network, server, client)?
Q: Should we retry automatically?
Q: How to handle partial failures?
Q: What error messages to show users?

**Recommended Clarification:**
"Error types and handling:

- Validation errors: Show inline on field with red text
- Network errors: Show retry button, auto-retry after 2s (max 3 times)
- Server errors (500): Show 'Something went wrong. Please try again later.'
- Client errors (400): Show specific error message from API"

## 📝 Updated Specification Requirements

Based on clarifications, update your spec:

**Section 4.1 - Performance (Updated):**

```markdown
NFR-1: Performance

- Form UI renders in < 100ms on mid-tier device (without API latency)
- Auto-save triggers 5s after user stops typing (debounced)
- Task creation API response < 300ms (95th percentile)
```
````

**Section 3.2 - Task Submission (Updated):**

```markdown
On success:

- Show toast: "✓ Task created successfully" (top-right, green)
- Auto-dismiss after 3 seconds
- Optional: "View Task" button in toast
- Clear form fields
- Navigate to task list after toast dismisses
```

## ✅ Next Steps

1. Update specification with clarified requirements
2. Get stakeholder sign-off on clarifications
3. Re-run /speckit.validate
4. Proceed to /speckit.plan

````

**Why this matters:**
- ✅ Eliminates vague requirements
- ✅ Prevents misinterpretation
- ✅ Aligns team understanding
- ✅ Reduces back-and-forth during implementation

---

### Step 5: `/speckit.plan` - Create Implementation Plan

**What it does:** Generates a detailed implementation plan with tasks, dependencies, and timeline based on your specification.

**When to use:** After clarification, before starting to code.

**Example:**

```typescript
// Command:
/speckit.plan

// Input: Your clarified specification
````

**Output: Implementation Plan**

```markdown
# Implementation Plan: Task Creation Feature

## 📊 Project Overview

**Estimated Effort:** 5 days
**Complexity:** Medium
**Risk Level:** Low

## 🎯 Implementation Phases

### Phase 1: Foundation (Day 1)

**Tasks:**

1. Set up form component structure
2. Install dependencies (React Hook Form, Zod, React Query)
3. Create TypeScript types/interfaces
4. Set up validation schemas

**Deliverables:**

- Basic form component shell
- Type definitions
- Validation rules configured

**Dependencies:** None

### Phase 2: Core Functionality (Days 2-3)

**Tasks:** 4. Implement form fields (title, description, due date, priority) 5. Add client-side validation 6. Implement auto-save to localStorage 7. Create API client function 8. Set up React Query mutation

**Deliverables:**

- Working form with all fields
- Real-time validation
- Auto-save feature
- API integration

**Dependencies:** Phase 1 complete

### Phase 3: Polish & Edge Cases (Day 4)

**Tasks:** 9. Add character counters 10. Implement loading states 11. Add error handling (network, validation, server) 12. Add success/error notifications 13. Implement draft restoration on mount 14. Add keyboard shortcuts

**Deliverables:**

- Polished user experience
- All edge cases handled
- Accessibility features

**Dependencies:** Phase 2 complete

### Phase 4: Testing & Review (Day 5)

**Tasks:** 15. Write unit tests (validation, auto-save) 16. Write integration tests (form submission) 17. Write E2E tests (full user flow) 18. Code review 19. Performance testing 20. Documentation

**Deliverables:**

- 80%+ test coverage
- Performance verified
- Documentation updated

**Dependencies:** Phase 3 complete

## 📋 Task Breakdown

| Task                    | Effort | Priority | Assignee | Status     |
| ----------------------- | ------ | -------- | -------- | ---------- |
| 1. Component structure  | 2h     | High     | Dev 1    | 🔄 Pending |
| 2. Install dependencies | 0.5h   | High     | Dev 1    | 🔄 Pending |
| 3. Type definitions     | 1h     | High     | Dev 1    | 🔄 Pending |
| 4. Form fields          | 4h     | High     | Dev 1    | 🔄 Pending |
| 5. Validation           | 3h     | High     | Dev 1    | 🔄 Pending |
| 6. Auto-save            | 2h     | Medium   | Dev 2    | 🔄 Pending |
| 7. API client           | 2h     | High     | Dev 2    | 🔄 Pending |
| 8. React Query setup    | 1h     | High     | Dev 2    | 🔄 Pending |
| 9. Character counters   | 1h     | Low      | Dev 1    | 🔄 Pending |
| 10. Loading states      | 2h     | Medium   | Dev 1    | 🔄 Pending |
| 11. Error handling      | 3h     | High     | Dev 2    | 🔄 Pending |
| 12. Notifications       | 2h     | Medium   | Dev 2    | 🔄 Pending |
| 13-20. Testing/Review   | 8h     | High     | Both     | 🔄 Pending |

## 🔗 Dependencies & Risks

**External Dependencies:**

- React Hook Form (v7+)
- Zod (v3+)
- React Query (v5+)
- date-fns (v2+)

**API Dependencies:**

- POST /api/tasks endpoint (backend team)
- User authentication middleware

**Risks:**

1. ⚠️ **Medium Risk:** Backend API not ready in time
   - Mitigation: Mock API responses, parallel development

2. ⚠️ **Low Risk:** localStorage quota exceeded
   - Mitigation: Handle QuotaExceededError, show user warning

3. ⚠️ **Low Risk:** Auto-save performance on slow devices
   - Mitigation: Debounce properly, test on low-end devices

## 📈 Progress Tracking
```

Day 1: □□□□□□□□□□ 0% (Foundation)
Day 2: □□□□□□□□□□ 0% (Core - Part 1)
Day 3: □□□□□□□□□□ 0% (Core - Part 2)
Day 4: □□□□□□□□□□ 0% (Polish)
Day 5: □□□□□□□□□□ 0% (Testing)

```

## ✅ Definition of Done

- [ ] All functional requirements implemented
- [ ] All edge cases handled
- [ ] Unit test coverage > 80%
- [ ] Integration tests passing
- [ ] E2E tests passing
- [ ] Code review approved
- [ ] Performance targets met
- [ ] Accessibility verified (WCAG 2.1 AA)
- [ ] Documentation updated
- [ ] Deployed to staging
```

**Why this matters:**

- ✅ Clear roadmap for implementation
- ✅ Effort estimation for scheduling
- ✅ Task dependencies identified
- ✅ Risks identified upfront
- ✅ Progress trackable

---

### Step 6: `/speckit.analysis` - Analyze Dependencies & Risks

**What it does:** Performs deep analysis of technical dependencies, potential risks, and architectural considerations.

**When to use:** After planning, before implementation (especially for complex features).

**Example:**

```typescript
// Command:
/speckit.analysis

// Input: Your specification + implementation plan
```

**Output: Technical Analysis Report**

````markdown
# Technical Analysis: Task Creation Feature

## 🔍 Dependency Analysis

### Frontend Dependencies

**Required Packages:**

```json
{
  "react-hook-form": "^7.50.0", // Form management
  "zod": "^3.22.0", // Validation
  "@tanstack/react-query": "^5.25.0", // API calls
  "date-fns": "^2.30.0" // Date handling
}
```
````

**Size Impact:**

- react-hook-form: ~40KB
- zod: ~35KB
- react-query: ~45KB
- date-fns: ~60KB
- **Total: ~180KB** (gzipped)
- ✅ Within 500KB budget

**Version Compatibility:**

- ✅ All compatible with React 18
- ✅ No peer dependency conflicts
- ⚠️ Zod requires TypeScript 4.5+

### Backend Dependencies

**API Endpoint Required:**

```typescript
POST /api/tasks
- Must support: JSON body parsing
- Must return: 201 Created with task object
- Must handle: Validation errors (400), Auth (401), Server errors (500)
```

**Database Schema:**

```sql
CREATE TABLE tasks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(200) NOT NULL,
  description TEXT,
  due_date TIMESTAMP,
  priority VARCHAR(20) NOT NULL CHECK (priority IN ('low', 'medium', 'high', 'urgent')),
  status VARCHAR(20) NOT NULL DEFAULT 'todo',
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  created_by UUID NOT NULL REFERENCES users(id)
);

CREATE INDEX idx_tasks_created_by ON tasks(created_by);
CREATE INDEX idx_tasks_due_date ON tasks(due_date) WHERE due_date IS NOT NULL;
```

## ⚠️ Risk Assessment

### High Priority Risks

**Risk 1: Backend API Not Ready**

- **Likelihood:** Medium
- **Impact:** High (blocks frontend development)
- **Mitigation:**

  ```typescript
  // Use MSW (Mock Service Worker) for development
  import { rest } from "msw";

  export const handlers = [
    rest.post("/api/tasks", (req, res, ctx) => {
      return res(ctx.status(201), ctx.json({ id: "mock-id", ...req.body }));
    }),
  ];
  ```

**Risk 2: localStorage Quota Exceeded**

- **Likelihood:** Low
- **Impact:** Medium (auto-save breaks)
- **Mitigation:**
  ```typescript
  try {
    localStorage.setItem("taskDraft", JSON.stringify(data));
  } catch (error) {
    if (error.name === "QuotaExceededError") {
      // Clear old drafts or show warning
      showWarning("Storage full. Auto-save disabled.");
      disableAutoSave();
    }
  }
  ```

### Medium Priority Risks

**Risk 3: Form Performance on Low-End Devices**

- **Likelihood:** Medium
- **Impact:** Medium (poor UX)
- **Mitigation:**
  - Debounce validation (300ms)
  - Use React.memo for form fields
  - Lazy load date picker component

**Risk 4: Network Reliability**

- **Likelihood:** High (users on mobile)
- **Impact:** Medium (failed submissions)
- **Mitigation:**
  - Implement retry logic (3 attempts)
  - Show clear error messages
  - Preserve draft on failure

## 🏗️ Architecture Considerations

### State Management Strategy

```typescript
// Recommended: Local component state + React Query
// ✅ Pros: Simple, performant, built-in caching
// ❌ Avoid: Redux (overkill for single form)

function TaskCreationForm() {
  // Form state (React Hook Form)
  const { register, handleSubmit } = useForm();

  // Server state (React Query)
  const mutation = useMutation({
    mutationFn: createTask,
  });

  // UI state (local)
  const [draftSaved, setDraftSaved] = useState(false);
}
```

### Error Boundary Strategy

```typescript
// Wrap component in error boundary
<ErrorBoundary
  fallback={<TaskFormError />}
  onError={(error) => logError(error)}
>
  <TaskCreationForm />
</ErrorBoundary>
```

### Testing Strategy

**Unit Tests (70% coverage):**

- Validation logic
- Auto-save mechanism
- Form field rendering

**Integration Tests (20% coverage):**

- Form submission flow
- Error handling
- API integration

**E2E Tests (10% coverage)**:

- Critical user path: Create task successfully

## 📊 Performance Impact

**Bundle Size:**

- Current: 420KB
- After feature: 600KB
- Impact: +180KB (+43%)
- ✅ Still under 1MB budget

**Runtime Performance:**

- Form render: ~50ms (measured)
- Validation: ~10ms per field
- Auto-save: ~5ms (localStorage write)
- ✅ All within targets

**API Impact:**

- Expected: 100 task creations/day per user
- Database writes: +100/day per user
- Storage: ~0.5KB per task
- ✅ Negligible impact

## 🔐 Security Considerations

**Input Validation:**

```typescript
// Client-side (Zod)
const schema = z.object({
  title: z.string().max(200).trim(),
  description: z.string().max(2000).optional(),
});

// Server-side (must also validate!)
// Never trust client validation alone
```

**XSS Prevention:**

- ✅ React escapes content by default
- ⚠️ If using dangerouslySetInnerHTML → sanitize!

**CSRF Protection:**

```typescript
// Add CSRF token to requests
fetch("/api/tasks", {
  method: "POST",
  headers: {
    "X-CSRF-Token": getCsrfToken(),
  },
});
```

## ✅ Recommendations

1. **Use MSW for API mocking** - Don't block on backend
2. **Implement error boundaries** - Graceful failure handling
3. **Add performance monitoring** - Track form render times
4. **Set up E2E tests early** - Catch integration issues
5. **Load test API endpoint** - Verify 1000 concurrent users
6. **Add analytics tracking** - Measure user behavior

## 🚦 Go/No-Go Decision

**✅ GREEN - Proceed with Implementation**

Reasoning:

- All critical dependencies available
- Risks are mitigated
- Performance impact acceptable
- Security considerations addressed
- Team has required expertise

````

**Why this matters:**
- ✅ Technical risks identified early
- ✅ Dependencies verified and planned
- ✅ Architecture decisions documented
- ✅ Security considerations addressed
- ✅ Performance impact understood

---

### Step 7: `/speckit.implement` - Generate Code from Spec

**What it does:** Generates implementation code based on your validated specification.

**When to use:** After spec validation, ready to code.

**Example:**

```typescript
// Command:
/speckit.implement

// Input: Your validated specification
````

**Output: Implementation Code**

```typescript
// ========================================
// GENERATED FROM SPECIFICATION
// Feature: Task Creation
// Spec Version: 1.0.0
// Generated: 2026-04-08
// ========================================

// 1. TYPE DEFINITIONS
// Based on API Contract (Section 5)

interface Task {
  id: string;
  title: string;
  description?: string;
  dueDate?: Date;
  priority: 'low' | 'medium' | 'high' | 'urgent';
  status: 'todo' | 'in_progress' | 'done';
  createdAt: Date;
  createdBy: string;
}

interface CreateTaskInput {
  title: string;
  description?: string;
  dueDate?: Date;
  priority: Task['priority'];
}

interface ValidationError {
  field: string;
  error: string;
}

// 2. VALIDATION SCHEMA
// Based on Functional Requirements (Section 3.1)

import { z } from 'zod';

const createTaskSchema = z.object({
  title: z.string()
    .min(1, 'Title is required')
    .max(200, 'Title must be 200 characters or less')
    .transform(str => str.trim()),

  description: z.string()
    .max(2000, 'Description must be 2000 characters or less')
    .optional(),

  dueDate: z.date()
    .refine(
      date => date >= new Date(),
      'Due date cannot be in the past'
    )
    .optional(),

  priority: z.enum(['low', 'medium', 'high', 'urgent'])
    .default('medium')
});

// 3. REACT COMPONENT
// Based on Functional & Non-Functional Requirements

import { useState, useEffect } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { useMutation } from '@tanstack/react-query';

export function TaskCreationForm() {
  const [draftSaved, setDraftSaved] = useState(false);

  // Form setup with validation (FR-1)
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors, isSubmitting },
    reset
  } = useForm<CreateTaskInput>({
    resolver: zodResolver(createTaskSchema),
    defaultValues: {
      title: '',
      description: '',
      priority: 'medium'
    }
  });

  const formData = watch();

  // Auto-save draft (FR-3)
  useEffect(() => {
    const timer = setTimeout(() => {
      localStorage.setItem('taskDraft', JSON.stringify(formData));
      setDraftSaved(true);
      setTimeout(() => setDraftSaved(false), 2000);
    }, 5000);

    return () => clearTimeout(timer);
  }, [formData]);

  // Restore draft on mount (FR-3)
  useEffect(() => {
    const draft = localStorage.getItem('taskDraft');
    if (draft) {
      try {
        const parsed = JSON.parse(draft);
        reset(parsed);
      } catch (error) {
        console.error('Failed to restore draft:', error);
        // Handle localStorage corruption (Validation feedback)
        localStorage.removeItem('taskDraft');
      }
    }
  }, [reset]);

  // API mutation (FR-2)
  const createTaskMutation = useMutation({
    mutationFn: async (data: CreateTaskInput) => {
      const response = await fetch('/api/tasks', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      });

      if (!response.ok) {
        const error = await response.json();
        throw new Error(error.message);
      }

      return response.json();
    },
    onSuccess: () => {
      // Clear draft (FR-3)
      localStorage.removeItem('taskDraft');
      // Show success message (FR-2)
      alert('Task created successfully!');
      // Clear form (FR-2)
      reset();
      // Redirect to task list (FR-2)
      window.location.href = '/tasks';
    },
    onError: (error) => {
      // Show error message (FR-2)
      alert(`Failed to create task: ${error.message}`);
    }
  });

  // Submit handler (FR-2)
  const onSubmit = handleSubmit((data) => {
    createTaskMutation.mutate(data);
  });

  // Warn on unsaved changes (Edge Case)
  useEffect(() => {
    const handleBeforeUnload = (e: BeforeUnloadEvent) => {
      if (formData.title || formData.description) {
        e.preventDefault();
        e.returnValue = '';
      }
    };

    window.addEventListener('beforeunload', handleBeforeUnload);
    return () => window.removeEventListener('beforeunload', handleBeforeUnload);
  }, [formData]);

  return (
    <form onSubmit={onSubmit} className="max-w-2xl mx-auto p-6">
      <h1 className="text-2xl font-bold mb-6">Create New Task</h1>

      {/* Draft saved indicator (FR-3) */}
      {draftSaved && (
        <div className="mb-4 text-sm text-green-600">
          ✓ Draft saved
        </div>
      )}

      {/* Title field (FR-1) */}
      <div className="mb-4">
        <label
          htmlFor="title"
          className="block text-sm font-medium mb-2"
        >
          Title <span className="text-red-500">*</span>
        </label>
        <input
          id="title"
          type="text"
          {...register('title')}
          className="w-full px-3 py-2 border rounded"
          maxLength={200}
          aria-required="true"
          aria-invalid={!!errors.title}
          aria-describedby={errors.title ? 'title-error' : undefined}
        />
        {errors.title && (
          <p id="title-error" className="mt-1 text-sm text-red-600">
            {errors.title.message}
          </p>
        )}
        <p className="mt-1 text-sm text-gray-500">
          {formData.title?.length || 0}/200 characters
        </p>
      </div>

      {/* Description field (FR-1) */}
      <div className="mb-4">
        <label
          htmlFor="description"
          className="block text-sm font-medium mb-2"
        >
          Description
        </label>
        <textarea
          id="description"
          {...register('description')}
          className="w-full px-3 py-2 border rounded"
          rows={4}
          maxLength={2000}
          aria-describedby="description-help"
        />
        <p id="description-help" className="mt-1 text-sm text-gray-500">
          {formData.description?.length || 0}/2000 characters
        </p>
      </div>

      {/* Due Date field (FR-1) */}
      <div className="mb-4">
        <label
          htmlFor="dueDate"
          className="block text-sm font-medium mb-2"
        >
          Due Date
        </label>
        <input
          id="dueDate"
          type="date"
          {...register('dueDate', { valueAsDate: true })}
          min={new Date().toISOString().split('T')[0]}
          className="w-full px-3 py-2 border rounded"
          aria-invalid={!!errors.dueDate}
        />
        {errors.dueDate && (
          <p className="mt-1 text-sm text-red-600">
            {errors.dueDate.message}
          </p>
        )}
      </div>

      {/* Priority field (FR-1) */}
      <div className="mb-6">
        <label
          htmlFor="priority"
          className="block text-sm font-medium mb-2"
        >
          Priority <span className="text-red-500">*</span>
        </label>
        <select
          id="priority"
          {...register('priority')}
          className="w-full px-3 py-2 border rounded"
          aria-required="true"
        >
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
          <option value="urgent">Urgent</option>
        </select>
      </div>

      {/* Submit button (FR-2) */}
      <button
        type="submit"
        disabled={isSubmitting || createTaskMutation.isPending}
        className="w-full bg-blue-600 text-white py-2 px-4 rounded
                   hover:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed"
      >
        {isSubmitting || createTaskMutation.isPending
          ? 'Creating...'
          : 'Create Task'}
      </button>
    </form>
  );
}

// 4. API CLIENT
// Based on API Contract (Section 5)

export async function createTask(data: CreateTaskInput): Promise<Task> {
  const response = await fetch('/api/tasks', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${getAuthToken()}`
    },
    body: JSON.stringify(data)
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.message || 'Failed to create task');
  }

  return response.json();
}

function getAuthToken(): string {
  // Implementation depends on auth system
  return localStorage.getItem('authToken') || '';
}

// 5. TESTS
// Based on Testing Requirements (Section 8)

describe('TaskCreationForm', () => {
  it('should validate required title', async () => {
    // Test validation logic
  });

  it('should auto-save draft every 5 seconds', async () => {
    // Test auto-save mechanism
  });

  it('should submit task successfully', async () => {
    // Test happy path
  });

  it('should handle API errors', async () => {
    // Test error scenarios
  });
});
```

**Why this matters:**

- ✅ Code matches specification exactly
- ✅ All requirements implemented
- ✅ Edge cases handled
- ✅ Accessibility included
- ✅ Tests structured

---

### Step 8: `/speckit.review` - Review Implementation

**What it does:** Compares your actual implementation against the specification to find gaps.

**When to use:** After coding, before code review or deployment.

**Example:**

```typescript
// Command:
/speckit.review

// Input: Your specification + your implementation code
```

**Output: Review Report**

````markdown
# Implementation Review Report

## 📋 Specification Compliance

### ✅ Fully Implemented (85%)

1. **FR-1: Task Form** ✅
   - All fields present and validated
   - Character counters working
   - Proper accessibility attributes

2. **FR-3: Auto-save Draft** ✅
   - Saves every 5 seconds
   - Restores on mount
   - Shows saved indicator

3. **NFR-2: Accessibility** ✅
   - ARIA labels present
   - Keyboard navigation works
   - Screen reader compatible

### ⚠️ Partially Implemented (10%)

4. **FR-2: Task Submission**
   - ✅ Validation working
   - ✅ Loading state shown
   - ✅ Success message displayed
   - ⚠️ **Missing**: Spec says "redirect to task list" but code uses `window.location.href`
     which causes full page reload. Should use React Router navigation.
5. **NFR-1: Performance**
   - ✅ Form renders quickly
   - ⚠️ **Issue**: No performance monitoring implemented
   - **Recommendation**: Add performance tracking

### ❌ Missing (5%)

6. **Edge Case: Network Timeout**
   - **Spec Section 6**: "Slow network - timeout after 30s"
   - **Status**: Not implemented
   - **Impact**: Users might wait indefinitely
   - **Fix Required**: Add timeout to fetch call

7. **Security Requirements**
   - **Spec Note**: Validation feedback mentioned localStorage corruption handling
   - **Status**: Basic try-catch present ✅
   - **Missing**: CSRF token in POST request
   - **Recommendation**: Add CSRF protection

## 🔍 Code Quality Issues

### 🔴 Critical

1. **Hardcoded Success Message**

   ```typescript
   // Current:
   alert("Task created successfully!");

   // Issue: Uses browser alert (poor UX)
   // Should: Use toast notification or inline message
   ```
````

2. **Missing Error Boundaries**
   - Component has no error boundary
   - Spec requires graceful error handling
   - Add error boundary wrapper

### 🟡 Moderate

3. **Inline Styles Mixed with Tailwind**
   - Some classes use Tailwind, some inline
   - Inconsistent styling approach
   - Standardize on Tailwind

4. **Magic Numbers**

   ```typescript
   // Current:
   setTimeout(() => setDraftSaved(false), 2000);

   // Should: Use named constants
   const DRAFT_SAVED_DISPLAY_TIME = 2000;
   ```

## 📊 Compliance Score: 85/100

| Category                    | Score  | Status |
| --------------------------- | ------ | ------ |
| Functional Requirements     | 90/100 | ✅     |
| Non-Functional Requirements | 75/100 | ⚠️     |
| Edge Cases                  | 70/100 | ⚠️     |
| Code Quality                | 85/100 | ✅     |
| Testing                     | 80/100 | ✅     |

## 🎯 Required Fixes Before Merge

1. Add 30-second timeout to API call
2. Replace alert() with proper notification system
3. Fix navigation to use React Router
4. Add CSRF token to requests
5. Add error boundary component

## 💚 Positive Highlights

- Excellent accessibility implementation
- Clean component structure
- Good error handling overall
- Auto-save feature works well
- Character counters helpful

## 📝 Next Steps

1. Fix required issues (listed above)
2. Add missing tests for edge cases
3. Performance monitoring
4. Update spec if any requirements changed during implementation

````

**Why this matters:**
- ✅ Ensures spec compliance
- ✅ Catches implementation gaps
- ✅ Improves code quality
- ✅ Prevents bugs before production

---

### Step 9: `/speckit.refine` - Improve Specifications

**What it does:** Updates and improves existing specifications based on new learnings or requirement changes.

**When to use:** When requirements change, after implementation feedback, or during retrospectives.

**Example:**

```typescript
// Command:
/speckit.refine

// Input:
"Based on user feedback, we need to add:
1. Ability to attach files to tasks
2. Task templates for common task types
3. Bulk task creation from CSV"
````

**Output: Refined Specification**

````markdown
# SPECIFICATION: Task Creation Feature (v2.0)

## Change Log

### v2.0 - 2026-04-15

**Added:**

- File attachments (up to 5 files, 10MB total)
- Task templates feature
- Bulk import from CSV

**Changed:**

- Increased description max length: 2000 → 5000 characters
- Added new priority level: "Critical" (above Urgent)

**Removed:**

- None

**Reason for changes:** User feedback from 100+ beta users

---

## New Requirements

### FR-4: File Attachments

Users can attach files to tasks:

**Specifications:**

- Support file types: PDF, DOC, DOCX, JPG, PNG, TXT
- Maximum 5 files per task
- Maximum total size: 10MB
- Files uploaded to S3
- Preview thumbnails for images
- Download functionality
- Delete attachment option

**API Changes:**

```typescript
// New endpoint
POST /api/tasks/{taskId}/attachments
Content-Type: multipart/form-data

// Task model now includes:
interface Task {
  // ... existing fields
  attachments: Array<{
    id: string;
    filename: string;
    size: number;
    mimeType: string;
    url: string;
    uploadedAt: Date;
  }>;
}
```
````

**Edge Cases:**

- File too large → Show error before upload
- Unsupported type → Show list of supported types
- Upload fails → Allow retry, keep form data
- Network interruption → Resume upload support

### FR-5: Task Templates

Pre-defined task templates for common task types:

**Templates:**

1. Bug Report
2. Feature Request
3. Code Review
4. Meeting Notes
5. Custom (user-created)

**Behavior:**

- Select template from dropdown
- Auto-populates title format, description template, priority
- User can override all fields
- Templates saved per workspace

### FR-6: Bulk Import

Import multiple tasks from CSV file:

**CSV Format:**

```csv
title,description,dueDate,priority
"Fix login bug","User cannot login",2026-04-20,high
"Add dark mode","Implement dark theme",2026-05-01,medium
```

**Requirements:**

- Validate each row before import
- Show preview before confirming
- Report: X succeeded, Y failed with reasons
- Maximum 100 tasks per import
- Skip duplicate detection

## Updated Success Criteria

### v2.0 Additional Criteria

- ✅ Users can attach files (90% success rate)
- ✅ Template usage reduces task creation time by 50%
- ✅ Bulk import handles 100 tasks in < 5 seconds
- ✅ File upload progress indicator shown
- ✅ CSV validation catches errors before import

## Migration Plan

**Phase 1 (Week 1):** File attachments
**Phase 2 (Week 2):** Task templates
**Phase 3 (Week 3):** Bulk import
**Phase 4 (Week 4):** Testing and refinement

**Backward Compatibility:**

- Existing tasks work without attachments
- API v1 still supported
- Gradual rollout with feature flags

````

**Why this matters:**
- ✅ Specs stay up-to-date
- ✅ Changes are documented
- ✅ Migration path clear
- ✅ Backward compatibility considered

---

## 📋 Specification Quality Best Practices

### What Makes a Good Specification?

#### 1. **Clear Problem Statement**

```markdown
❌ Bad:
"Build a search feature"

✅ Good:
"Users currently spend 5+ minutes manually scrolling through 200+ products
to find items. We need a search feature to reduce finding time to < 30 seconds
and improve user satisfaction by 40%."
````

#### 2. **Measurable Success Criteria**

```markdown
❌ Bad:
"Search should be fast"

✅ Good:
"Search response time < 200ms for 95th percentile"
"User finds desired item in < 3 searches (90% of sessions)"
```

#### 3. **Complete Edge Cases**

```markdown
❌ Bad:
"Handle errors"

✅ Good:
| Scenario | Behavior |
|----------|----------|
| Empty query | Show placeholder |
| No results | Suggest alternatives |
| Network timeout | Show retry button |
| Server error (500) | Log error, show fallback message |
| Typo in query | Fuzzy match, suggest "Did you mean...?" |
```

#### 4. **Explicit API Contracts**

```typescript
// ❌ Bad:
"Endpoint returns user data";

// ✅ Good:
interface UserResponse {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

// GET /api/users/:id
// Success: 200 OK + UserResponse
// NotFound: 404 + { error: string }
// Unauthorized: 401 + { error: string }
```

#### 5. **Prioritized Requirements**

```markdown
✅ Must Have (P0):

- User login
- Task creation
- Task viewing

🟡 Should Have (P1):

- Task editing
- File attachments
- Search

🔵 Nice to Have (P2):

- Task templates
- Bulk operations
- Analytics dashboard
```

### Specification Checklist

Before finalizing any spec, ensure:

- [ ] **Problem clearly stated** - Why are we building this?
- [ ] **User personas defined** - Who is this for?
- [ ] **Functional requirements numbered** - What must it do?
- [ ] **Non-functional requirements** - Performance, security, accessibility
- [ ] **API contracts documented** - Request/response formats
- [ ] **Edge cases listed** - What can go wrong?
- [ ] **Success criteria measurable** - How do we know it works?
- [ ] **Testing strategy defined** - How will we verify?
- [ ] **Dependencies identified** - What do we need?
- [ ] **Acceptance criteria written** - Given/When/Then format
- [ ] **Reviewed by team** - Feedback incorporated
- [ ] **Versioned and dated** - Trackable changes

---

## 🎨 Real-World Example: Complete SDD Workflow

Let's see a complete example from start to finish.

### Scenario: Building an Email Notification System

#### Step 1: Constitution

```markdown
# Email Notification System - Constitution

## Principles

- Reliability: 99.9% delivery rate
- Speed: Emails sent within 30 seconds
- Privacy: No PII in logs
- Scalability: Handle 100k emails/hour

## Standards

- All specs must include retry logic
- Security review required for email templates
- Performance benchmarks mandatory
```

#### Step 2: Specification

```markdown
# SPEC: User Signup Email Notification

## Problem

New users don't receive confirmation emails, causing 30% drop-off rate.

## Requirements

### FR-1: Send Welcome Email

When user signs up:

1. Validate email format
2. Queue email in background job
3. Send HTML email with:
   - Welcome message
   - Confirmation link (expires in 24h)
   - Getting started guide
4. Retry failed sends (3 attempts, exponential backoff)

### FR-2: Email Template

Subject: "Welcome to TaskFlow - Confirm Your Email"
Content:

- Personalized greeting with user's name
- Clear CTA button
- Mobile-responsive design
- Unsubscribe link (legal requirement)

### NFR-1: Performance

- Email queued in < 50ms
- Sent within 30 seconds
- API endpoint responds in < 100ms

### NFR-2: Reliability

- 99.9% delivery rate
- Automatic retry on failure
- Dead letter queue for failed emails
- Monitoring and alerts

## API Contract

POST /api/users/signup
→ Triggers email asynchronously
→ Returns 201 immediately (doesn't wait for email)

## Edge Cases

- Invalid email → Don't send, log error
- Email service down → Queue for retry
- User already confirmed → Don't resend
- Spam filter blocks → Log for investigation

## Success Criteria

- ✅ 99%+ emails delivered within 30s
- ✅ < 1% bounce rate
- ✅ 70% users confirm email within 24h
- ✅ Zero PII leaks in logs
```

#### Step 3: Validation

```markdown
✅ Score: 95/100

- Complete requirements
- Clear success criteria
- Good edge case coverage
  ⚠️ Add: Email template preview
  ⚠️ Add: Localization requirements
```

#### Step 4: Implementation

```typescript
// Email service implementation
import { Queue } from "bull";
import sgMail from "@sendgrid/mail";

interface WelcomeEmailData {
  userId: string;
  email: string;
  name: string;
  confirmationToken: string;
}

const emailQueue = new Queue<WelcomeEmailData>("emails", {
  redis: process.env.REDIS_URL,
});

export async function queueWelcomeEmail(data: WelcomeEmailData) {
  // Queue email (non-blocking)
  await emailQueue.add(data, {
    attempts: 3,
    backoff: {
      type: "exponential",
      delay: 2000,
    },
    removeOnComplete: true,
  });
}

// Worker process
emailQueue.process(async (job) => {
  const { email, name, confirmationToken } = job.data;

  const msg = {
    to: email,
    from: "noreply@taskflow.com",
    subject: "Welcome to TaskFlow - Confirm Your Email",
    html: generateWelcomeEmailHTML(name, confirmationToken),
  };

  try {
    await sgMail.send(msg);
    console.log(`Email sent to ${email}`);
  } catch (error) {
    console.error(`Failed to send email to ${email}:`, error);
    throw error; // Triggers retry
  }
});

function generateWelcomeEmailHTML(name: string, token: string): string {
  return `
    <!DOCTYPE html>
    <html>
    <head>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body style="font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto;">
      <h1>Welcome to TaskFlow, ${name}!</h1>
      <p>Thanks for signing up. Please confirm your email to get started.</p>
      <a href="https://taskflow.com/confirm?token=${token}" 
         style="display: inline-block; padding: 12px 24px; background: #007bff; 
                color: white; text-decoration: none; border-radius: 4px;">
        Confirm Email
      </a>
      <p style="margin-top: 24px; color: #666; font-size: 12px;">
        Link expires in 24 hours.
      </p>
    </body>
    </html>
  `;
}

// API endpoint
app.post("/api/users/signup", async (req, res) => {
  const { email, name, password } = req.body;

  // Create user
  const user = await createUser({ email, name, password });

  // Generate confirmation token
  const token = generateConfirmationToken(user.id);

  // Queue email (async, doesn't block response)
  await queueWelcomeEmail({
    userId: user.id,
    email: user.email,
    name: user.name,
    confirmationToken: token,
  });

  // Return immediately
  res.status(201).json({
    message: "User created. Check your email to confirm.",
  });
});
```

#### Step 5: Review

```markdown
✅ Spec Compliance: 98%

- All requirements implemented
- Retry logic working
- Performance targets met
- Edge cases handled

⚠️ Minor Issues:

- Add email template testing
- Add monitoring dashboard
- Document runbook for failures
```

#### Step 6: Refinement (After Launch)

```markdown
# SPEC v2.0: Email Notifications

## Added Based on Production Data:

- Rate limiting (1 email per user per hour)
- A/B testing support for email templates
- Email preview before sending
- Localization for 5 languages

## Changed:

- Retry attempts: 3 → 5 (improved delivery from 99.5% to 99.9%)
- Timeout: 30s → 60s (accommodate slower email providers)
```

---

## 🎤 Interview Questions & Answers

### Q1: What is Spec Driven Development and how does it differ from TDD?

**Answer:**

```typescript
// Spec Driven Development (SDD)
// 1. Write detailed specification
// 2. Get specification reviewed/validated
// 3. Implement based on spec
// 4. Review implementation against spec

// Test Driven Development (TDD)
// 1. Write failing test
// 2. Write code to pass test
// 3. Refactor
// 4. Repeat

// Key Differences:

// SDD focuses on WHAT (requirements, behavior, contracts)
Spec: "System shall send welcome email within 30 seconds";
("Email must include confirmation link");
("Retry 3 times on failure");

// TDD focuses on HOW (implementation details)
Test: expect(sendEmail()).toBeDefined();
expect(email.subject).toBe("Welcome");
expect(await sendEmail()).resolves.toBeTruthy();

// Can be Combined:
// 1. Write spec (SDD)
// 2. Write tests from spec
// 3. Write code to pass tests (TDD)
// 4. Review against spec (SDD)
```

**Key Takeaway:** SDD defines requirements and contracts before implementation. TDD verifies implementation through tests. They complement each other!

### Q2: What should every good specification include?

**Answer:**

```markdown
Essential Elements:

1. **Problem Statement**
   - What problem are we solving?
   - Why is it important?
   - Current state vs desired state

2. **Functional Requirements**
   - What must the system do?
   - Numbered and specific (FR-1, FR-2...)
   - Input/output defined

3. **Non-Functional Requirements**
   - Performance targets
   - Security requirements
   - Accessibility standards
   - Scalability goals

4. **API Contracts**
   - Request/response formats
   - Status codes
   - Error messages
   - TypeScript interfaces

5. **Edge Cases**
   - What can go wrong?
   - How should system respond?
   - Error recovery strategies

6. **Success Criteria**
   - Measurable outcomes
   - How we know it's done
   - Acceptance tests

7. **Dependencies**
   - External services
   - Libraries/frameworks
   - Other components

8. **Testing Strategy**
   - Unit tests
   - Integration tests
   - E2E tests
```

**Example of Complete Spec:**

```markdown
# SPEC: User Login

## Problem: Users need secure authentication

## Functional Requirements

FR-1: Accept email + password
FR-2: Validate credentials against database
FR-3: Return JWT token on success
FR-4: Return error on failure

## Non-Functional Requirements

NFR-1: Response time < 200ms
NFR-2: Rate limiting: 5 attempts per minute
NFR-3: Passwords hashed with bcrypt

## API Contract

POST /api/login
Request: { email: string, password: string }
Response: { token: string, userId: string }
Error: { error: string, code: number }

## Edge Cases

- Empty fields → 400 Bad Request
- Invalid email → 400 Bad Request
- Wrong password → 401 Unauthorized
- Account locked → 403 Forbidden
- Server error → 500 Internal Error

## Success Criteria

- ✅ Valid users can login
- ✅ Invalid credentials rejected
- ✅ No password leaks in logs
- ✅ Rate limiting prevents brute force
```

### Q3: How do you handle changing requirements in Spec Driven Development?

**Answer:**

````markdown
Change Management Process:

1. **Document the Change**
   - What's changing and why?
   - Who requested it?
   - Impact analysis

2. **Update the Spec**
   - Use version numbering (v1.0 → v2.0)
   - Add change log section
   - Mark deprecated features

3. **Review & Validate**
   - Get stakeholder approval
   - Technical feasibility check
   - Timeline impact

4. **Communicate Changes**
   - Notify all team members
   - Update related specs
   - Adjust implementation plan

5. **Track in Version Control**
   - Git commit with clear message
   - Link to ticket/issue
   - Document decision rationale

Example:

```markdown
# SPEC: User Profile (v2.0)

## Change Log

### v2.0 - 2026-04-08

**Added:**

- Profile picture upload
- Bio field (max 500 chars)
- Social media links

**Changed:**

- Username: now editable (was immutable)
- Name max length: 50 → 100 chars

**Removed:**

- Fax number field (obsolete)

**Reason:** User feedback survey (85% requested features)

**Impact:**

- Database migration required
- API breaking changes (v2 endpoint)
- 2 weeks additional development time

**Migration Plan:**

- Week 1: Implement changes
- Week 2: Testing
- Week 3: Deploy with feature flag
- Week 4: Full rollout
```
````

**Best Practices:**

- ✅ Never delete old spec versions
- ✅ Maintain change history
- ✅ Communicate breaking changes early
- ✅ Version APIs appropriately
- ✅ Use feature flags for gradual rollout

### Q4: What's the difference between `/speckit.validate` and `/speckit.review`?

**Answer:**

```markdown
/speckit.validate - Checks SPECIFICATION Quality
├─ Before implementation
├─ Analyzes spec document itself
├─ Checks for:
│ ├─ Completeness (all sections present?)
│ ├─ Clarity (requirements clear?)
│ ├─ Testability (can we verify?)
│ ├─ Consistency (no contradictions?)
│ └─ Missing edge cases
└─ Output: Spec quality score + improvement suggestions

/speckit.review - Checks IMPLEMENTATION Compliance
├─ After implementation
├─ Compares code vs spec
├─ Checks for:
│ ├─ All requirements implemented?
│ ├─ Code matches API contracts?
│ ├─ Edge cases handled?
│ ├─ Performance targets met?
│ └─ Deviations from spec?
└─ Output: Compliance score + gaps/issues

Workflow:

1. Write spec
2. Run /speckit.validate → Improve spec
3. Implement code
4. Run /speckit.review → Fix gaps
5. Deploy
```

**Example:**

```typescript
// Spec says:
"Response time must be < 200ms"

// /speckit.validate checks:
✅ Is this requirement measurable? YES
✅ Is success criteria clear? YES
✅ Is it testable? YES
✅ Spec quality: GOOD

// /speckit.review checks:
❌ Code doesn't have performance monitoring
❌ No timeout configured
❌ Missing: Response time measurement
⚠️ Requirement not verifiable in implementation
```

### Q5: How detailed should specifications be?

**Answer:**

````markdown
It depends on:

1. Team experience
2. Project complexity
3. Regulatory requirements
4. Risk level

General Rule: "Just enough detail to prevent ambiguity"

Too Little Detail (❌):
"Build a search feature that's fast and accurate"

- What's "fast"? 100ms? 1s?
- What's "accurate"? Exact match? Fuzzy?

Too Much Detail (❌):
"The search button shall be positioned 47 pixels from the left edge,
with a border radius of 4.5 pixels, using hex color #007bff,
with a transition duration of 150 milliseconds using cubic-bezier(0.4, 0, 0.2, 1)..."

- Over-specification
- Limits creativity
- Hard to maintain

Just Right (✅):
"Search response time: < 200ms (95th percentile)
Relevance: Results ranked by weighted score (title 60%, description 40%)
UI: Follow design system guidelines (Button component, primary variant)"

Balance Detail Levels:

High Detail Needed:
├─ Security requirements
├─ API contracts
├─ Data formats
├─ Performance targets
└─ Critical business logic

Low Detail Acceptable:
├─ UI styling (follow design system)
├─ Internal implementation
├─ Non-critical features
└─ Developer discretion areas

Example:

```markdown
# Login Spec

High Detail (Security critical):
"Passwords must:

- Minimum 8 characters
- Include 1 uppercase, 1 lowercase, 1 number
- Hashed with bcrypt (cost factor 10)
- Never logged or transmitted unencrypted
- Session tokens: JWT, 24-hour expiry"

Lower Detail (UI):
"Login form should:

- Follow design system (Form component)
- Mobile responsive
- Clear error messages
- Developer decides exact layout"
```
````

**Best Practice:**

- Be specific about WHAT and WHY
- Be flexible about HOW (unless critical)
- Let developers use their expertise for implementation details

### Q6: How do you measure if Spec Driven Development is working?

**Answer:**

```markdown
Key Metrics:

1. **Requirement Stability**
   - Changes after development starts
   - Goal: < 10% change rate

2. **Rework Reduction**
   - Code rewrites due to misunderstanding
   - Goal: 50% reduction compared to no-spec baseline

3. **Time to Clarity**
   - Time from ticket to "ready to code"
   - Goal: < 2 days for medium features

4. **Defect Reduction**
   - Bugs due to missing requirements
   - Goal: 60% reduction in requirement-related bugs

5. **Developer Confidence**
   - Survey: "I understand what to build"
   - Goal: 85%+ agree/strongly agree

6. **Review Cycle Time**
   - Rounds of review before approval
   - Goal: < 2 review cycles

7. **Documentation Quality**
   - "Can new dev understand spec?"
   - Goal: 90%+ comprehension score

Tracking Example:

Before SDD (Baseline):
├─ Requirement changes: 35% of features
├─ Rework: 25% of dev time
├─ Understanding: 60% developer confidence
└─ Bugs: 40 requirement-related bugs/quarter

After SDD (6 months):
├─ Requirement changes: 8% of features ✅ (77% reduction)
├─ Rework: 10% of dev time ✅ (60% reduction)
├─ Understanding: 90% developer confidence ✅ (50% improvement)
└─ Bugs: 12 requirement-related bugs/quarter ✅ (70% reduction)

ROI Calculation:
Time saved on rework: 15% of dev capacity
→ For 10-person team: ~1.5 FTE savings
→ Annual: ~$180k saved (at $120k/FTE)
Cost of writing specs: ~3-5 hours per feature
→ Annual: ~$30k investment
→ Net Benefit: $150k/year
```

### Q7: What are common mistakes in writing specifications?

**Answer:**

```markdown
Top 10 Specification Mistakes:

1. ❌ **Vague Requirements**
   Bad: "System should be fast"
   Good: "API response < 200ms (95th percentile)"

2. ❌ **Missing Edge Cases**
   Bad: "Handle user upload"
   Good: "Handle user upload - File too large → Show error - Invalid type → Reject with message - Network failure → Allow retry"

3. ❌ **No Success Criteria**
   Bad: "Build search feature"
   Good: "Success: Users find items in < 30 seconds (80% of searches)"

4. ❌ **Mixing Requirements with Implementation**
   Bad: "Use Redux for state management"
   Good: "Maintain user session across page refreshes"
   (Let devs choose Redux, Context, or other solutions)

5. ❌ **Unclear Priorities**
   Bad: "Add these 20 features"
   Good: "P0 (Must): Login, Search
   P1 (Should): Filters, Sort
   P2 (Nice): Export, Share"

6. ❌ **No API Contracts**
   Bad: "Create user endpoint"
   Good: "POST /api/users
   Request: { email, name, password }
   Response: { id, email, createdAt }
   Errors: 400, 409, 500"

7. ❌ **Forgetting Non-Functionals**
   Bad: Only functional features listed
   Good: Also include performance, security, accessibility

8. ❌ **No Stakeholder Review**
   Bad: Developer writes spec alone
   Good: Review with PM, design, backend, QA

9. ❌ **Unchangeable Specs**
   Bad: "Spec is finalized, no changes"
   Good: "Spec v1.0, changes tracked with version numbers"

10. ❌ **No Acceptance Criteria**
    Bad: "Build feature"
    Good: "Given user is logged in
    When they click create task
    Then task is saved and visible in list"
```

### Q8: How does Spec Driven Development fit with Agile?

**Answer:**

```markdown
SDD + Agile = Perfect Match! 🤝

Common Misconception:
"Agile means no documentation, just code"
❌ WRONG!

Agile Principle:
"Working software over comprehensive documentation"
≠ "No documentation"
= "Right amount of documentation"

How they work together:

Sprint Planning:
├─ Product backlog has user stories
├─ Before sprint: Write specs for selected stories
├─ Team reviews specs in planning
└─ Stories marked "ready" only with validated specs

During Sprint:
├─ Developers implement based on specs
├─ Specs can evolve (but tracked)
├─ Daily standups reference specs
└─ Blockers include spec ambiguities

Sprint Review:
├─ Demo against spec success criteria
├─ Stakeholders verify requirements met
└─ Decide if "done" based on spec

Sprint Retrospective:
└─ Review spec quality

- Were specs clear?
- Did specs help or hinder?
- How can we improve specs?

Example 2-Week Sprint:

Week 1:
Day 1-2: /speckit.constitution (if new project)
Day 2-3: /speckit.specify for sprint stories
Day 3: /speckit.validate all specs
Day 4-5: Begin implementation

Week 2:
Day 6-9: Continue implementation
Day 9: /speckit.review completed work
Day 10: Demo + retro + planning

Benefits in Agile:
✅ Specs replace lengthy meetings
✅ Async collaboration (distributed teams)
✅ Clear definition of done
✅ Better estimates (clearer scope)
✅ Less mid-sprint scope confusion

Lightweight Specs for Agile:

- Keep specs concise (2-3 pages max)
- Focus on acceptance criteria
- Include API contracts
- Defer UI details to designers
- Version specs like code
```

---

## 🎯 Key Takeaways

### What is Spec Driven Development?

- Write detailed specifications BEFORE coding
- Define requirements, contracts, success criteria
- Living documentation that evolves

### Why Use SDD?

- **Clear Communication** - Everyone aligned
- **Fewer Bugs** - Requirements understood upfront
- **Less Rework** - Catch issues in planning
- **Better Testing** - Clear expected behavior
- **Faster Onboarding** - Self-documenting system

### SpecKit Commands Summary

| Command        | Purpose               | Phase                    |
| -------------- | --------------------- | ------------------------ |
| `constitution` | Set project standards | Start                    |
| `specify`      | Write feature specs   | Before coding            |
| `validate`     | Check spec quality    | After spec draft         |
| `implement`    | Generate code         | Coding phase             |
| `review`       | Verify compliance     | After coding             |
| `refine`       | Update specs          | When requirements change |

### Specification Quality

- Clear problem statements
- Measurable success criteria
- Complete edge cases
- Explicit API contracts
- Testable requirements

### Best Practices

- ✅ Spec before code (always!)
- ✅ Version your specs
- ✅ Review specs as a team
- ✅ Keep specs up-to-date
- ✅ Balance detail appropriately
- ✅ Include non-functional requirements
- ✅ Define clear success criteria

---

**That's Spec Driven Development!** 🎉

You now know:

- ✅ What SDD is and why it matters
- ✅ How to use SpecKit commands effectively
- ✅ How to write quality specifications
- ✅ Best practices and common pitfalls
- ✅ How to measure success

**Next:** Start using SDD in your projects! Write your first spec today! 🚀

---

_Last Updated: April 8, 2026_
