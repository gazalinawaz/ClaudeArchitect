# Claude Certified Architect – Foundations: Complete Study Guide

## 📋 Certification Overview

**Exam Format:**
- Multiple choice questions (1 correct answer, 3 distractors)
- Scenario-based questions
- 50 questions total (4 of 6 scenarios randomly selected)
- Passing score: 720/1000 (72%)
- Time: Approximately 90 minutes

**Domain Weightings:**
1. **Agentic Architecture & Orchestration** - 27% (~23 hours study time)
2. **Tool Design & MCP Integration** - 18% (~15 hours study time)
3. **Claude Code Configuration & Workflows** - 20% (~17 hours study time)
4. **Prompt Engineering & Structured Output** - 20% (~17 hours study time)
5. **Context Management & Reliability** - 15% (~13 hours study time)

---

## 🎯 6 Core Exam Scenarios

You must master these scenarios as the exam randomly selects 4 of 6:

1. **Customer Support Resolution Agent** - Agent SDK, MCP tools, escalation logic
2. **Code Generation with Claude Code** - CLAUDE.md, plan mode, slash commands
3. **Multi-Agent Research System** - Coordinator-subagent patterns, context passing, error propagation
4. **Developer Productivity with Claude** - Built-in tools, MCP servers, codebase exploration
5. **Claude Code for CI/CD** - `-p` flag, structured output, batch API, multi-pass review
6. **Structured Data Extraction** - JSON schemas, tool_use, validation-retry, few-shot prompting

---

## 📚 Domain 1: Agentic Architecture & Orchestration (27%)

### 1.1 Agentic Loop Lifecycle

**Core Concepts:**
- **stop_reason** is the definitive loop termination indicator
  - `"tool_use"` - Agent wants to execute a tool
  - `"end_turn"` - Agent has completed its work
  - `"max_tokens"` - Response truncated (handle gracefully)
  - `"stop_sequence"` - Custom stop sequence encountered

**Agentic Loop Pattern:**
```python
from anthropic import Anthropic

client = Anthropic()
messages = []

while True:
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=4096,
        messages=messages,
        tools=tools
    )
    
    # Check stop_reason - THE definitive termination signal
    if response.stop_reason == "end_turn":
        break
    elif response.stop_reason == "tool_use":
        # Execute tools and append results
        for content_block in response.content:
            if content_block.type == "tool_use":
                result = execute_tool(content_block)
                messages.append({
                    "role": "user",
                    "content": [{
                        "type": "tool_result",
                        "tool_use_id": content_block.id,
                        "content": result
                    }]
                })
```

**Critical Anti-Patterns (Common Wrong Answers):**
- ❌ Parsing natural language output for termination signals
- ❌ Arbitrary iteration caps as primary stopping mechanism
- ❌ Checking assistant text content for "done" or "complete"
- ❌ Using self-reported confidence scores for decisions

### 1.2 Multi-Agent Orchestration

**Hub-and-Spoke Architecture:**
- **Coordinator Agent**: Orchestrates workflow, delegates to subagents
- **Subagents**: Specialized for specific tasks (search, analysis, synthesis)
- **Context Isolation**: Each subagent has isolated context (no shared memory)

**Task Tool for Subagent Spawning:**
```python
# Coordinator must include "Task" in allowedTools
coordinator_config = {
    "allowedTools": ["Task", "other_tools"],
    "systemPrompt": "You coordinate research tasks..."
}

# Coordinator spawns subagent
response = coordinator.run("Research quantum computing")
# Coordinator uses Task tool to spawn specialized subagent
```

**Parallel Subagent Execution:**
- Multiple `Task` calls in single response = parallel execution
- Use `fork_session` for independent exploration paths
- Explicit context passing required (no implicit state sharing)

**Key Principles:**
- Coordinator role: Task decomposition, result synthesis, error handling
- Subagent context isolation prevents cross-contamination
- Explicit context passing via tool results
- Avoid overly narrow task decomposition (creates coverage gaps)

### 1.3 Hooks and Programmatic Enforcement

**PostToolUse Hooks:**
- Intercept tool calls for validation, normalization, compliance
- Execute AFTER tool call, BEFORE result returned to agent
- Use cases: Data normalization, policy enforcement, logging

**Programmatic vs Prompt-Based Enforcement:**
- **Programmatic (Hooks)**: Deterministic, guaranteed enforcement
  - Critical business rules (refund limits, data access)
  - Compliance requirements
  - Security policies
- **Prompt-Based**: Probabilistic, guidance only
  - Stylistic preferences
  - Soft guidelines
  - Contextual suggestions

**Example Hook Pattern:**
```python
def post_tool_use_hook(tool_name, tool_input, tool_result):
    if tool_name == "process_refund":
        if tool_input["amount"] > 500:
            # Block and escalate
            return {
                "isError": True,
                "errorCategory": "policy_violation",
                "message": "Refunds over $500 require manager approval",
                "escalate": True
            }
    return tool_result
```

### 1.4 Session Management

**Session Types:**
- `--resume`: Continue previous session with full context
- `fork_session`: Branch from current state for exploration
- Named sessions: Organize parallel work streams

**Session Context Degradation:**
- Long sessions accumulate context, may lose focus
- Use scratchpad files for persistent state
- Consider session reset for fresh perspective

**Best Practices:**
- Name sessions descriptively for easy resumption
- Fork sessions for experimental paths
- Monitor context window usage
- Use `/compact` to summarize and reduce context

### 1.5 Task Decomposition Strategies

**Prompt Chaining vs Dynamic Adaptive:**
- **Prompt Chaining**: Predefined sequence of prompts
  - Good for: Known workflows, repeatable processes
  - Limited: Cannot adapt to unexpected findings
- **Dynamic Adaptive**: Agent determines next steps based on results
  - Good for: Exploratory tasks, complex problem-solving
  - Requires: Strong coordinator logic

**Decomposition Anti-Patterns:**
- Overly narrow decomposition → coverage gaps
- Too many subagents → coordination overhead
- Insufficient context passing → redundant work

---

## 🔧 Domain 2: Tool Design & MCP Integration (18%)

### 2.1 Tool Description Best Practices

**Effective Tool Descriptions:**
```json
{
  "name": "search_database",
  "description": "Search customer database by email, phone, or customer ID. Returns customer profile with order history. Use email for most accurate results. Phone numbers must be in E.164 format (+1234567890). Returns empty array if no match found.",
  "input_schema": {
    "type": "object",
    "properties": {
      "search_type": {
        "type": "string",
        "enum": ["email", "phone", "customer_id"],
        "description": "Type of identifier to search by"
      },
      "search_value": {
        "type": "string",
        "description": "The identifier value. For phone, use E.164 format."
      }
    },
    "required": ["search_type", "search_value"]
  }
}
```

**Key Elements:**
- Input formats with examples
- Edge cases and boundaries
- Expected output structure
- Error conditions
- When to use vs other tools

### 2.2 Structured Error Responses

**Error Response Schema:**
```json
{
  "isError": true,
  "errorCategory": "authentication_failed",
  "isRetryable": false,
  "message": "API key expired. Please refresh credentials.",
  "context": {
    "attempted_operation": "fetch_user_data",
    "user_id": "12345"
  }
}
```

**Error Categories:**
- `authentication_failed` - Credentials invalid
- `authorization_denied` - Insufficient permissions
- `resource_not_found` - Requested resource doesn't exist
- `rate_limit_exceeded` - Too many requests
- `validation_error` - Input validation failed
- `service_unavailable` - Temporary service issue

**isRetryable Guidelines:**
- `true`: Transient errors (rate limits, temporary outages)
- `false`: Permanent errors (auth failures, not found, validation)

### 2.3 Tool Distribution and Selection

**Optimal Tool Count:**
- **4-5 tools per agent** - Optimal for selection accuracy
- **18+ tools** - Significantly degrades selection quality
- Use scoped tool access per subagent

**tool_choice Options:**
```python
# Auto - Let Claude decide
tool_choice={"type": "auto"}

# Any - Force tool use, Claude picks which
tool_choice={"type": "any"}

# Specific tool - Force specific tool
tool_choice={"type": "tool", "name": "search_database"}
```

**When to Use:**
- `auto`: Default, most scenarios
- `any`: Guarantee tool use (structured output)
- `specific`: Force particular tool (validation, specific workflow step)

### 2.4 Model Context Protocol (MCP) Configuration

**MCP Server Scopes:**

1. **Local Scope** (`~/.claude.json` or `.claude/settings.local.json`)
   - Personal servers, experimental configs
   - Sensitive credentials for one project
   - Not checked into version control

2. **Project Scope** (`.mcp.json` in project root)
   - Team-shared servers
   - Project-specific tools
   - Checked into version control
   - Shared across team

3. **User Scope** (`~/.claude.json` under user config)
   - Personal utilities across projects
   - Development tools
   - Frequently used services

**Scope Hierarchy:**
```
Managed > Project > User > Local
```

**Environment Variable Expansion:**
```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

**Syntax:**
- `${VAR}` - Expands to environment variable VAR
- `${VAR:-default}` - Uses default if VAR not set

### 2.5 Built-in Tools (Claude Agent SDK)

**Core Tools:**
- **Read**: Read file contents
- **Write**: Create/overwrite files
- **Edit**: Modify existing files (search/replace)
- **Bash**: Execute shell commands
- **Grep**: Search file contents
- **Glob**: Find files by pattern

**When to Use Each:**
- **Read**: Understand existing code, review files
- **Edit**: Targeted modifications (preferred over Write for changes)
- **Write**: Create new files, complete rewrites
- **Bash**: Run tests, build commands, system operations
- **Grep**: Find specific code patterns, search across files
- **Glob**: Discover files matching patterns

**Best Practices:**
- Prefer Edit over Write for modifications (preserves unchanged content)
- Use Grep before Edit to locate code
- Use Glob to discover relevant files
- Bash for verification (run tests after changes)

---

## ⚙️ Domain 3: Claude Code Configuration & Workflows (20%)

### 3.1 CLAUDE.md Hierarchy

**Configuration Levels (Precedence Order):**
1. **Directory-specific** - `.claude/CLAUDE.md` in subdirectory
2. **Project** - `.claude/CLAUDE.md` in project root
3. **User** - `~/.claude/CLAUDE.md` in home directory

**Structure:**
```markdown
# Project: E-commerce Platform

## Code Standards
- Use TypeScript strict mode
- Follow Airbnb style guide
- All functions must have JSDoc comments

## Testing Requirements
- Unit tests required for all business logic
- Integration tests for API endpoints
- Minimum 80% code coverage

## Security Rules
- Never log sensitive data (passwords, tokens, PII)
- Validate all user inputs
- Use parameterized queries for database access
```

**Best Practices:**
- Keep under 200 lines (every low-value instruction degrades high-value ones)
- Focus on team standards, not general advice
- Use specific, actionable rules
- Organize by topic (code standards, testing, security)

### 3.2 Advanced Configuration

**@import Syntax:**
```markdown
@import .claude/rules/security.md
@import .claude/rules/testing.md
```

**Topic-Specific Rules Directory:**
```
.claude/
  ├── CLAUDE.md (main config)
  ├── rules/
  │   ├── security.md
  │   ├── testing.md
  │   ├── api-design.md
  │   └── database.md
```

**Path-Specific Rules (YAML Frontmatter):**
```markdown
---
paths:
  - "src/api/**/*.ts"
  - "src/routes/**/*.ts"
---

# API Endpoint Rules
- All endpoints must have rate limiting
- Return proper HTTP status codes
- Include request/response examples in comments
```

### 3.3 Custom Commands and Skills

**Slash Commands** (`.claude/commands/`)
- Simple, single-purpose actions
- No complex logic
- Example: `/format-code`, `/run-tests`

**Skills** (`.claude/skills/`)
- Complex, multi-step workflows
- Can use tools and subagents
- Reusable across sessions

**SKILL.md Frontmatter:**
```markdown
---
context: fork
allowed-tools: ["Read", "Edit", "Bash"]
argument-hint: "path/to/component"
---

# Refactor Component Skill

This skill refactors a React component to use hooks...

## Steps
1. Read component file
2. Identify class-based patterns
3. Convert to functional component
4. Replace lifecycle methods with hooks
5. Run tests to verify
```

**Frontmatter Options:**
- `context: fork` - Run in forked session (isolated)
- `allowed-tools` - Restrict tools available
- `argument-hint` - Help text for arguments

### 3.4 Plan Mode vs Direct Execution

**When to Use Plan Mode:**
- Complex refactoring across multiple files
- High-risk changes (database migrations, API changes)
- Unfamiliar codebases
- Need to review before execution

**When to Use Direct Execution:**
- Well-understood tasks
- Low-risk changes
- Iterative development
- Quick fixes

**Activating Plan Mode:**
```bash
# Interactive mode
claude --plan "Refactor authentication system"

# CI/CD mode
claude -p "Generate API documentation"
```

**Plan Mode Benefits:**
- Review proposed changes before execution
- Understand impact across codebase
- Catch potential issues early
- Learn codebase structure

### 3.5 Iterative Refinement Patterns

**Effective Iteration Strategies:**

1. **Concrete Examples:**
   ```
   "Generate user profile component"
   → Shows basic structure
   
   "Add loading state and error handling"
   → Refines with edge cases
   
   "Add unit tests with 90% coverage"
   → Completes with testing
   ```

2. **TDD Iteration:**
   - Write failing test
   - Implement minimal code to pass
   - Refactor
   - Repeat

3. **Interview Pattern:**
   - Ask Claude to propose approach
   - Review and provide feedback
   - Iterate on specific aspects
   - Finalize implementation

### 3.6 CI/CD Integration

**Command-Line Flags:**
```bash
# Non-interactive mode with prompt
claude -p "Review code for security issues"

# Structured JSON output
claude --output-format json -p "Analyze test coverage"

# With JSON schema validation
claude --json-schema schema.json -p "Extract API endpoints"
```

**Session Context Isolation:**
- **Generator session**: Creates initial code
- **Reviewer session**: Reviews in fresh context (avoids reasoning bias)
- Pass prior review findings as context

**Batch Processing:**
```python
# Message Batches API
requests = [
    {
        "custom_id": "review-file-1",
        "params": {
            "model": "claude-3-5-sonnet-20241022",
            "messages": [{"role": "user", "content": "Review auth.py"}]
        }
    },
    # ... more requests
]

# 50% cost savings, 24-hour processing window
batch = client.messages.batches.create(requests=requests)
```

**Benefits:**
- 50% cost savings
- Process large volumes
- 24-hour completion window
- Track with `custom_id`

---

## 💬 Domain 4: Prompt Engineering & Structured Output (20%)

### 4.1 Explicit Criteria Over Vague Instructions

**Bad (Vague):**
```
"Make the code better"
"Improve performance"
"Fix the bugs"
```

**Good (Explicit):**
```
"Reduce API response time from 500ms to under 200ms by:
- Adding database indexes on user_id and created_at
- Implementing Redis caching for user profiles
- Using connection pooling"

"Fix authentication bug where users remain logged in after password reset:
- Clear all active sessions on password change
- Invalidate refresh tokens
- Send email notification of password change"
```

**Impact on Trust:**
- False positives erode developer trust
- Explicit criteria reduce false positives
- Measurable outcomes enable validation

### 4.2 Few-Shot Prompting

**When to Use:**
- Ambiguous formatting requirements
- Domain-specific patterns
- Consistent output structure

**Optimal Example Count:**
- **2-4 examples** for most cases
- More examples for highly ambiguous tasks
- Ensure examples cover edge cases

**Example Pattern:**
```
Extract product information from these descriptions:

Example 1:
Input: "MacBook Pro 16-inch, M3 Max, 64GB RAM, $3499"
Output: {"name": "MacBook Pro", "size": "16-inch", "processor": "M3 Max", "ram": "64GB", "price": 3499}

Example 2:
Input: "Dell XPS 15, Intel i9, 32GB, 1TB SSD - $2299"
Output: {"name": "Dell XPS", "size": "15-inch", "processor": "Intel i9", "ram": "32GB", "storage": "1TB SSD", "price": 2299}

Now extract from: "ThinkPad X1 Carbon, i7-1365U, 16GB, $1899"
```

### 4.3 Tool Use with JSON Schemas

**Schema Compliance:**
- **Guaranteed**: Structure matches schema (field names, types)
- **Not Guaranteed**: Semantic correctness (values may be wrong)

**Example:**
```python
tools = [{
    "name": "extract_invoice_data",
    "description": "Extract structured data from invoice",
    "input_schema": {
        "type": "object",
        "properties": {
            "invoice_number": {"type": "string"},
            "date": {"type": "string", "format": "date"},
            "total": {"type": "number"},
            "currency": {
                "type": "string",
                "enum": ["USD", "EUR", "GBP", "other"]
            },
            "line_items": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "description": {"type": "string"},
                        "quantity": {"type": "integer"},
                        "unit_price": {"type": "number"}
                    },
                    "required": ["description", "quantity", "unit_price"]
                }
            }
        },
        "required": ["invoice_number", "date", "total", "currency"]
    }
}]
```

**Schema Design Best Practices:**
- Use `required` for mandatory fields
- Use `enum` with "other" option for flexibility
- Add `description` for clarity
- Use `nullable` for optional values
- Consider `additionalProperties: false` for strict validation

### 4.4 Validation-Retry Loops

**Effective Pattern:**
```python
max_retries = 3
for attempt in range(max_retries):
    result = extract_data(document)
    
    validation_errors = validate(result)
    if not validation_errors:
        break
    
    # Append SPECIFIC errors to prompt
    messages.append({
        "role": "user",
        "content": f"Validation failed: {validation_errors}. Please correct these specific issues."
    })
```

**When Retries Are Ineffective:**
- Ambiguous requirements (clarify first)
- Missing information in source
- Schema too restrictive
- Fundamental misunderstanding (needs human intervention)

**Best Practices:**
- Provide specific error details
- Limit retry attempts (3-5 max)
- Escalate after max retries
- Track patterns with `detected_pattern` field

### 4.5 Batch Processing Strategy

**Synchronous vs Batch:**
- **Synchronous**: Blocking operations, immediate results needed
- **Batch**: Latency-tolerant, large volumes, cost-sensitive

**Use Cases:**
- **Batch**: Code reviews, documentation generation, test creation
- **Synchronous**: Interactive debugging, real-time assistance

### 4.6 Multi-Pass Review

**Self-Review Limitations:**
- Same session retains reasoning context
- Confirmation bias toward original approach
- May miss fundamental issues

**Multi-Pass Strategy:**
```
Pass 1 (Per-File Local Analysis):
- Review each file independently
- Check local correctness
- Identify file-specific issues

Pass 2 (Cross-File Integration):
- Fresh session with Pass 1 findings
- Check integration points
- Verify consistency across files
- Identify architectural issues
```

**Benefits:**
- Fresh perspective in Pass 2
- Catches integration issues
- Reduces confirmation bias
- Better architectural review

---

## 🧠 Domain 5: Context Management & Reliability (15%)

### 5.1 Context Management Challenges

**Progressive Summarization Risks:**
- Information loss through compression
- "Lost in the middle" effect (middle content deprioritized)
- Cumulative degradation over iterations

**Better Approaches:**
- **Case Facts Blocks**: Structured, persistent key information
- **Trimming Verbose Outputs**: Remove redundant tool results
- **Position-Aware Ordering**: Important info at start/end

**Example Case Facts:**
```markdown
## Case Facts
- Customer: John Doe (ID: 12345)
- Issue: Payment failed on 2024-01-15
- Order: #ORD-789 ($299.99)
- Previous attempts: 3 (all failed)
- Payment method: Visa ending 4242
- Error code: INSUFFICIENT_FUNDS
```

### 5.2 Escalation Patterns

**When to Escalate:**
- Customer demands beyond policy
- Policy gaps (no clear guidance)
- High-value transactions (over threshold)
- Legal/compliance concerns
- Complex multi-system issues

**Common Mistakes:**
- ❌ Sentiment-based escalation (sentiment ≠ complexity)
- ❌ Self-reported confidence scores (unreliable)
- ❌ Arbitrary thresholds without business logic

**Proper Escalation Logic:**
```python
def should_escalate(context):
    # Explicit business rules
    if context.refund_amount > 500:
        return True, "Refund exceeds $500 limit"
    
    if context.policy_gap_detected:
        return True, "No policy covers this scenario"
    
    if context.legal_concern:
        return True, "Potential legal implications"
    
    # Customer demands outside policy
    if context.customer_request not in allowed_actions:
        return True, "Request outside standard policy"
    
    return False, None
```

### 5.3 Error Propagation

**Structured Context vs Generic Errors:**

**Bad (Generic):**
```json
{
  "error": "Operation failed"
}
```

**Good (Structured):**
```json
{
  "isError": true,
  "errorCategory": "database_connection_failed",
  "isRetryable": true,
  "context": {
    "attempted_operation": "fetch_user_orders",
    "user_id": "12345",
    "database": "orders_db",
    "connection_pool_status": "exhausted"
  },
  "suggested_action": "Retry after 30 seconds or escalate if persists"
}
```

**Access Failures vs Empty Results:**
- **Access Failure**: Permission denied, authentication failed
  - Return error, don't retry
- **Empty Results**: Query returned no data
  - Valid result, not an error
  - Distinguish from failure

### 5.4 Local Recovery Before Escalation

**Recovery Hierarchy:**
1. **Local Recovery**: Agent attempts to fix (retry, alternative approach)
2. **Coordinator Escalation**: Pass to coordinator with context
3. **Human Escalation**: Requires human intervention

**Partial Results Pattern:**
```json
{
  "status": "partial_success",
  "completed": ["task_1", "task_2"],
  "failed": ["task_3"],
  "results": {
    "task_1": {...},
    "task_2": {...}
  },
  "failure_context": {
    "task_3": {
      "error": "API rate limit exceeded",
      "attempted_at": "2024-01-15T10:30:00Z",
      "retry_after": 60
    }
  }
}
```

### 5.5 Context Degradation in Extended Sessions

**Symptoms:**
- Repetitive questions
- Forgetting earlier decisions
- Inconsistent recommendations
- Reduced accuracy

**Mitigation Strategies:**
- **Scratchpad Files**: Persistent state outside context
  ```
  .claude/session-state.md
  ```
- **Session Reset**: Start fresh when context degrades
- **Subagent Delegation**: Offload verbose exploration
- **Crash Recovery Manifests**: Document state for recovery

**Using /compact:**
```bash
# Summarize and reduce context
/compact
```

### 5.6 Information Provenance and Synthesis

**Claim-Source Mapping:**
```json
{
  "claim": "Product X increases conversion by 25%",
  "sources": [
    {
      "document": "whitepaper_2023.pdf",
      "page": 12,
      "confidence": "high",
      "date": "2023-06-15"
    }
  ],
  "temporal_context": "Based on Q2 2023 data",
  "conflicts": [
    {
      "claim": "Product X increases conversion by 15%",
      "source": "case_study_2022.pdf",
      "note": "Earlier study with smaller sample size"
    }
  ]
}
```

**Synthesis Best Practices:**
- Distinguish well-established vs contested claims
- Preserve source characterizations
- Note temporal context (data may be outdated)
- Annotate conflicts explicitly
- Provide confidence levels

**Human Review Strategy:**
- **Stratified Sampling**: Review across document types
- **Field-Level Confidence**: Track accuracy by field
- **Accuracy by Doc Type**: Some formats more reliable

---

## ⚠️ Critical Anti-Patterns (Memorize These!)

These appear repeatedly as **wrong answers** on the exam:

1. **Parsing natural language for loop termination** instead of checking `stop_reason`
2. **Arbitrary iteration caps** as primary stopping mechanism
3. **Prompt-based enforcement** for critical business rules (use programmatic hooks)
4. **Self-reported confidence scores** for escalation decisions (unreliable)
5. **Sentiment-based escalation** (sentiment ≠ complexity)
6. **Generic error messages** ("Operation failed") hiding context
7. **Silently suppressing errors** (returning empty results as success)
8. **Too many tools per agent** (18 vs 4-5 degrades selection)
9. **Same-session self-review** (retains reasoning context bias)
10. **Aggregate accuracy metrics** masking per-document-type failures

---

## 📅 12-Week Study Plan (1 hour/day)

### Phase 1: Foundations (Weeks 1-4)

**Week 1: Agentic Loops & Core API**
- Day 1: Read exam guide, understand 6 scenarios
- Day 2: Study agentic loop lifecycle, stop_reason
- Day 3: Build minimal agentic loop with Agent SDK
- Day 4: Study anti-patterns
- Day 5: Practice Test 1 (10 questions)
- Day 6-7: Review and rest

**Week 2: Multi-Agent Orchestration**
- Day 1: Hub-and-spoke architecture
- Day 2: Task tool, allowedTools
- Day 3: Build coordinator + 2 subagents
- Day 4: Parallel execution, fork_session
- Day 5: Task decomposition pitfalls
- Day 6: Practice Test 2
- Day 7: Rest

**Week 3: Hooks, Workflows & Sessions**
- Day 1: PostToolUse hooks
- Day 2: Programmatic vs prompt-based enforcement
- Day 3: Build compliance hook
- Day 4: Session management
- Day 5: Task decomposition strategies
- Day 6: Practice Test 3
- Day 7: Rest

**Week 4: Tool Design & MCP**
- Day 1: Tool description best practices
- Day 2: Structured error responses
- Day 3: Tool distribution
- Day 4: MCP server config
- Day 5: Built-in tools
- Day 6: Practice Test 4
- Day 7: Rest

### Phase 2: Applied Knowledge (Weeks 5-8)

**Week 5: Claude Code Configuration**
- Day 1: CLAUDE.md hierarchy
- Day 2: @import syntax, rules directory
- Day 3: Custom slash commands vs skills
- Day 4: SKILL.md frontmatter
- Day 5: Path-specific rules
- Day 6: Practice Test 5
- Day 7: Rest

**Week 6: Plan Mode, Iteration & CI/CD**
- Day 1: Plan mode vs direct execution
- Day 2: Iterative refinement patterns
- Day 3: CI/CD integration
- Day 4: Session context isolation
- Day 5: Batch processing
- Day 6: Practice Test 6
- Day 7: Rest

**Week 7: Prompt Engineering**
- Day 1: Explicit criteria
- Day 2: Few-shot prompting
- Day 3: Tool use with JSON schemas
- Day 4: tool_choice options
- Day 5: Schema design
- Day 6: Practice Test 7
- Day 7: Rest

**Week 8: Validation & Multi-Pass**
- Day 1: Validation-retry loops
- Day 2: detected_pattern tracking
- Day 3: Batch processing strategy
- Day 4: Self-review limitations
- Day 5: Multi-pass review
- Day 6: Practice Test 8
- Day 7: Rest

### Phase 3: Context & Reliability + Exam Prep (Weeks 9-12)

**Week 9: Context Management**
- Day 1: Progressive summarization risks
- Day 2: Case facts blocks, trimming outputs
- Day 3: Escalation patterns
- Day 4: Error propagation
- Day 5: Local recovery
- Day 6: Practice Test 9
- Day 7: Rest

**Week 10: Advanced Context**
- Day 1: Context degradation
- Day 2: /compact, scratchpad files
- Day 3: Human review strategies
- Day 4: Information provenance
- Day 5: Synthesis best practices
- Day 6: Practice Test 10
- Day 7: Rest

**Week 11: Integration & Practice**
- Day 1-4: Complete exam guide exercises
- Day 5: Full Practice Exam 1 (50 questions)
- Day 6: Review wrong answers
- Day 7: Rest

**Week 12: Final Prep**
- Day 1-2: Review weakest domains
- Day 3: Full Practice Exam 2
- Day 4: Fill knowledge gaps
- Day 5: Full Practice Exam 3 (timed)
- Day 6: Light review
- Day 7: Exam day or rest

---

## 🎓 Exam Day Tips

1. **Read scenarios carefully** - Identify which of the 6 core scenarios
2. **Eliminate wrong answers** - Look for anti-patterns
3. **Check for deterministic vs probabilistic** - Hooks vs prompts
4. **Verify stop_reason usage** - Common trap in agentic questions
5. **Consider scope** - Local vs project vs user (MCP questions)
6. **Think about error handling** - Structured vs generic errors
7. **Session context** - Fresh session for review questions
8. **Tool count** - 4-5 optimal, 18+ degrades performance

---

## 📚 Additional Resources

**Official Documentation:**
- [Claude API Docs](https://platform.claude.com/docs)
- [Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview)
- [Claude Code Docs](https://code.claude.com/docs)
- [Model Context Protocol](https://modelcontextprotocol.io)

**Training Materials:**
- [Building with Claude API Course](https://anthropic.skilljar.com/claude-with-the-anthropic-api)
- [GitHub Training Repo](https://github.com/SGridworks/claude-certified-architect-training)

**Practice:**
- Build each of the 6 core scenarios
- Complete all exam guide exercises
- Take full practice exams under timed conditions

---

## ✅ Pre-Exam Checklist

- [ ] Completed all 10 practice tests
- [ ] Built all 6 core scenarios
- [ ] Memorized 10 critical anti-patterns
- [ ] Understand stop_reason lifecycle
- [ ] Know MCP scope hierarchy
- [ ] Understand tool_choice options
- [ ] Know when to use hooks vs prompts
- [ ] Understand multi-pass review benefits
- [ ] Know escalation patterns
- [ ] Understand error propagation best practices

---

**Good luck with your certification! 🚀**

Remember: The exam tests practical application, not just theory. Build the scenarios, make mistakes, learn from them, and you'll be well-prepared.
