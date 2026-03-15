# Domain 5: Context Management & Reliability - Concept Guide

**Study this guide BEFORE attempting Domain 5 questions**

This guide explains context management, escalation patterns, error handling, and reliability strategies.

---

## 📚 Table of Contents

1. [Context Management](#context-management)
2. [Progressive Summarization](#progressive-summarization)
3. [Escalation Patterns](#escalation-patterns)
4. [Error Propagation](#error-propagation)
5. [Information Provenance](#information-provenance)
6. [Reliability Strategies](#reliability-strategies)

---

## 1. Context Management

### What is Context?

**Context** is the conversation history and information Claude remembers during a session.

### The Context Window Problem

**Problem:** Context window has limits (e.g., 200K tokens)

**What happens when full:**
- Older messages get dropped
- Information loss
- Degraded performance

### Context Degradation

**Symptoms:**
- Loss of focus
- Repetitive responses
- Forgetting earlier instructions
- Decreased accuracy

**Causes:**
- Extended sessions (2+ hours)
- Accumulated context (50K+ tokens)
- Too much irrelevant information

### Solutions

#### Solution 1: /compact

**What it does:** Summarizes and reduces context

```bash
/compact
```

**When to use:** Context is large but session should continue

#### Solution 2: Fresh Session

**What it does:** Start new session with summary

```python
# Summarize current session
summary = session.summarize()

# Start fresh with summary
new_session = create_session(context=summary)
```

**When to use:** Context has degraded significantly

#### Solution 3: Scratchpad Files

**What it does:** Store state outside context window

```python
# Instead of keeping in context
context += large_exploration_results  # 100KB

# Use scratchpad
save_to_file("scratchpad.json", exploration_results)
context += "Exploration results saved to scratchpad.json"
```

**When to use:** Large intermediate data that doesn't need to be in context

#### Solution 4: Subagent Delegation

**What it does:** Delegate verbose tasks to subagents

```python
# Instead of exploring in main session
main_session.explore_codebase()  # Generates 200KB of notes

# Delegate to subagent
exploration_results = coordinator.spawn(
    explorer_agent,
    task="explore codebase"
)
# Main session only gets summary
```

---

## 2. Progressive Summarization

### What is Progressive Summarization?

Repeatedly summarizing context to compress it.

### The Process

```
Original: 10,000 tokens
↓ Summarize
Summary 1: 2,000 tokens
↓ Summarize
Summary 2: 500 tokens
↓ Summarize
Summary 3: 100 tokens
```

### The Risk: Information Loss

**Problem:** Each summarization loses details

```
Original: "User reported bug in login form where special characters 
in password field cause authentication to fail. Error occurs at 
auth.js line 45 in validatePassword function."

After 1 summary: "Login bug with special characters in passwords"

After 2 summaries: "Login issue reported"

After 3 summaries: "Bug reported"
```

**Critical details lost!**

### Solution: Case Facts Blocks

**Preserve critical information** that should never be summarized:

```markdown
## Case Facts (DO NOT SUMMARIZE)
- Customer ID: 12345
- Issue: Login fails with special chars in password
- Location: auth.js line 45, validatePassword()
- Impact: High - affects 15% of users
- Reported: 2024-03-15
```

**These facts persist** through all summarizations.

### When to Use Progressive Summarization

✓ **Good for:**
- Long sessions (2+ hours)
- Accumulated context (50K+ tokens)
- General conversation history

❌ **Not good for:**
- Critical information (use case facts)
- Recent context (last few messages)
- Active work in progress

---

## 3. Escalation Patterns

### What is Escalation?

Passing a request to a human when the agent can't handle it.

### When to Escalate

**✓ Escalate for:**

1. **Policy Gaps** - Situation not covered by rules
2. **High-Value Transactions** - Above threshold (e.g., >$10,000)
3. **Legal Concerns** - Threats, lawsuits, legal language
4. **Explicit Business Rules** - Defined escalation criteria

### When NOT to Escalate

**❌ Don't escalate based on:**

1. **Customer Sentiment** - Angry ≠ Complex
2. **Self-Reported Confidence** - Unreliable
3. **Response Length** - Long ≠ Complex
4. **Arbitrary Thresholds** - Without business justification

### Example: Sentiment vs Complexity

**Scenario 1: High Anger, Low Complexity**
```
Customer: "I'm FURIOUS! Reset my password NOW!"
Sentiment: Very negative
Complexity: Simple password reset
→ DON'T escalate (simple issue)
```

**Scenario 2: Calm, High Complexity**
```
Customer: "Hi, I'd like to process a $50,000 refund for order #12345"
Sentiment: Neutral
Complexity: High-value transaction
→ DO escalate (exceeds threshold)
```

### Escalation Logic

```python
def should_escalate(request):
    # ✓ Explicit business rules
    if request.amount > 10000:
        return True, "High-value transaction requires approval"
    
    if "lawyer" in request.text or "sue" in request.text:
        return True, "Legal concern"
    
    if not policy_covers(request):
        return True, "Policy gap - no defined procedure"
    
    # ❌ Don't use these
    # if sentiment_score < 0.3:  # NO!
    # if confidence_score < 0.8:  # NO!
    
    return False, None
```

### Escalation Response

```python
{
    "escalated": True,
    "reason": "High-value transaction ($15,000 exceeds $10,000 limit)",
    "context": {
        "customer_id": "12345",
        "request_type": "refund",
        "amount": 15000,
        "order_id": "ORD-789"
    },
    "suggested_action": "Manager approval required",
    "attempted_resolution": "Validated order exists and is eligible for refund"
}
```

---

## 4. Error Propagation

### Structured Error Propagation

Errors should include **full context** as they propagate through system layers.

### Error Structure

```python
{
    "isError": True,
    "errorCategory": "database_connection_failed",
    "isRetryable": True,
    "message": "Failed to connect to customer database",
    "context": {
        "layer": "data_access",
        "attempted": "fetch_customer",
        "customer_id": "12345",
        "timestamp": "2024-03-15T10:30:00Z"
    },
    "suggestedAction": "Retry with exponential backoff",
    "originalError": "Connection timeout after 5000ms"
}
```

### Error Categories

| Category | isRetryable | Meaning |
|----------|-------------|---------|
| `network_timeout` | true | Temporary network issue |
| `rate_limit_exceeded` | true | Too many requests |
| `service_unavailable` | true | Temporary outage |
| `database_connection_failed` | true | DB temporarily down |
| `authentication_failed` | false | Invalid credentials |
| `invalid_input` | false | Bad parameters |
| `resource_not_found` | false | Item doesn't exist |
| `permission_denied` | false | Unauthorized |

### Empty Results vs Errors

**Important distinction:**

```python
# ✓ Empty results (NOT an error)
{
    "success": True,
    "results": [],
    "message": "No customers found matching 'john@example.com'"
}

# ❌ Access failure (IS an error)
{
    "success": False,
    "errorCategory": "database_connection_failed",
    "message": "Could not access customer database"
}
```

**Rule:** No data found = Valid result. Can't access data = Error.

### Multi-Layer Error Propagation

```python
# Layer 1: Database
{
    "error": "Connection timeout",
    "layer": "database"
}

# Layer 2: Data Access (adds context)
{
    "error": "Connection timeout",
    "layer": "data_access",
    "attempted": "fetch_customer",
    "customer_id": "12345",
    "originalError": {...}
}

# Layer 3: Business Logic (adds more context)
{
    "error": "Connection timeout",
    "layer": "business_logic",
    "operation": "process_refund",
    "impact": "Cannot validate customer eligibility",
    "dataAccessError": {...}
}

# Layer 4: Coordinator (final context)
{
    "error": "Connection timeout",
    "layer": "coordinator",
    "workflow": "customer_support",
    "task": "process_refund_request",
    "businessLogicError": {...}
}
```

**Each layer adds context without losing previous information.**

---

## 5. Information Provenance

### What is Provenance?

**Provenance** = Tracking where information came from

### Why It Matters

- Verify accuracy
- Assess credibility
- Handle conflicts
- Audit trail

### Claim-Source Mapping

```python
{
    "claim": "Python is the most popular programming language in 2024",
    "sources": [
        {
            "source": "Stack Overflow Developer Survey 2024",
            "url": "https://...",
            "date": "2024-01-15",
            "confidence": "high",
            "type": "survey"
        },
        {
            "source": "TIOBE Index March 2024",
            "url": "https://...",
            "date": "2024-03-01",
            "confidence": "high",
            "type": "index"
        }
    ],
    "consensus": true
}
```

### Temporal Context

**Include dates** - Information ages

```python
{
    "claim": "COVID-19 vaccines are in development",
    "source": "WHO Report",
    "date": "2020-06-15",  # Important!
    "note": "This information is from 2020 and may be outdated"
}
```

### Conflict Annotation

When sources disagree:

```python
{
    "claim": "Best programming language for beginners",
    "sources": [
        {
            "source": "Source A",
            "answer": "Python",
            "reasoning": "Simple syntax, large community"
        },
        {
            "source": "Source B",
            "answer": "JavaScript",
            "reasoning": "Immediate visual feedback, web-focused"
        }
    ],
    "conflict": true,
    "resolution": "No consensus - depends on goals (web dev vs general programming)"
}
```

### Source Characterization

Distinguish source types:

```python
{
    "claim": "New drug shows promise",
    "sources": [
        {
            "type": "peer-reviewed study",
            "credibility": "high",
            "note": "Published in Nature, n=1000, double-blind"
        },
        {
            "type": "preliminary report",
            "credibility": "medium",
            "note": "Small sample size (n=50), not peer-reviewed"
        }
    ]
}
```

---

## 6. Reliability Strategies

### Strategy 1: Local Recovery Before Escalation

**Pattern:**
```python
def subagent_task():
    try:
        return execute_task()
    except RetryableError as e:
        # Try local recovery first
        for attempt in range(3):
            wait(exponential_backoff(attempt))
            try:
                return execute_task()
            except:
                continue
        
        # Only escalate after local recovery fails
        return escalate_to_coordinator(error_context)
```

### Strategy 2: Partial Results

**Don't fail everything if some parts succeed:**

```python
{
    "status": "partial_success",
    "completed": [
        {"task": "search", "result": {...}},
        {"task": "analyze", "result": {...}}
    ],
    "failed": [
        {"task": "summarize", "error": "timeout", "context": {...}}
    ],
    "summary": "2 of 3 tasks completed successfully"
}
```

### Strategy 3: Crash Recovery

**Save state for long-running operations:**

```python
# State manifest
{
    "workflow_id": "refactor-auth-123",
    "completed_steps": [
        "analyze_current_code",
        "design_new_structure",
        "refactor_auth_service"
    ],
    "current_step": "update_tests",
    "pending_steps": [
        "update_documentation",
        "deploy_changes"
    ],
    "context": {
        "files_modified": ["auth.js", "auth.service.js"],
        "tests_status": "in_progress"
    }
}
```

**If crash occurs:** Resume from current_step

### Strategy 4: Circuit Breaker

**Stop calling failing services:**

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failures = 0
        self.threshold = failure_threshold
        self.timeout = timeout
        self.state = "closed"  # closed, open, half-open
    
    def call(self, func):
        if self.state == "open":
            if time_since_open() > self.timeout:
                self.state = "half-open"
            else:
                raise CircuitOpenError("Service unavailable")
        
        try:
            result = func()
            if self.state == "half-open":
                self.state = "closed"
                self.failures = 0
            return result
        except:
            self.failures += 1
            if self.failures >= self.threshold:
                self.state = "open"
            raise
```

### Strategy 5: Graceful Degradation

**Maintain partial functionality when services fail:**

```python
def search_products(query):
    try:
        # Try primary search (with AI recommendations)
        return ai_enhanced_search(query)
    except AIServiceDown:
        # Fallback to basic search
        return basic_search(query)
    except DatabaseDown:
        # Fallback to cached results
        return cached_search(query)
    except AllServiceDown:
        # Minimal functionality
        return {"error": "Limited service", "suggestion": "Try again later"}
```

---

## 🎯 Key Takeaways

### Critical Concepts

1. **Context degrades** - Use /compact, fresh sessions, scratchpads
2. **Progressive summarization loses info** - Use case facts for critical data
3. **Escalate on business rules** - NOT sentiment or confidence
4. **Empty results ≠ Error** - Distinguish access failure from no data
5. **Structured errors** - Include category, context, isRetryable
6. **Track provenance** - Source, date, confidence for all claims
7. **Local recovery first** - Then escalate if needed

### Common Anti-Patterns

❌ Letting context grow unbounded
❌ Summarizing critical information
❌ Sentiment-based escalation
❌ Confidence-based escalation
❌ Treating empty results as errors
❌ Generic error messages
❌ No source tracking
❌ Immediate escalation without local recovery

### Reliability Checklist

- [ ] Context management strategy defined
- [ ] Case facts blocks for critical info
- [ ] Explicit escalation rules (not sentiment/confidence)
- [ ] Structured error propagation
- [ ] Empty results handled separately from errors
- [ ] Information provenance tracked
- [ ] Local recovery before escalation
- [ ] Partial results supported
- [ ] Crash recovery for long operations

---

## 📖 Study Flow

**Now that you understand these concepts:**

1. ✅ Read this concept guide
2. ✅ Review the examples
3. ✅ Understand reliability patterns
4. ➡️ **Now attempt Domain 5 Questions** → [Domain-5-Questions.md](./Domain-5-Questions.md)

---

**Previous:** [Domain 4 Concepts](./Domain-4-Concepts.md) | **Back to Overview:** [Practice Questions Overview](./Practice-Questions-Overview.md)
