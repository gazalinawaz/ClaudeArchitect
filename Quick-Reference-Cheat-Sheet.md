# Claude Certified Architect – Quick Reference Cheat Sheet

## 🎯 Exam Format
- **50 questions** | **72% to pass (720/1000)** | **~90 minutes**
- 4 of 6 scenarios randomly selected
- Multiple choice (1 correct, 3 distractors)

---

## 📊 Domain Weights
| Domain | Weight | Hours |
|--------|--------|-------|
| D1: Agentic Architecture & Orchestration | 27% | ~23h |
| D2: Tool Design & MCP Integration | 18% | ~15h |
| D3: Claude Code Configuration & Workflows | 20% | ~17h |
| D4: Prompt Engineering & Structured Output | 20% | ~17h |
| D5: Context Management & Reliability | 15% | ~13h |

---

## 🎬 6 Core Scenarios
1. **Customer Support Resolution** - Agent SDK, MCP, escalation
2. **Code Gen with Claude Code** - CLAUDE.md, plan mode, commands
3. **Multi-Agent Research** - Coordinator, subagents, context passing
4. **Developer Productivity** - Built-in tools, MCP, codebase exploration
5. **Claude Code CI/CD** - `-p` flag, batch API, multi-pass review
6. **Structured Data Extraction** - JSON schemas, validation-retry

---

## 🔄 D1: Agentic Architecture (27%)

### Stop Reasons (CRITICAL!)
```python
stop_reason == "tool_use"    # Execute tools, continue loop
stop_reason == "end_turn"    # Agent done, exit loop
stop_reason == "max_tokens"  # Truncated, handle gracefully
```

### Multi-Agent Pattern
- **Coordinator**: Orchestrates, delegates, synthesizes
- **Subagents**: Specialized, context-isolated
- **Task tool**: Spawn subagents (`allowedTools: ["Task"]`)
- **Parallel**: Multiple Task calls in one response
- **Context**: Explicit passing required (no shared state)

### Hooks vs Prompts
| Use Hooks (Programmatic) | Use Prompts (Guidance) |
|--------------------------|------------------------|
| Critical business rules | Stylistic preferences |
| Compliance requirements | Soft guidelines |
| Security policies | Contextual suggestions |
| Refund limits | Tone/format preferences |

### Session Management
- `--resume` - Continue with full context
- `fork_session` - Branch for exploration
- Named sessions - Organize parallel work
- `/compact` - Summarize and reduce context

---

## 🔧 D2: Tool Design & MCP (18%)

### Tool Count Sweet Spot
- **4-5 tools** ✅ Optimal selection
- **18+ tools** ❌ Degrades quality

### Error Response Structure
```json
{
  "isError": true,
  "errorCategory": "authentication_failed",
  "isRetryable": false,
  "message": "Specific error details",
  "context": {...}
}
```

### MCP Scope Hierarchy
```
Managed > Project (.mcp.json) > User (~/.claude.json) > Local
```

### MCP Scopes
| Scope | File | Use For |
|-------|------|---------|
| **Local** | `~/.claude.json` or `.claude/settings.local.json` | Personal, experimental, sensitive |
| **Project** | `.mcp.json` (root) | Team-shared, version controlled |
| **User** | `~/.claude.json` | Personal utilities across projects |

### Environment Variables
```json
"url": "${API_URL:-https://default.com}"
```
- `${VAR}` - Required variable
- `${VAR:-default}` - Optional with default

### Built-in Tools
- **Read** - View files
- **Edit** - Modify files (preferred over Write)
- **Write** - Create/overwrite
- **Bash** - Run commands
- **Grep** - Search content
- **Glob** - Find files by pattern

---

## ⚙️ D3: Claude Code Config (20%)

### CLAUDE.md Hierarchy
```
Directory-specific > Project > User
```

### Configuration Files
```
.claude/
  ├── CLAUDE.md              # Main config (<200 lines!)
  ├── rules/                 # Topic-specific rules
  │   ├── security.md
  │   └── testing.md
  ├── commands/              # Simple slash commands
  └── skills/                # Complex workflows
```

### SKILL.md Frontmatter
```yaml
---
context: fork              # Run in isolated session
allowed-tools: ["Read", "Edit"]
argument-hint: "file path"
---
```

### Path-Specific Rules
```yaml
---
paths:
  - "src/api/**/*.ts"
---
```

### Plan Mode vs Direct
| Plan Mode | Direct Execution |
|-----------|------------------|
| Complex refactoring | Well-understood tasks |
| High-risk changes | Low-risk changes |
| Unfamiliar codebase | Iterative development |
| Review before execute | Quick fixes |

### CI/CD Flags
```bash
claude -p "prompt"                    # Non-interactive
claude --output-format json           # JSON output
claude --json-schema schema.json      # Validate schema
```

### Batch API
- **50% cost savings**
- **24-hour window**
- Use `custom_id` for tracking

---

## 💬 D4: Prompt Engineering (20%)

### Explicit > Vague
❌ "Make it better"  
✅ "Reduce API response time from 500ms to <200ms by adding indexes"

### Few-Shot Prompting
- **2-4 examples** optimal
- Cover edge cases
- Ensure format consistency

### tool_choice Options
```python
{"type": "auto"}                      # Let Claude decide
{"type": "any"}                       # Force tool use
{"type": "tool", "name": "search"}    # Force specific tool
```

### Schema Design
```json
{
  "type": "object",
  "properties": {
    "status": {
      "type": "string",
      "enum": ["active", "inactive", "other"]
    }
  },
  "required": ["status"]
}
```
- Use `required` for mandatory
- `enum` with "other" for flexibility
- `nullable` for optional values

### Validation-Retry
- Max **3-5 retries**
- Provide **specific errors**
- Escalate after max attempts
- Track with `detected_pattern`

### Multi-Pass Review
**Pass 1**: Per-file local analysis  
**Pass 2**: Fresh session, cross-file integration

Benefits: Fresh perspective, catches integration issues

---

## 🧠 D5: Context & Reliability (15%)

### Context Management
- **Case Facts Blocks** - Structured key info
- **Trim Verbose Outputs** - Remove redundancy
- **Position-Aware** - Important at start/end
- **Scratchpad Files** - Persistent state

### Escalation Triggers
✅ Customer demands beyond policy  
✅ Policy gaps  
✅ High-value transactions  
✅ Legal/compliance concerns  
❌ Sentiment (sentiment ≠ complexity)  
❌ Self-reported confidence (unreliable)

### Error Propagation
**Bad**: `{"error": "Operation failed"}`

**Good**:
```json
{
  "isError": true,
  "errorCategory": "database_connection_failed",
  "isRetryable": true,
  "context": {
    "attempted_operation": "fetch_orders",
    "user_id": "12345"
  }
}
```

### Recovery Hierarchy
1. **Local Recovery** - Agent attempts fix
2. **Coordinator Escalation** - Pass to coordinator
3. **Human Escalation** - Requires human

### Partial Results
```json
{
  "status": "partial_success",
  "completed": ["task_1", "task_2"],
  "failed": ["task_3"],
  "failure_context": {...}
}
```

### Provenance Tracking
- Claim-source mapping
- Temporal context
- Conflict annotation
- Confidence levels
- Source characterization

---

## ⚠️ Top 10 Anti-Patterns (Wrong Answers!)

1. ❌ Parsing NL for loop termination (use `stop_reason`)
2. ❌ Arbitrary iteration caps as primary stop
3. ❌ Prompt-based enforcement for critical rules (use hooks)
4. ❌ Self-reported confidence for escalation
5. ❌ Sentiment-based escalation
6. ❌ Generic errors ("Operation failed")
7. ❌ Silently suppressing errors
8. ❌ 18+ tools per agent (use 4-5)
9. ❌ Same-session self-review
10. ❌ Aggregate metrics masking per-type failures

---

## 🎯 Exam Strategy

### Question Analysis
1. **Identify scenario** (which of 6?)
2. **Look for anti-patterns** (common wrong answers)
3. **Deterministic vs probabilistic** (hooks vs prompts)
4. **Check stop_reason usage** (agentic loop questions)
5. **Verify scope** (MCP configuration questions)
6. **Error handling** (structured vs generic)
7. **Session context** (fresh for review?)
8. **Tool count** (4-5 optimal)

### Time Management
- **~1.8 minutes per question**
- Flag difficult questions
- Return to flagged questions
- Don't overthink

### Common Traps
- Anti-patterns as distractors
- Overly complex solutions (simpler is often correct)
- Prompt-based when hooks needed
- Generic errors when structured needed
- Same-session review when fresh needed

---

## 📝 Quick Formulas

### Agentic Loop
```python
while response.stop_reason == "tool_use":
    execute_tools()
    append_results()
    continue_loop()
```

### MCP Config
```json
{
  "mcpServers": {
    "server-name": {
      "command": "/path/to/server",
      "args": [],
      "env": {"KEY": "${ENV_VAR}"}
    }
  }
}
```

### Tool Schema
```json
{
  "name": "tool_name",
  "description": "Clear description with examples",
  "input_schema": {
    "type": "object",
    "properties": {...},
    "required": [...]
  }
}
```

---

## ✅ Pre-Exam Checklist

- [ ] Know all 6 scenarios
- [ ] Memorized 10 anti-patterns
- [ ] Understand stop_reason lifecycle
- [ ] Know MCP scope hierarchy
- [ ] Understand tool_choice options
- [ ] Know hooks vs prompts distinction
- [ ] Understand multi-pass review
- [ ] Know escalation patterns
- [ ] Completed 3 full practice exams
- [ ] Scored 75%+ on practice exams

---

## 🚀 Day Before Exam

1. Review this cheat sheet
2. Review anti-patterns
3. Quick review of 6 scenarios
4. Light practice (don't cram)
5. Get good sleep
6. Stay confident!

**You've got this! 💪**
