# Domain 1: Agentic Architecture & Orchestration - Concept Guide

**Study this guide BEFORE attempting Domain 1 questions**

This guide explains all the core concepts you need to understand before tackling the practice questions.

---

## 📚 Table of Contents

1. [Agentic Loops](#agentic-loops)
2. [Stop Reasons](#stop-reasons)
3. [Multi-Agent Systems](#multi-agent-systems)
4. [Hooks (PreToolUse & PostToolUse)](#hooks)
5. [Session Management](#session-management)
6. [Tool Selection](#tool-selection)
7. [Workflow Patterns](#workflow-patterns)
8. [Error Handling & Escalation](#error-handling)

---

## 1. Agentic Loops

### What is an Agentic Loop?

An **agentic loop** is the cycle an AI agent goes through to complete tasks autonomously:

```
1. Receive task
2. Think: What do I need to do?
3. Decide: What action should I take?
4. Act: Use a tool or respond
5. Check: Am I done?
   → If NO: Go back to step 2
   → If YES: Return final response
```

### Example: Research Agent

**Task:** "Find the top 3 programming languages in 2024"

```
Loop 1:
- Think: "I need to search for current rankings"
- Decide: "Use search tool"
- Act: search_web("top programming languages 2024")
- Check: "Got results, but need to analyze" → Continue

Loop 2:
- Think: "I should extract and rank the top 3"
- Decide: "Process the data"
- Act: Analyze results
- Check: "I have the answer" → Done!

Response: "Top 3 are Python, JavaScript, Java"
```

### Key Concept: The Loop Continues Until Done

The agent keeps looping until it has completed the task. It doesn't stop after one action.

---

## 2. Stop Reasons

### What is stop_reason?

`stop_reason` is a **programmatic signal** that tells you WHY the agent stopped.

### The Two Main Stop Reasons

| stop_reason | Meaning | What Happens Next |
|-------------|---------|-------------------|
| **"tool_use"** | Agent wants to use a tool | Execute the tool, continue loop |
| **"end_turn"** | Agent is finished | Task complete, stop loop |

### Example Flow

```python
while True:
    response = agent.process(task)
    
    if response.stop_reason == "tool_use":
        # Agent needs to use a tool
        result = execute_tool(response.tool_call)
        # Continue loop with tool result
        
    elif response.stop_reason == "end_turn":
        # Agent is done!
        break
        
    # Other stop_reasons: "max_tokens", "stop_sequence"
```

### ❌ Anti-Pattern: Parsing Natural Language

**WRONG:**
```python
if "I'm done" in response.text:
    stop = True  # Unreliable!
```

**RIGHT:**
```python
if response.stop_reason == "end_turn":
    stop = True  # Definitive!
```

**Why?** The agent might say "I'm done analyzing" but still need to continue. `stop_reason` is the official signal.

---

## 3. Multi-Agent Systems

### What is a Multi-Agent System?

Instead of one agent doing everything, you have:
- **1 Coordinator Agent** - Orchestrates the workflow
- **Multiple Subagents** - Each specialized for specific tasks

### Hub-and-Spoke Architecture

```
        [Coordinator]
             |
    +--------+--------+
    |        |        |
[Search]  [Analyze] [Write]
Subagent  Subagent  Subagent
```

### Key Concept: Context Isolation

**Important:** Subagents DO NOT automatically share context!

```python
# ❌ WRONG: Assuming automatic sharing
coordinator.spawn(search_agent)
coordinator.spawn(analyze_agent)  # Won't see search results!

# ✓ RIGHT: Explicit context passing
search_results = coordinator.spawn(search_agent, task="search")
coordinator.spawn(analyze_agent, context=search_results)
```

### The Task Tool

To spawn subagents, the coordinator needs the **Task** tool:

```python
coordinator_tools = [
    "Task",  # Required for spawning subagents
    "Read",
    "Write"
]
```

### Coordinator Responsibilities

1. **Delegate** tasks to subagents
2. **Pass context** explicitly between subagents
3. **Synthesize** results from multiple subagents
4. **Handle errors** from subagent failures
5. **Decide** next steps based on results

---

## 4. Hooks (PreToolUse & PostToolUse)

### What are Hooks?

**Hooks** are programmatic functions that intercept tool calls to enforce rules.

### Two Types of Hooks

| Hook Type | When It Runs | Purpose |
|-----------|--------------|---------|
| **PreToolUse** | BEFORE tool executes | Validate inputs, check permissions |
| **PostToolUse** | AFTER tool executes | Validate outputs, enforce policies |

### Example: Refund Limit Enforcement

**Business Rule:** Never approve refunds over $500

**❌ WRONG: Using prompts**
```
System Prompt: "Never approve refunds over $500"
```
Problem: Agent might still approve it (prompts are probabilistic)

**✓ RIGHT: Using PostToolUse hook**
```python
def post_tool_use_hook(tool_name, tool_result):
    if tool_name == "process_refund":
        if tool_result.amount > 500:
            return {
                "error": True,
                "message": "Refund exceeds $500 limit. Manager approval required."
            }
    return tool_result
```

### When to Use Hooks vs Prompts

| Use Case | Method |
|----------|--------|
| **Critical business rules** | Hooks (programmatic) |
| **Compliance requirements** | Hooks (programmatic) |
| **Security policies** | Hooks (programmatic) |
| **Style preferences** | Prompts (guidance) |
| **Tone suggestions** | Prompts (guidance) |

**Key Principle:** If it MUST be enforced, use hooks. If it's a preference, use prompts.

---

## 5. Session Management

### What is a Session?

A **session** is a conversation with Claude that maintains context (memory of what was discussed).

### Session Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `--resume` | Continue previous session | Pick up where you left off |
| `fork_session` | Branch from current state | Explore alternatives |
| `/compact` | Summarize and reduce context | Context is getting too large |
| Reset (new session) | Start fresh | Context has degraded |

### Context Degradation

**Problem:** Long sessions accumulate context, leading to:
- Loss of focus
- Decreased accuracy
- Repetitive responses

**Solution:**
```python
if session_tokens > 50000:
    # Option 1: Compact context
    session.compact()
    
    # Option 2: Start fresh with summary
    summary = session.summarize()
    new_session = create_session(context=summary)
```

### Session Forking

**Use Case:** Explore multiple approaches without affecting main work

```python
# Main session
main_session.work_on("feature A")

# Fork to explore alternative
alt_session = main_session.fork()
alt_session.try_approach("alternative design")

# Main session unchanged
# Choose which approach to keep
```

### Named Sessions

Organize parallel work streams:

```python
sessions = {
    "frontend": Session("working on UI"),
    "backend": Session("working on API"),
    "testing": Session("writing tests")
}
```

---

## 6. Tool Selection

### The 4-5 Tool Rule

**Optimal:** 4-5 tools per agent

**Why?**
- Too few (1-2): Agent is limited
- Just right (4-5): Agent chooses effectively
- Too many (18+): Agent gets confused, selection quality degrades

### Example: Customer Support Agent

**❌ BAD: 18 tools**
```
search_by_email, search_by_phone, search_by_id, search_by_name,
get_order_by_id, get_orders_by_customer, get_recent_orders,
process_refund_full, process_refund_partial, cancel_order,
update_order, send_email, send_sms, create_ticket, update_ticket...
```

**✓ GOOD: 5 tools**
```
1. search_customer (handles email/phone/ID)
2. search_orders
3. process_refund
4. send_email
5. create_ticket
```

### How Agents Choose Tools

Agents use **tool descriptions** to decide:

```python
{
    "name": "search_customer",
    "description": "Searches customer database by email, phone, or ID. 
                   Returns customer profile with order history. 
                   Use when you need customer information.",
    "parameters": {...}
}
```

**Clear description** → Better tool selection

---

## 7. Workflow Patterns

### Pattern 1: Prompt Chaining

**What:** Predefined sequence of prompts

**When to use:** Known, sequential workflows

**Example: Code Review**
```
Step 1: Analyze code structure
Step 2: Check for bugs
Step 3: Review performance
Step 4: Generate report
```

Each step is a separate prompt, building on previous results.

### Pattern 2: Dynamic Adaptive Decomposition

**What:** Agent decides next steps based on results

**When to use:** Exploratory tasks, unknown workflows

**Example: Research**
```
Agent: "I'll search for X"
→ Finds 3 relevant sources
Agent: "Now I'll analyze these 3 sources"
→ Finds conflicting info
Agent: "I need to search for clarification"
→ Continues adapting...
```

### Pattern 3: Parallel Execution

**What:** Multiple subagents work simultaneously

**How:**
```python
# Coordinator makes multiple Task calls in one response
coordinator_response = {
    "tool_calls": [
        {"tool": "Task", "task": "search articles"},
        {"tool": "Task", "task": "search papers"},
        {"tool": "Task", "task": "search reports"}
    ]
}
# All 3 subagents run in parallel
```

**When to use:** Independent tasks that can run concurrently

---

## 8. Error Handling & Escalation

### Structured Error Propagation

**❌ BAD: Generic errors**
```python
return "Error"
```

**✓ GOOD: Structured errors**
```python
return {
    "isError": True,
    "errorCategory": "database_connection_failed",
    "isRetryable": True,
    "context": {
        "attempted": "fetch customer data",
        "customer_id": "12345"
    },
    "suggestedAction": "Retry with exponential backoff"
}
```

### Error Categories

| Category | isRetryable | Example |
|----------|-------------|---------|
| `network_timeout` | True | Temporary network issue |
| `rate_limit_exceeded` | True | Wait and retry |
| `authentication_failed` | False | Invalid credentials |
| `invalid_input` | False | Bad email format |
| `resource_not_found` | False | Customer doesn't exist |

### Escalation: When to Escalate to Human

**✓ Escalate for:**
- Policy gaps (situation not covered by rules)
- High-value transactions (>$10,000)
- Legal concerns (threats, lawsuits)
- Explicit business rules

**❌ DON'T escalate based on:**
- Customer sentiment (angry ≠ complex)
- Self-reported confidence (unreliable)
- Response length

### Example: Escalation Logic

```python
def should_escalate(request):
    # ✓ Explicit business rules
    if request.amount > 10000:
        return True, "High-value transaction"
    
    if "lawyer" in request.text or "sue" in request.text:
        return True, "Legal concern"
    
    if not policy_covers(request):
        return True, "Policy gap"
    
    # ❌ Don't use these
    # if sentiment_score < 0.3:  # NO!
    # if confidence < 0.8:  # NO!
    
    return False, None
```

### Partial Results

When some subagents succeed and others fail:

```python
{
    "completed": [
        {"agent": "search", "result": {...}},
        {"agent": "analyze", "result": {...}}
    ],
    "failed": [
        {"agent": "summarize", "error": "timeout", "context": {...}}
    ],
    "status": "partial_success"
}
```

**Don't:** Fail entire workflow
**Do:** Return partial results with error context

---

## 🎯 Key Takeaways

### Critical Concepts to Remember

1. **stop_reason is definitive** - Don't parse natural language for termination
2. **Context is isolated** - Must be explicitly passed between subagents
3. **Hooks for critical rules** - Prompts for preferences
4. **4-5 tools optimal** - More tools degrade selection
5. **Structured errors** - Include category, context, isRetryable
6. **Explicit escalation rules** - Not sentiment or confidence based

### Common Anti-Patterns to Avoid

❌ Parsing "I'm done" from text
❌ Assuming automatic context sharing
❌ Using prompts for critical business rules
❌ Giving agents 18+ tools
❌ Generic error messages
❌ Sentiment-based escalation
❌ Using arbitrary iteration caps as primary stop mechanism

---

## 📖 Study Flow

**Now that you understand these concepts:**

1. ✅ Read this concept guide
2. ✅ Review the examples
3. ✅ Understand the anti-patterns
4. ➡️ **Now attempt Domain 1 Questions** → [Domain-1-Questions.md](./Domain-1-Questions.md)

**Study Tip:** Refer back to this guide when you encounter questions you don't understand!

---

**Next Concept Guide:** [Domain 2 Concepts →](./Domain-2-Concepts.md)
