# Domain 3: Claude Code Configuration & Workflows - Concept Guide

**Study this guide BEFORE attempting Domain 3 questions**

This guide explains Claude Code configuration, CLAUDE.md, skills, commands, and workflows.

---

## �️ Concept Map

```
CLAUDE CODE CONFIGURATION & WORKFLOWS
│
├── CLAUDE.md CONFIGURATION
│   ├── Location: .claude/CLAUDE.md
│   ├── The 200-Line Rule
│   │   └── Focus on critical rules, not preferences
│   ├── Content
│   │   ├── Tech stack
│   │   ├── Code standards
│   │   ├── Critical rules
│   │   └── File structure
│   └── Modular Organization
│       └── @import .claude/rules/specific-rules.md
│
├── CONFIGURATION HIERARCHY
│   ├── Directory-specific CLAUDE.md (Highest)
│   ├── Project CLAUDE.md (.claude/)
│   └── User CLAUDE.md (~/.claude/) (Lowest)
│
├── PATH-SPECIFIC RULES
│   └── YAML Frontmatter
│       └── paths: ["src/api/**/*.ts"]
│
├── SKILLS vs COMMANDS
│   ├── Commands (.claude/commands/)
│   │   ├── Purpose: Simple, single-purpose actions
│   │   └── Example: Format code, run tests
│   └── Skills (.claude/skills/)
│       ├── Purpose: Complex, multi-step workflows
│       ├── Frontmatter Options
│       │   ├── context: fork (Isolated session)
│       │   ├── allowed-tools: [...] (Restrict tools)
│       │   └── argument-hint: "..." (User guidance)
│       └── Example: Code review, complex refactoring
│
├── PLAN MODE vs DIRECT EXECUTION
│   ├── Plan Mode (--plan or -p)
│   │   ├── Shows proposed changes BEFORE executing
│   │   └── Use for: Complex refactoring, high-risk changes
│   └── Direct Execution
│       ├── Executes immediately
│       └── Use for: Simple tasks, low-risk changes
│
├── CI/CD INTEGRATION
│   ├── Non-Interactive Mode
│   │   └── Flag: -p (prompt flag)
│   ├── Structured Output
│   │   └── Flag: --output-format json
│   └── Schema Validation
│       └── Flag: --json-schema schema.json
│
├── BATCH API
│   ├── Benefits
│   │   ├── 50% cost savings
│   │   └── Process multiple requests together
│   ├── Trade-off
│   │   └── Results within 24 hours (not instant)
│   ├── custom_id
│   │   └── Track which response belongs to which request
│   └── Use Cases
│       ├── ✓ Bulk processing (1000 code reviews)
│       ├── ✓ Non-urgent analysis
│       └── ✗ Real-time needs
│
└── ITERATIVE REFINEMENT
    ├── Pattern 1: Basic → Enhanced → Polished
    ├── Pattern 2: Interview Pattern
    │   ├── Ask Claude to propose approach
    │   ├── Review proposal
    │   └── Iterate based on feedback
    ├── Pattern 3: TDD
    │   ├── Write failing test
    │   ├── Implement code
    │   └── Refactor
    └── Multi-Pass Review
        ├── Pass 1: Generate (Session 1)
        ├── Pass 2: Review (Fresh Session 2)
        │   └── Why fresh: Avoid confirmation bias
        ├── Pass 1 Focus: Per-file analysis
        └── Pass 2 Focus: Cross-file integration
```

---

## �📚 Table of Contents

1. [CLAUDE.md Configuration](#claudemd-configuration)
2. [Configuration Hierarchy](#configuration-hierarchy)
3. [Skills vs Commands](#skills-vs-commands)
4. [Plan Mode vs Direct Execution](#plan-mode-vs-direct-execution)
5. [CI/CD Integration](#cicd-integration)
6. [Batch API](#batch-api)
7. [Iterative Refinement](#iterative-refinement)

---

## 1. CLAUDE.md Configuration

### What is CLAUDE.md?

**CLAUDE.md** is the main configuration file that tells Claude how to work on your project.

**Location:** `.claude/CLAUDE.md` in project root

### What Goes in CLAUDE.md?

```markdown
# Project: E-commerce API

## Tech Stack
- Node.js + Express
- PostgreSQL database
- TypeScript strict mode

## Code Standards
- All functions must have JSDoc comments
- Use async/await, not callbacks
- Error handling required for all API endpoints
- Test coverage minimum 80%

## File Structure
- `/src/routes` - API endpoints
- `/src/models` - Database models
- `/src/services` - Business logic
```

### The 200-Line Rule

**Keep CLAUDE.md under 200 lines**

**Why?** Every low-value instruction degrades high-value ones.

❌ **BAD:** 500 lines with minor preferences
```markdown
- Use single quotes
- Add semicolons
- Prefer const over let
- Use arrow functions
- Add trailing commas
... (495 more lines of minor style preferences)
```

✓ **GOOD:** ~150 lines of critical rules
```markdown
## Critical Rules
- All API endpoints MUST have rate limiting
- Never store passwords in plain text
- All database queries MUST use parameterized statements
- Authentication required for all /api/* routes

## Tech Stack
...
```

**Principle:** Focus on what MUST be followed, not preferences.

---

## 2. Configuration Hierarchy

### Three Levels

```
Directory-specific CLAUDE.md  (highest priority)
         ↓
Project CLAUDE.md (.claude/CLAUDE.md)
         ↓
User CLAUDE.md (~/.claude/CLAUDE.md)  (lowest priority)
```

### Example: Monorepo Structure

```
my-monorepo/
├── .claude/
│   └── CLAUDE.md              # Shared project rules
├── frontend/
│   └── .claude/
│       └── CLAUDE.md          # Frontend-specific rules
└── backend/
    └── .claude/
        └── CLAUDE.md          # Backend-specific rules
```

**How it works:**
- Working in `/frontend`: Uses frontend CLAUDE.md + project CLAUDE.md
- Working in `/backend`: Uses backend CLAUDE.md + project CLAUDE.md

### Modular Configuration with @import

Instead of one huge file:

```markdown
# Main CLAUDE.md

@import .claude/rules/api-standards.md
@import .claude/rules/database-rules.md
@import .claude/rules/security-policies.md
```

**Benefits:**
- Organized by topic
- Easier to maintain
- Reusable across projects

### Path-Specific Rules

Use YAML frontmatter for path-specific rules:

```markdown
---
paths: ["src/api/**/*.ts"]
---

# API-Specific Rules

- All endpoints must have rate limiting
- OpenAPI documentation required
- Input validation on all parameters
```

```markdown
---
paths: ["src/components/**/*.tsx"]
---

# Component-Specific Rules

- All components must use TypeScript
- PropTypes required
- Accessibility attributes required
```

---

## 3. Skills vs Commands

### Commands: Simple Actions

**Location:** `.claude/commands/`

**Purpose:** Single-purpose, simple operations

**Example:** `format.md`
```markdown
Format all TypeScript files using Prettier
```

**Usage:** `/format`

### Skills: Complex Workflows

**Location:** `.claude/skills/`

**Purpose:** Multi-step, complex workflows

**Example:** `code-review.md`
```markdown
---
context: fork
allowed-tools: ["Read", "Grep", "Edit"]
argument-hint: "file or directory to review"
---

# Code Review Skill

1. Read the specified files
2. Check for:
   - Missing error handling
   - Security vulnerabilities
   - Performance issues
   - Code style violations
3. Generate detailed review report
4. Suggest specific improvements
```

**Usage:** `/code-review src/api`

### Frontmatter Options

```yaml
---
context: fork              # Run in isolated session
allowed-tools: ["Read", "Edit", "Bash"]  # Restrict tools
argument-hint: "path to files"  # Help text for users
---
```

**context: fork** - Runs skill in separate session (doesn't affect main work)

**allowed-tools** - Limits which tools the skill can use

**argument-hint** - Shows users what arguments are needed

### When to Use Each

| Use Case | Type |
|----------|------|
| Format code | Command |
| Run tests | Command |
| Simple file operations | Command |
| Multi-step code review | Skill |
| Complex refactoring | Skill |
| Generate + test + document | Skill |

---

## 4. Plan Mode vs Direct Execution

### Plan Mode

**Flag:** `--plan` or `-p`

**What it does:** Shows proposed changes BEFORE executing

```bash
claude --plan "refactor authentication system"
```

**Output:**
```
Plan:
1. Read current auth files
2. Identify issues
3. Propose new structure
4. Show file changes

[Review plan] → Approve or modify
```

**When to use:**
- Complex refactoring
- High-risk changes
- Unfamiliar codebase
- Large-scale modifications

### Direct Execution

**What it does:** Executes immediately without showing plan

```bash
claude "fix typo in README"
```

**When to use:**
- Well-understood tasks
- Low-risk changes
- Quick fixes
- Familiar code

### Decision Matrix

| Task Type | Mode | Reason |
|-----------|------|--------|
| Refactor 20 files | Plan | Review before execution |
| Fix typo | Direct | Simple, low-risk |
| New feature | Plan | Complex, review needed |
| Update dependency | Direct | Straightforward |
| Architecture change | Plan | High-risk, review needed |

---

## 5. CI/CD Integration

### Non-Interactive Mode

**Flag:** `-p` (prompt flag for non-interactive)

```bash
# CI/CD pipeline
claude -p "generate API documentation"
```

**What it does:**
- No user prompts
- Automatic execution
- Suitable for automation

### Structured Output

**Flag:** `--output-format json`

```bash
claude --output-format json "analyze code quality" > results.json
```

**Output:**
```json
{
  "success": true,
  "results": {
    "issues_found": 5,
    "coverage": 85,
    "complexity": "medium"
  }
}
```

**Use in CI:** Parse JSON for pass/fail decisions

### Schema Validation

**Flag:** `--json-schema schema.json`

```bash
claude --json-schema api-schema.json "generate API spec"
```

**What it does:** Validates output against JSON schema

**Example schema.json:**
```json
{
  "type": "object",
  "required": ["endpoints", "version"],
  "properties": {
    "endpoints": {"type": "array"},
    "version": {"type": "string"}
  }
}
```

### CI/CD Pipeline Example

```yaml
# .github/workflows/code-gen.yml
steps:
  - name: Generate code
    run: |
      claude -p \
        --output-format json \
        --json-schema schema.json \
        "generate API client" > output.json
  
  - name: Validate output
    run: |
      if jq -e '.success' output.json; then
        echo "Success"
      else
        exit 1
      fi
```

---

## 6. Batch API

### What is the Batch API?

Process multiple requests together with **50% cost savings**.

**Trade-off:** Results within 24 hours (not instant)

### When to Use

✓ **Good for:**
- Processing 1000 code reviews
- Bulk data extraction
- Non-urgent analysis
- Cost-sensitive operations

❌ **Not good for:**
- Real-time needs
- Interactive workflows
- Urgent requests

### How It Works

```python
# Create batch request
batch = {
    "requests": [
        {
            "custom_id": "review-1",
            "params": {
                "prompt": "Review file1.js"
            }
        },
        {
            "custom_id": "review-2",
            "params": {
                "prompt": "Review file2.js"
            }
        }
        # ... 998 more
    ]
}

# Submit batch
batch_id = client.batches.create(batch)

# Check status later
status = client.batches.retrieve(batch_id)

# Get results when done
results = client.batches.results(batch_id)
```

### custom_id Field

**Purpose:** Track which response belongs to which request

```python
{
    "custom_id": "file-123-review",  # Your identifier
    "params": {...}
}

# Later, in results:
{
    "custom_id": "file-123-review",  # Same ID
    "response": {...}
}
```

---

## 7. Iterative Refinement

### What is Iterative Refinement?

Progressively improving output through multiple prompts.

### Pattern 1: Basic → Enhanced → Polished

```
Iteration 1: "Create basic login component"
→ Basic structure created

Iteration 2: "Add form validation"
→ Validation added

Iteration 3: "Add error handling and loading states"
→ Complete component
```

### Pattern 2: Interview Pattern

**Step 1:** Ask Claude to propose approach
```
"How would you approach refactoring this authentication system?"
```

**Step 2:** Review proposal
```
Claude: "I suggest:
1. Extract auth logic to separate service
2. Implement JWT tokens
3. Add refresh token mechanism"
```

**Step 3:** Iterate based on review
```
"Good, but use sessions instead of JWT. Proceed with that approach."
```

### Pattern 3: TDD (Test-Driven Development)

```
Iteration 1: "Write failing test for user login"
→ Test created

Iteration 2: "Implement code to make test pass"
→ Implementation

Iteration 3: "Refactor for better code quality"
→ Refactored code
```

### Multi-Pass Review

**Why?** Same-session review has confirmation bias.

**Pass 1:** Generate code
```
Session 1: "Create user authentication system"
→ Code generated
```

**Pass 2:** Review in FRESH session
```
Session 2 (new): "Review this authentication code for security issues"
→ Unbiased review
```

**Key:** Fresh session = No memory of generation = Unbiased review

### Pass 1 vs Pass 2 Focus

**Pass 1: Per-File Analysis**
- Code quality
- Style compliance
- Basic errors
- Individual file issues

**Pass 2: Cross-File Integration (Fresh Session)**
- Architecture
- Integration issues
- Cross-file dependencies
- System-level concerns

---

## 🎯 Key Takeaways

### Critical Concepts

1. **CLAUDE.md under 200 lines** - Focus on critical rules
2. **Hierarchy:** Directory > Project > User
3. **Commands** for simple actions, **Skills** for complex workflows
4. **Plan mode** for high-risk, **Direct** for low-risk
5. **Batch API** = 50% savings, 24-hour window
6. **Fresh session** for unbiased review
7. **CI/CD:** Use `-p` flag + `--output-format json`

### Common Anti-Patterns

❌ 500-line CLAUDE.md with minor preferences
❌ All rules in one file (use @import)
❌ Using direct execution for complex refactoring
❌ Same-session self-review
❌ Batch API for urgent needs
❌ No schema validation in CI/CD

### Configuration Checklist

- [ ] CLAUDE.md under 200 lines
- [ ] Critical rules clearly marked
- [ ] Path-specific rules for different directories
- [ ] Skills use `context: fork` for isolation
- [ ] Skills restrict tools with `allowed-tools`
- [ ] CI/CD uses non-interactive mode
- [ ] Batch processing for non-urgent bulk work

---

## 📖 Study Flow

**Now that you understand these concepts:**

1. ✅ Read this concept guide
2. ✅ Review the examples
3. ✅ Understand configuration hierarchy
4. ➡️ **Now attempt Domain 3 Questions** → [Domain-3-Questions.md](./Domain-3-Questions.md)

---

**Previous:** [Domain 2 Concepts](./Domain-2-Concepts.md) | **Next:** [Domain 4 Concepts →](./Domain-4-Concepts.md)
