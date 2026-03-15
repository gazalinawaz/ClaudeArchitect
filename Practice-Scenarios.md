# Claude Certified Architect – Practice Scenarios

## 🎯 How to Use This Document

Each scenario includes:
- **Context**: Background and requirements
- **Key Concepts Tested**: What domains are being evaluated
- **Common Mistakes**: Anti-patterns to avoid
- **Correct Approach**: Best practices solution
- **Code Examples**: Implementation patterns

Work through each scenario, implement the solution, and verify against the correct approach.

---

## Scenario 1: Customer Support Resolution Agent

### Context
You're building an AI agent for customer support that handles refund requests, order status inquiries, and account issues. The agent must:
- Process refunds up to $500 automatically
- Escalate refunds over $500 to human agents
- Track customer sentiment
- Maintain conversation context
- Use MCP tools for CRM integration

### Key Concepts Tested
- D1: Agentic loops, hooks, escalation
- D2: Tool design, MCP configuration
- D5: Context management, error handling

### Common Mistakes ❌
1. Using sentiment analysis to determine escalation
2. Prompt-based refund limit enforcement
3. Generic error messages
4. Self-reported confidence for escalation
5. Parsing natural language for loop termination

### Correct Approach ✅

**1. Agentic Loop with stop_reason:**
```python
from anthropic import Anthropic

client = Anthropic()

def run_support_agent(customer_query):
    messages = [{"role": "user", "content": customer_query}]
    
    while True:
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=4096,
            messages=messages,
            tools=tools
        )
        
        # Check stop_reason - NOT natural language parsing
        if response.stop_reason == "end_turn":
            return response.content[0].text
        
        elif response.stop_reason == "tool_use":
            # Execute tools with hook interception
            for block in response.content:
                if block.type == "tool_use":
                    result = execute_with_hooks(block)
                    messages.append({
                        "role": "user",
                        "content": [{
                            "type": "tool_result",
                            "tool_use_id": block.id,
                            "content": result
                        }]
                    })
```

**2. Programmatic Hook for Refund Limits:**
```python
def post_tool_use_hook(tool_name, tool_input, tool_result):
    """Programmatic enforcement - NOT prompt-based"""
    if tool_name == "process_refund":
        amount = tool_input.get("amount", 0)
        
        # Hard limit enforcement
        if amount > 500:
            return {
                "isError": True,
                "errorCategory": "policy_violation",
                "isRetryable": False,
                "message": f"Refund amount ${amount} exceeds $500 limit. Escalating to manager.",
                "escalate": True,
                "escalation_reason": "refund_limit_exceeded",
                "context": {
                    "customer_id": tool_input.get("customer_id"),
                    "order_id": tool_input.get("order_id"),
                    "requested_amount": amount
                }
            }
    
    return tool_result
```

**3. Structured Error Responses:**
```python
def search_customer(customer_id):
    try:
        customer = crm.get_customer(customer_id)
        return customer
    except AuthenticationError:
        return {
            "isError": True,
            "errorCategory": "authentication_failed",
            "isRetryable": False,
            "message": "CRM authentication failed. Check API credentials.",
            "context": {"customer_id": customer_id}
        }
    except RateLimitError:
        return {
            "isError": True,
            "errorCategory": "rate_limit_exceeded",
            "isRetryable": True,
            "message": "CRM rate limit exceeded. Retry in 60 seconds.",
            "retry_after": 60,
            "context": {"customer_id": customer_id}
        }
```

**4. Escalation Logic (NOT sentiment-based):**
```python
def should_escalate(context):
    """Explicit business rules - NOT sentiment or confidence"""
    
    # Policy-based escalation
    if context.get("refund_amount", 0) > 500:
        return True, "refund_limit_exceeded"
    
    # Policy gap detection
    if context.get("policy_gap_detected"):
        return True, "no_policy_coverage"
    
    # Legal concerns
    if context.get("legal_keywords_detected"):
        return True, "potential_legal_issue"
    
    # Customer request outside allowed actions
    allowed_actions = ["refund", "order_status", "account_update"]
    if context.get("requested_action") not in allowed_actions:
        return True, "unsupported_action"
    
    return False, None
```

**5. MCP Configuration (.mcp.json):**
```json
{
  "mcpServers": {
    "crm-server": {
      "command": "node",
      "args": ["./mcp-servers/crm-server.js"],
      "env": {
        "CRM_API_KEY": "${CRM_API_KEY}",
        "CRM_BASE_URL": "${CRM_BASE_URL:-https://crm.example.com}"
      }
    }
  }
}
```

---

## Scenario 2: Multi-Agent Research System

### Context
Build a research system with:
- **Coordinator Agent**: Decomposes research tasks
- **Search Subagent**: Finds relevant sources
- **Analysis Subagent**: Analyzes documents
- **Synthesis Subagent**: Generates final report

### Key Concepts Tested
- D1: Multi-agent orchestration, Task tool, context isolation
- D5: Error propagation, partial results

### Common Mistakes ❌
1. Shared context between subagents
2. Generic error propagation
3. Overly narrow task decomposition
4. Not using Task tool properly
5. Missing allowedTools configuration

### Correct Approach ✅

**1. Coordinator Configuration:**
```python
from anthropic_agent_sdk import Agent

coordinator = Agent(
    model="claude-3-5-sonnet-20241022",
    systemPrompt="""You are a research coordinator. 
    Decompose research tasks and delegate to specialized subagents:
    - Search subagent: Find relevant sources
    - Analysis subagent: Analyze documents
    - Synthesis subagent: Generate reports
    
    Use the Task tool to spawn subagents.
    Explicitly pass context - subagents have isolated contexts.""",
    allowedTools=["Task"]  # CRITICAL: Must include Task
)
```

**2. Parallel Subagent Execution:**
```python
# Coordinator can spawn multiple subagents in parallel
# by making multiple Task calls in single response

coordinator_prompt = """
Research quantum computing applications in cryptography.

Spawn these subagents in parallel:
1. Search subagent: Find academic papers on quantum cryptography
2. Search subagent: Find industry reports on quantum threats
3. Analysis subagent: Analyze current cryptographic vulnerabilities
"""

# Claude will make multiple Task tool calls in one response
# for parallel execution
```

**3. Explicit Context Passing:**
```python
# Coordinator passes results to synthesis subagent
synthesis_prompt = f"""
Synthesize research findings:

Search Results:
{search_results}

Analysis Results:
{analysis_results}

Generate comprehensive report with:
- Executive summary
- Key findings
- Recommendations
- Source citations
"""

synthesis_agent = Agent(
    model="claude-3-5-sonnet-20241022",
    systemPrompt="You synthesize research findings into reports.",
    allowedTools=["Write"]
)

report = synthesis_agent.run(synthesis_prompt)
```

**4. Error Propagation with Context:**
```python
def execute_subagent_task(task_type, task_input):
    try:
        result = subagent.run(task_input)
        return {
            "status": "success",
            "task_type": task_type,
            "result": result
        }
    except Exception as e:
        # Structured error with context
        return {
            "status": "error",
            "task_type": task_type,
            "isError": True,
            "errorCategory": classify_error(e),
            "isRetryable": is_retryable(e),
            "context": {
                "attempted_operation": task_type,
                "input_summary": task_input[:100],
                "error_details": str(e)
            },
            "suggested_action": "Retry with alternative approach or escalate"
        }
```

**5. Partial Results Pattern:**
```python
def coordinate_research(topic):
    tasks = [
        ("search_papers", f"Find papers on {topic}"),
        ("search_reports", f"Find industry reports on {topic}"),
        ("analyze_trends", f"Analyze trends in {topic}")
    ]
    
    results = {
        "completed": [],
        "failed": [],
        "results": {},
        "errors": {}
    }
    
    for task_type, task_input in tasks:
        result = execute_subagent_task(task_type, task_input)
        
        if result["status"] == "success":
            results["completed"].append(task_type)
            results["results"][task_type] = result["result"]
        else:
            results["failed"].append(task_type)
            results["errors"][task_type] = result
    
    # Return partial results if some tasks failed
    return {
        "status": "partial_success" if results["failed"] else "success",
        "completed_count": len(results["completed"]),
        "failed_count": len(results["failed"]),
        **results
    }
```

---

## Scenario 3: Code Generation with Claude Code

### Context
Configure Claude Code for a development team working on a React/TypeScript project with:
- Strict code standards
- Required testing
- Security policies
- Custom workflows

### Key Concepts Tested
- D3: CLAUDE.md configuration, skills, plan mode
- D4: Iterative refinement

### Common Mistakes ❌
1. CLAUDE.md over 200 lines (degrades effectiveness)
2. Not using path-specific rules
3. Skills without proper frontmatter
4. Using direct execution for complex refactoring
5. Not organizing rules by topic

### Correct Approach ✅

**1. Project CLAUDE.md (<200 lines):**
```markdown
# React TypeScript Project

## Code Standards
- TypeScript strict mode enabled
- Use functional components with hooks
- Props must have TypeScript interfaces
- No `any` types without explicit justification

## Testing Requirements
- Unit tests for all business logic (Jest)
- Integration tests for API endpoints
- Minimum 80% code coverage
- Tests must pass before commit

## Security Rules
- Never log sensitive data (passwords, tokens, API keys, PII)
- Validate all user inputs
- Use parameterized queries
- Sanitize HTML output

@import .claude/rules/api-design.md
@import .claude/rules/component-patterns.md
```

**2. Path-Specific Rules (.claude/rules/api-design.md):**
```markdown
---
paths:
  - "src/api/**/*.ts"
  - "src/routes/**/*.ts"
---

# API Design Rules

## Endpoint Standards
- All endpoints must have rate limiting
- Return proper HTTP status codes (200, 201, 400, 401, 403, 404, 500)
- Include request/response examples in JSDoc
- Use Zod for request validation

## Error Handling
- Return structured error responses
- Include error codes for client handling
- Log errors with context (request ID, user ID, timestamp)
```

**3. Refactoring Skill (.claude/skills/refactor-to-hooks.md):**
```markdown
---
context: fork
allowed-tools: ["Read", "Edit", "Bash"]
argument-hint: "path/to/component.tsx"
---

# Refactor Class Component to Hooks

Refactors React class components to functional components with hooks.

## Process
1. Read component file
2. Identify class-based patterns:
   - State → useState
   - Lifecycle methods → useEffect
   - Refs → useRef
   - Context → useContext
3. Convert to functional component
4. Replace lifecycle methods with appropriate hooks
5. Update tests
6. Run tests to verify: `npm test -- <component-name>`

## Example
Input: Class component with state and componentDidMount
Output: Functional component with useState and useEffect

## Validation
- All tests pass
- No TypeScript errors
- Props interface unchanged
- Behavior identical to original
```

**4. Plan Mode for Complex Changes:**
```bash
# Use plan mode for high-risk refactoring
claude --plan "Refactor authentication system to use JWT tokens"

# Review proposed changes before execution
# Understand impact across codebase
# Approve or modify plan
```

**5. Iterative Refinement Pattern:**
```bash
# Step 1: Basic structure
claude "Create user profile component with name and email fields"

# Step 2: Add features
claude "Add avatar upload with preview and validation"

# Step 3: Add error handling
claude "Add loading states and error handling for API failures"

# Step 4: Add tests
claude "Add unit tests with 90% coverage including edge cases"
```

---

## Scenario 4: Structured Data Extraction Pipeline

### Context
Extract structured data from invoices (PDF/images) with:
- JSON schema validation
- Validation-retry loops
- Batch processing
- Multi-pass review

### Key Concepts Tested
- D4: Tool use, JSON schemas, validation-retry, few-shot
- D5: Multi-pass review

### Common Mistakes ❌
1. Assuming semantic correctness from schema compliance
2. Infinite retry loops
3. Generic validation errors
4. Same-session self-review
5. Not using few-shot examples

### Correct Approach ✅

**1. Tool with JSON Schema:**
```python
extraction_tool = {
    "name": "extract_invoice_data",
    "description": "Extract structured data from invoice. Use few-shot examples for format guidance.",
    "input_schema": {
        "type": "object",
        "properties": {
            "invoice_number": {
                "type": "string",
                "description": "Invoice number (e.g., INV-2024-001)"
            },
            "date": {
                "type": "string",
                "format": "date",
                "description": "Invoice date in YYYY-MM-DD format"
            },
            "total": {
                "type": "number",
                "description": "Total amount as number (e.g., 1299.99)"
            },
            "currency": {
                "type": "string",
                "enum": ["USD", "EUR", "GBP", "other"],
                "description": "Currency code"
            },
            "vendor": {
                "type": "object",
                "properties": {
                    "name": {"type": "string"},
                    "address": {"type": "string"},
                    "tax_id": {"type": "string"}
                },
                "required": ["name"]
            },
            "line_items": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "description": {"type": "string"},
                        "quantity": {"type": "integer", "minimum": 1},
                        "unit_price": {"type": "number", "minimum": 0},
                        "total": {"type": "number"}
                    },
                    "required": ["description", "quantity", "unit_price"]
                }
            }
        },
        "required": ["invoice_number", "date", "total", "currency", "line_items"]
    }
}
```

**2. Few-Shot Prompting:**
```python
few_shot_prompt = """
Extract invoice data using these examples as format guidance:

Example 1:
Invoice: "INVOICE #12345 | Date: 2024-01-15 | Total: $1,299.99"
Output: {
  "invoice_number": "12345",
  "date": "2024-01-15",
  "total": 1299.99,
  "currency": "USD"
}

Example 2:
Invoice: "Invoice No: INV-2024-002 | 2024-02-20 | €850.00"
Output: {
  "invoice_number": "INV-2024-002",
  "date": "2024-02-20",
  "total": 850.00,
  "currency": "EUR"
}

Now extract from: {invoice_text}
"""
```

**3. Validation-Retry Loop (Max 3 attempts):**
```python
def extract_with_validation(invoice_text, max_retries=3):
    messages = [{"role": "user", "content": few_shot_prompt.format(invoice_text=invoice_text)}]
    
    for attempt in range(max_retries):
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=4096,
            messages=messages,
            tools=[extraction_tool]
        )
        
        # Extract tool use result
        for block in response.content:
            if block.type == "tool_use":
                extracted_data = block.input
                
                # Validate (schema compliance guaranteed, check semantics)
                validation_errors = validate_semantics(extracted_data, invoice_text)
                
                if not validation_errors:
                    return {"status": "success", "data": extracted_data}
                
                # Append SPECIFIC errors for retry
                messages.append({
                    "role": "user",
                    "content": f"""Validation failed with these specific errors:
{json.dumps(validation_errors, indent=2)}

Please correct:
{format_error_details(validation_errors)}

Original invoice: {invoice_text}"""
                })
                break
    
    # Max retries exceeded - escalate
    return {
        "status": "failed",
        "reason": "max_retries_exceeded",
        "last_errors": validation_errors,
        "requires_human_review": True
    }

def validate_semantics(data, original_text):
    """Validate semantic correctness (schema compliance already guaranteed)"""
    errors = []
    
    # Check line items total matches invoice total
    calculated_total = sum(item["quantity"] * item["unit_price"] for item in data["line_items"])
    if abs(calculated_total - data["total"]) > 0.01:
        errors.append({
            "field": "total",
            "error": "Line items total doesn't match invoice total",
            "expected": calculated_total,
            "actual": data["total"]
        })
    
    # Check date format and validity
    try:
        datetime.strptime(data["date"], "%Y-%m-%d")
    except ValueError:
        errors.append({
            "field": "date",
            "error": "Invalid date format or value",
            "value": data["date"]
        })
    
    return errors
```

**4. Batch Processing:**
```python
def process_invoices_batch(invoice_files):
    """Use Batch API for cost savings (50% off)"""
    
    requests = []
    for idx, file_path in enumerate(invoice_files):
        invoice_text = extract_text_from_pdf(file_path)
        
        requests.append({
            "custom_id": f"invoice-{idx}-{Path(file_path).stem}",
            "params": {
                "model": "claude-3-5-sonnet-20241022",
                "max_tokens": 4096,
                "messages": [{
                    "role": "user",
                    "content": few_shot_prompt.format(invoice_text=invoice_text)
                }],
                "tools": [extraction_tool]
            }
        })
    
    # Submit batch (24-hour processing window)
    batch = client.messages.batches.create(requests=requests)
    
    return batch.id
```

**5. Multi-Pass Review (Fresh Session):**
```python
def multi_pass_extraction(invoice_text):
    # Pass 1: Initial extraction
    extraction_result = extract_with_validation(invoice_text)
    
    if extraction_result["status"] != "success":
        return extraction_result
    
    # Pass 2: Fresh session review (avoids confirmation bias)
    review_prompt = f"""
Review this extracted invoice data for accuracy:

Original Invoice:
{invoice_text}

Extracted Data:
{json.dumps(extraction_result["data"], indent=2)}

Verify:
1. All line items present and accurate
2. Calculations correct
3. Dates valid
4. Vendor information complete
5. Currency correct

Report any discrepancies found.
"""
    
    # NEW session for fresh perspective
    review_response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=2048,
        messages=[{"role": "user", "content": review_prompt}]
    )
    
    return {
        "status": "reviewed",
        "data": extraction_result["data"],
        "review_notes": review_response.content[0].text
    }
```

---

## 🎯 Practice Exercise Checklist

For each scenario, verify you can:

### Scenario 1: Customer Support
- [ ] Implement agentic loop with stop_reason
- [ ] Create programmatic hook for refund limits
- [ ] Return structured error responses
- [ ] Implement policy-based escalation (not sentiment)
- [ ] Configure MCP server with environment variables

### Scenario 2: Multi-Agent Research
- [ ] Configure coordinator with Task tool
- [ ] Spawn parallel subagents
- [ ] Pass context explicitly between agents
- [ ] Handle partial results gracefully
- [ ] Propagate errors with full context

### Scenario 3: Claude Code
- [ ] Write concise CLAUDE.md (<200 lines)
- [ ] Create path-specific rules
- [ ] Build skill with proper frontmatter
- [ ] Use plan mode for complex changes
- [ ] Implement iterative refinement

### Scenario 4: Data Extraction
- [ ] Design JSON schema with validation
- [ ] Implement few-shot prompting
- [ ] Create validation-retry loop (max 3)
- [ ] Use Batch API for cost savings
- [ ] Implement multi-pass review with fresh session

---

## 📊 Self-Assessment

After completing all scenarios, rate your confidence (1-5):

| Concept | Confidence | Notes |
|---------|-----------|-------|
| Agentic loops with stop_reason | ___ | |
| Hooks vs prompts | ___ | |
| Multi-agent orchestration | ___ | |
| MCP configuration | ___ | |
| CLAUDE.md best practices | ___ | |
| Plan mode usage | ___ | |
| JSON schema design | ___ | |
| Validation-retry loops | ___ | |
| Multi-pass review | ___ | |
| Error propagation | ___ | |

**Target**: All concepts at 4+ before taking exam

---

## 🚀 Next Steps

1. **Build each scenario** from scratch
2. **Compare** your implementation to correct approach
3. **Identify gaps** in understanding
4. **Review** relevant study guide sections
5. **Rebuild** scenarios until confident
6. **Take practice exams** to validate knowledge

**Remember**: The exam tests practical application. Building these scenarios is the best preparation!
