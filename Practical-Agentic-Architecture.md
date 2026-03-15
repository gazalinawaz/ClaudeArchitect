# Practical Agentic Architecture: Customer Support System

**A complete, real-world implementation demonstrating all certification concepts**

This guide shows you how to build a production-ready agentic system using all the concepts from the Claude Certified Architect certification.

---

## 📋 Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Design](#architecture-design)
3. [Implementation](#implementation)
4. [Tool Design](#tool-design)
5. [Configuration](#configuration)
6. [Error Handling & Reliability](#error-handling--reliability)
7. [Testing & Validation](#testing--validation)
8. [Deployment](#deployment)

---

## 1. System Overview

### Business Requirements

**Scenario:** Automated customer support system for an e-commerce platform

**Capabilities:**
- Handle customer inquiries (order status, refunds, account issues)
- Process refunds up to $500 automatically
- Escalate complex cases to human agents
- Maintain conversation context across multiple interactions
- Generate support tickets for unresolved issues

### Success Metrics

- 70% of inquiries resolved without human intervention
- <2 minute average response time
- 95% customer satisfaction
- Zero unauthorized refunds >$500

---

## 2. Architecture Design

### 🗺️ System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    COORDINATOR AGENT                         │
│  - Receives customer inquiry                                 │
│  - Determines workflow                                       │
│  - Orchestrates subagents                                    │
│  - Synthesizes final response                                │
└───────────┬─────────────────────────────────────────────────┘
            │
            ├─────────────────┬─────────────────┬──────────────┐
            ▼                 ▼                 ▼              ▼
    ┌───────────────┐ ┌──────────────┐ ┌─────────────┐ ┌─────────────┐
    │  SEARCH       │ │  REFUND      │ │  ACCOUNT    │ │  TICKET     │
    │  AGENT        │ │  AGENT       │ │  AGENT      │ │  AGENT      │
    ├───────────────┤ ├──────────────┤ ├─────────────┤ ├─────────────┤
    │ - Find orders │ │ - Validate   │ │ - Reset pwd │ │ - Create    │
    │ - Track ship  │ │ - Process    │ │ - Update    │ │ - Escalate  │
    │ - Get history │ │ - Confirm    │ │ - Verify    │ │ - Track     │
    └───────┬───────┘ └──────┬───────┘ └──────┬──────┘ └──────┬──────┘
            │                │                │               │
            └────────────────┴────────────────┴───────────────┘
                                    │
                            ┌───────▼────────┐
                            │  TOOL LAYER    │
                            ├────────────────┤
                            │ - Database     │
                            │ - Payment API  │
                            │ - Email        │
                            │ - Ticketing    │
                            └────────────────┘
```

### Multi-Agent Pattern: Hub-and-Spoke

**Why this pattern?**
- Clear separation of concerns
- Specialized agents for specific domains
- Coordinator handles orchestration
- Easy to add new capabilities

---

## 3. Implementation

### Coordinator Agent

```python
# coordinator_agent.py

from anthropic import Anthropic
import json

class CoordinatorAgent:
    def __init__(self):
        self.client = Anthropic()
        self.conversation_history = []
        
    def process_inquiry(self, customer_message, customer_id):
        """
        Main entry point for customer inquiries.
        Implements the agentic loop with proper stop_reason handling.
        """
        # Add customer message to conversation
        self.conversation_history.append({
            "role": "user",
            "content": f"Customer ID: {customer_id}\nMessage: {customer_message}"
        })
        
        # Agentic loop
        while True:
            response = self.client.messages.create(
                model="claude-3-5-sonnet-20241022",
                max_tokens=4096,
                system=self._get_system_prompt(),
                messages=self.conversation_history,
                tools=self._get_coordinator_tools()
            )
            
            # Check stop_reason (NOT parsing text!)
            if response.stop_reason == "end_turn":
                # Task complete
                return self._extract_final_response(response)
            
            elif response.stop_reason == "tool_use":
                # Agent wants to use tools
                tool_results = self._execute_tools(response.content)
                
                # Add tool results to conversation
                self.conversation_history.append({
                    "role": "assistant",
                    "content": response.content
                })
                self.conversation_history.append({
                    "role": "user",
                    "content": tool_results
                })
                # Continue loop
                
            elif response.stop_reason == "max_tokens":
                # Response truncated - handle gracefully
                return {
                    "status": "error",
                    "message": "Response too long, please simplify your request"
                }
    
    def _get_system_prompt(self):
        """
        Explicit, detailed system prompt.
        Domain 4 concept: Explicit > Vague
        """
        return """You are a customer support coordinator for an e-commerce platform.

Your role:
1. Analyze customer inquiries
2. Delegate to specialized subagents using the Task tool
3. Synthesize results into helpful responses
4. Escalate when necessary

Available subagents:
- SearchAgent: Find orders, track shipments, get customer history
- RefundAgent: Process refunds (up to $500 automatically)
- AccountAgent: Password resets, account updates, verification
- TicketAgent: Create support tickets for complex issues

CRITICAL RULES (enforced by hooks):
- NEVER process refunds >$500 (requires manager approval)
- ALWAYS verify customer identity before account changes
- ESCALATE if: policy gap, legal concern, or high-value transaction

Escalation triggers:
- Refund request >$500
- Legal language (lawsuit, lawyer, sue)
- Policy not covered
- Customer requests manager

Response format:
- Be empathetic and professional
- Provide specific order numbers and dates
- Explain next steps clearly
- Set expectations for timing"""
    
    def _get_coordinator_tools(self):
        """
        Domain 2 concept: 4-5 tools optimal for selection quality
        """
        return [
            {
                "name": "Task",
                "description": "Spawn a specialized subagent to handle specific tasks. Use this to delegate work to SearchAgent, RefundAgent, AccountAgent, or TicketAgent.",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "agent": {
                            "type": "string",
                            "enum": ["SearchAgent", "RefundAgent", "AccountAgent", "TicketAgent"],
                            "description": "Which specialized agent to use"
                        },
                        "task": {
                            "type": "string",
                            "description": "Specific task for the agent"
                        },
                        "context": {
                            "type": "object",
                            "description": "Context to pass to subagent (customer_id, order_id, etc.)"
                        }
                    },
                    "required": ["agent", "task", "context"]
                }
            },
            {
                "name": "escalate_to_human",
                "description": "Escalate to human agent when: refund >$500, legal concern, policy gap, or customer requests manager.",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "reason": {
                            "type": "string",
                            "enum": ["high_value_transaction", "legal_concern", "policy_gap", "customer_request"],
                            "description": "Why escalation is needed"
                        },
                        "context": {
                            "type": "object",
                            "description": "Full context for human agent"
                        }
                    },
                    "required": ["reason", "context"]
                }
            }
        ]
    
    def _execute_tools(self, content):
        """
        Execute tool calls and return results.
        Domain 1 concept: Proper tool execution in agentic loop
        """
        results = []
        
        for block in content:
            if block.type == "tool_use":
                tool_name = block.name
                tool_input = block.input
                
                # Execute tool with error handling
                try:
                    if tool_name == "Task":
                        result = self._spawn_subagent(
                            tool_input["agent"],
                            tool_input["task"],
                            tool_input["context"]
                        )
                    elif tool_name == "escalate_to_human":
                        result = self._escalate(
                            tool_input["reason"],
                            tool_input["context"]
                        )
                    
                    results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps(result)
                    })
                    
                except Exception as e:
                    # Domain 5 concept: Structured error propagation
                    results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps({
                            "isError": True,
                            "errorCategory": "tool_execution_failed",
                            "isRetryable": False,
                            "message": str(e),
                            "context": {
                                "tool": tool_name,
                                "input": tool_input
                            }
                        })
                    })
        
        return results
    
    def _spawn_subagent(self, agent_type, task, context):
        """
        Domain 1 concept: Explicit context passing to subagents
        """
        # Import subagent classes
        from subagents import SearchAgent, RefundAgent, AccountAgent, TicketAgent
        
        agents = {
            "SearchAgent": SearchAgent(),
            "RefundAgent": RefundAgent(),
            "AccountAgent": AccountAgent(),
            "TicketAgent": TicketAgent()
        }
        
        agent = agents[agent_type]
        
        # Execute subagent task with explicit context
        return agent.execute(task, context)
    
    def _escalate(self, reason, context):
        """
        Domain 5 concept: Explicit escalation with full context
        """
        return {
            "escalated": True,
            "reason": reason,
            "context": context,
            "ticket_id": self._create_escalation_ticket(reason, context),
            "message": "This inquiry has been escalated to a human agent who will respond within 2 hours."
        }
```

### Refund Agent (Subagent Example)

```python
# subagents/refund_agent.py

from anthropic import Anthropic
import json

class RefundAgent:
    def __init__(self):
        self.client = Anthropic()
        self.max_auto_refund = 500  # Business rule
    
    def execute(self, task, context):
        """
        Specialized agent for refund processing.
        Domain 1 concept: Specialized subagent with focused responsibility
        """
        # Validate context
        if "order_id" not in context or "customer_id" not in context:
            return {
                "isError": True,
                "errorCategory": "invalid_input",
                "isRetryable": False,
                "message": "Missing required context: order_id and customer_id"
            }
        
        # Agentic loop for refund agent
        messages = [{
            "role": "user",
            "content": f"Task: {task}\nContext: {json.dumps(context)}"
        }]
        
        while True:
            response = self.client.messages.create(
                model="claude-3-5-sonnet-20241022",
                max_tokens=2048,
                system=self._get_system_prompt(),
                messages=messages,
                tools=self._get_tools()
            )
            
            if response.stop_reason == "end_turn":
                return self._extract_result(response)
            
            elif response.stop_reason == "tool_use":
                # Execute tools with hook validation
                tool_results = self._execute_tools_with_hooks(
                    response.content,
                    context
                )
                
                messages.append({
                    "role": "assistant",
                    "content": response.content
                })
                messages.append({
                    "role": "user",
                    "content": tool_results
                })
    
    def _get_system_prompt(self):
        return """You are a refund processing agent.

Your responsibilities:
1. Validate refund eligibility (order status, return window, condition)
2. Calculate refund amount
3. Process refund if eligible and within limits
4. Return detailed results

Rules:
- Check order exists and belongs to customer
- Verify order is within 30-day return window
- Confirm item condition allows refund
- Calculate refund (may include shipping)

IMPORTANT: You can only process refunds up to $500. Larger amounts will be blocked by the system."""
    
    def _get_tools(self):
        """
        Domain 2 concept: Clear, focused tools with good descriptions
        """
        return [
            {
                "name": "get_order",
                "description": "Retrieve order details including status, items, total, and date. Use to verify order exists and get refund amount.",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "order_id": {"type": "string"},
                        "customer_id": {"type": "string"}
                    },
                    "required": ["order_id", "customer_id"]
                }
            },
            {
                "name": "process_refund",
                "description": "Process a refund for an order. Returns confirmation or error. Amount must be ≤$500 (enforced by hook).",
                "input_schema": {
                    "type": "object",
                    "properties": {
                        "order_id": {"type": "string"},
                        "amount": {"type": "number"},
                        "reason": {"type": "string"}
                    },
                    "required": ["order_id", "amount", "reason"]
                }
            }
        ]
    
    def _execute_tools_with_hooks(self, content, context):
        """
        Domain 1 concept: PostToolUse hooks for critical business rules
        """
        results = []
        
        for block in content:
            if block.type == "tool_use":
                tool_name = block.name
                tool_input = block.input
                
                try:
                    # Execute tool
                    if tool_name == "get_order":
                        result = self._get_order(
                            tool_input["order_id"],
                            tool_input["customer_id"]
                        )
                    elif tool_name == "process_refund":
                        # PreToolUse Hook: Validate before execution
                        validation = self._pre_tool_use_hook(tool_name, tool_input)
                        if validation.get("blocked"):
                            result = validation
                        else:
                            result = self._process_refund(
                                tool_input["order_id"],
                                tool_input["amount"],
                                tool_input["reason"]
                            )
                            
                            # PostToolUse Hook: Validate after execution
                            result = self._post_tool_use_hook(tool_name, result)
                    
                    results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps(result)
                    })
                    
                except Exception as e:
                    results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": json.dumps({
                            "isError": True,
                            "errorCategory": "tool_execution_failed",
                            "isRetryable": True,
                            "message": str(e)
                        })
                    })
        
        return results
    
    def _pre_tool_use_hook(self, tool_name, tool_input):
        """
        Domain 1 concept: PreToolUse hook for validation
        Critical business rule: No refunds >$500
        """
        if tool_name == "process_refund":
            amount = tool_input.get("amount", 0)
            
            if amount > self.max_auto_refund:
                return {
                    "blocked": True,
                    "isError": True,
                    "errorCategory": "policy_violation",
                    "isRetryable": False,
                    "message": f"Refund amount ${amount} exceeds automatic limit of ${self.max_auto_refund}. Manager approval required.",
                    "suggestedAction": "escalate_to_manager",
                    "context": {
                        "amount": amount,
                        "limit": self.max_auto_refund,
                        "order_id": tool_input.get("order_id")
                    }
                }
        
        return {"blocked": False}
    
    def _post_tool_use_hook(self, tool_name, result):
        """
        Domain 1 concept: PostToolUse hook for result validation
        """
        # Add audit logging
        if tool_name == "process_refund" and result.get("success"):
            self._log_refund(result)
        
        return result
    
    def _get_order(self, order_id, customer_id):
        """
        Simulated database call.
        Domain 2 concept: Empty results ≠ Error
        """
        # In real implementation, query database
        orders = {
            "ORD-123": {
                "order_id": "ORD-123",
                "customer_id": "CUST-456",
                "total": 299.99,
                "status": "delivered",
                "date": "2024-02-15",
                "items": ["Laptop Stand", "USB-C Cable"]
            }
        }
        
        order = orders.get(order_id)
        
        if not order:
            # Empty result, NOT an error
            return {
                "success": True,
                "found": False,
                "message": f"No order found with ID {order_id}"
            }
        
        if order["customer_id"] != customer_id:
            return {
                "success": False,
                "isError": True,
                "errorCategory": "permission_denied",
                "message": "Order does not belong to this customer"
            }
        
        return {
            "success": True,
            "found": True,
            "order": order
        }
    
    def _process_refund(self, order_id, amount, reason):
        """
        Process refund (simulated).
        Domain 5 concept: Structured response with full context
        """
        # In real implementation, call payment API
        return {
            "success": True,
            "refund_id": f"REF-{order_id}-001",
            "amount": amount,
            "order_id": order_id,
            "status": "processed",
            "estimated_arrival": "3-5 business days",
            "message": f"Refund of ${amount} processed successfully"
        }
    
    def _log_refund(self, result):
        """Audit logging for compliance"""
        # In real implementation, log to audit system
        print(f"AUDIT: Refund processed - {result}")
```

---

## 4. Tool Design

### Tool Design Principles Applied

```python
# tools/customer_tools.py

"""
Domain 2 concepts demonstrated:
1. Clear, descriptive names
2. Detailed descriptions
3. Proper parameter types
4. Examples in descriptions
5. Combining related operations
"""

def get_tool_definitions():
    return [
        {
            "name": "search_customer",
            "description": """Search customer database by email, phone, or customer ID.
            
Returns customer profile with order history and account status.

Examples:
- search_customer(search_type="email", value="john@example.com")
- search_customer(search_type="phone", value="+1-555-0123")
- search_customer(search_type="customer_id", value="CUST-456")

Use when you need customer information for verification or context.""",
            "input_schema": {
                "type": "object",
                "properties": {
                    "search_type": {
                        "type": "string",
                        "enum": ["email", "phone", "customer_id"],
                        "description": "Type of search to perform"
                    },
                    "value": {
                        "type": "string",
                        "description": "Search value"
                    }
                },
                "required": ["search_type", "value"]
            }
        },
        {
            "name": "send_email",
            "description": """Send email to customer. Use for confirmations, updates, or responses.

Required: to, subject, body
Optional: cc, attachments

Email is sent immediately and confirmation is returned.""",
            "input_schema": {
                "type": "object",
                "properties": {
                    "to": {
                        "type": "string",
                        "description": "Recipient email address"
                    },
                    "subject": {
                        "type": "string",
                        "description": "Email subject line"
                    },
                    "body": {
                        "type": "string",
                        "description": "Email content (supports HTML)"
                    },
                    "cc": {
                        "type": "array",
                        "items": {"type": "string"},
                        "description": "CC recipients (optional)"
                    }
                },
                "required": ["to", "subject", "body"]
            }
        }
    ]
```

### Error Handling in Tools

```python
# tools/error_handling.py

"""
Domain 2 & 5 concepts: Structured error responses
"""

def handle_tool_error(error, tool_name, tool_input):
    """
    Standardized error handling for all tools.
    Returns structured error with category and retry guidance.
    """
    error_mappings = {
        "ConnectionError": {
            "errorCategory": "network_timeout",
            "isRetryable": True,
            "suggestedAction": "Retry with exponential backoff"
        },
        "AuthenticationError": {
            "errorCategory": "authentication_failed",
            "isRetryable": False,
            "suggestedAction": "Check API credentials"
        },
        "RateLimitError": {
            "errorCategory": "rate_limit_exceeded",
            "isRetryable": True,
            "suggestedAction": "Wait 60 seconds and retry"
        },
        "ValidationError": {
            "errorCategory": "invalid_input",
            "isRetryable": False,
            "suggestedAction": "Fix input parameters"
        }
    }
    
    error_type = type(error).__name__
    error_info = error_mappings.get(error_type, {
        "errorCategory": "unknown_error",
        "isRetryable": False,
        "suggestedAction": "Contact support"
    })
    
    return {
        "isError": True,
        "errorCategory": error_info["errorCategory"],
        "isRetryable": error_info["isRetryable"],
        "message": str(error),
        "suggestedAction": error_info["suggestedAction"],
        "context": {
            "tool": tool_name,
            "input": tool_input,
            "timestamp": datetime.now().isoformat()
        },
        "originalError": error_type
    }
```

---

## 5. Configuration

### CLAUDE.md Configuration

```markdown
# .claude/CLAUDE.md

# Customer Support System Configuration

## Critical Rules (< 200 lines total)

### Security & Compliance
- NEVER process refunds >$500 without manager approval
- ALWAYS verify customer identity before account changes
- NEVER share customer data across different customer sessions
- ALL payment operations must be logged for audit

### Escalation Rules
Escalate immediately if:
- Refund request >$500
- Legal language detected (lawsuit, lawyer, sue, legal action)
- Policy gap (situation not covered by procedures)
- Customer explicitly requests manager/supervisor
- Suspected fraud or security issue

### Response Standards
- Response time: <2 minutes
- Tone: Professional, empathetic, helpful
- Always include: Order numbers, dates, next steps
- Set clear expectations for timing

### Data Handling
- Customer context isolated per session
- Sensitive data (passwords, payment info) never logged
- PII handling complies with GDPR/CCPA

## Tech Stack
- Python 3.11+
- Anthropic Claude API
- PostgreSQL for data
- Redis for session management
- SendGrid for email

## File Structure
- `/coordinator` - Main coordinator agent
- `/subagents` - Specialized agents
- `/tools` - Tool implementations
- `/hooks` - Pre/Post tool use hooks
- `/config` - Configuration files
```

### MCP Configuration

```json
// .mcp.json

{
  "mcpServers": {
    "database": {
      "command": "python",
      "args": ["mcp_servers/database_server.py"],
      "env": {
        "DB_HOST": "${DB_HOST}",
        "DB_PORT": "${DB_PORT:-5432}",
        "DB_NAME": "${DB_NAME}",
        "DB_USER": "${DB_USER}",
        "DB_PASSWORD": "${DB_PASSWORD}"
      }
    },
    "payment": {
      "command": "python",
      "args": ["mcp_servers/payment_server.py"],
      "env": {
        "PAYMENT_API_KEY": "${PAYMENT_API_KEY}",
        "PAYMENT_ENV": "${PAYMENT_ENV:-sandbox}"
      }
    },
    "email": {
      "command": "python",
      "args": ["mcp_servers/email_server.py"],
      "env": {
        "SENDGRID_API_KEY": "${SENDGRID_API_KEY}"
      }
    }
  }
}
```

---

## 6. Error Handling & Reliability

### Local Recovery Before Escalation

```python
# reliability/recovery.py

"""
Domain 5 concept: Try local recovery before escalating
"""

class RecoveryManager:
    def __init__(self, max_retries=3):
        self.max_retries = max_retries
    
    def execute_with_recovery(self, func, *args, **kwargs):
        """
        Execute function with automatic retry for transient errors.
        Only escalate after local recovery attempts fail.
        """
        for attempt in range(self.max_retries):
            try:
                return func(*args, **kwargs)
            
            except Exception as e:
                error_info = self._classify_error(e)
                
                if not error_info["isRetryable"]:
                    # Not retryable, fail immediately
                    raise
                
                if attempt < self.max_retries - 1:
                    # Try local recovery
                    wait_time = self._exponential_backoff(attempt)
                    print(f"Attempt {attempt + 1} failed: {e}. Retrying in {wait_time}s...")
                    time.sleep(wait_time)
                else:
                    # Max retries exceeded, escalate
                    raise Exception(f"Max retries exceeded after {self.max_retries} attempts") from e
    
    def _exponential_backoff(self, attempt):
        """Calculate exponential backoff: 1s, 2s, 4s, 8s..."""
        return min(2 ** attempt, 60)  # Cap at 60 seconds
    
    def _classify_error(self, error):
        """Determine if error is retryable"""
        retryable_errors = [
            "ConnectionError",
            "TimeoutError",
            "RateLimitError",
            "ServiceUnavailable"
        ]
        
        return {
            "isRetryable": type(error).__name__ in retryable_errors
        }
```

### Circuit Breaker Pattern

```python
# reliability/circuit_breaker.py

"""
Domain 5 concept: Stop calling failing services
"""

import time
from enum import Enum

class CircuitState(Enum):
    CLOSED = "closed"      # Normal operation
    OPEN = "open"          # Service failing, reject calls
    HALF_OPEN = "half_open"  # Testing if service recovered

class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failures = 0
        self.last_failure_time = None
        self.state = CircuitState.CLOSED
    
    def call(self, func, *args, **kwargs):
        """Execute function with circuit breaker protection"""
        
        if self.state == CircuitState.OPEN:
            if self._should_attempt_reset():
                self.state = CircuitState.HALF_OPEN
            else:
                raise Exception("Circuit breaker OPEN - service unavailable")
        
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        """Reset circuit breaker on successful call"""
        self.failures = 0
        if self.state == CircuitState.HALF_OPEN:
            self.state = CircuitState.CLOSED
    
    def _on_failure(self):
        """Track failure and potentially open circuit"""
        self.failures += 1
        self.last_failure_time = time.time()
        
        if self.failures >= self.failure_threshold:
            self.state = CircuitState.OPEN
    
    def _should_attempt_reset(self):
        """Check if enough time has passed to try again"""
        if self.last_failure_time is None:
            return True
        
        return (time.time() - self.last_failure_time) > self.timeout
```

### Partial Results Handling

```python
# reliability/partial_results.py

"""
Domain 5 concept: Return partial success when some operations fail
"""

def process_batch_operations(operations):
    """
    Process multiple operations and return partial results.
    Don't fail everything if some succeed.
    """
    completed = []
    failed = []
    
    for op in operations:
        try:
            result = execute_operation(op)
            completed.append({
                "operation": op["id"],
                "result": result,
                "status": "success"
            })
        except Exception as e:
            failed.append({
                "operation": op["id"],
                "error": str(e),
                "errorCategory": classify_error(e),
                "status": "failed"
            })
    
    return {
        "status": "partial_success" if failed else "success",
        "completed": completed,
        "failed": failed,
        "summary": {
            "total": len(operations),
            "succeeded": len(completed),
            "failed": len(failed)
        }
    }
```

---

## 7. Testing & Validation

### Unit Tests for Hooks

```python
# tests/test_hooks.py

"""
Test critical business rules enforced by hooks
"""

import pytest
from subagents.refund_agent import RefundAgent

def test_refund_limit_enforcement():
    """
    Domain 1 concept: Hooks enforce critical rules
    Test that refunds >$500 are blocked
    """
    agent = RefundAgent()
    
    # Test within limit
    result = agent._pre_tool_use_hook("process_refund", {
        "amount": 499.99,
        "order_id": "ORD-123"
    })
    assert result["blocked"] == False
    
    # Test over limit
    result = agent._pre_tool_use_hook("process_refund", {
        "amount": 500.01,
        "order_id": "ORD-123"
    })
    assert result["blocked"] == True
    assert result["errorCategory"] == "policy_violation"
    assert "Manager approval required" in result["message"]

def test_escalation_triggers():
    """
    Domain 5 concept: Explicit escalation rules
    """
    from coordinator_agent import CoordinatorAgent
    
    agent = CoordinatorAgent()
    
    # Test legal language detection
    message = "I'm going to sue you if this isn't resolved"
    # Should trigger escalation
    
    # Test high-value transaction
    # Should escalate for refund >$500
    
    # Test sentiment (should NOT escalate)
    message = "I'm FURIOUS but just need password reset"
    # Should NOT escalate (simple task despite anger)
```

### Integration Tests

```python
# tests/test_integration.py

"""
End-to-end integration tests
"""

def test_complete_refund_workflow():
    """
    Test full workflow: Customer request → Coordinator → Refund Agent → Response
    """
    coordinator = CoordinatorAgent()
    
    result = coordinator.process_inquiry(
        customer_message="I want to return my order ORD-123 for a refund",
        customer_id="CUST-456"
    )
    
    assert result["status"] == "success"
    assert "refund" in result["message"].lower()
    assert "REF-" in result["message"]  # Refund ID present

def test_escalation_workflow():
    """
    Test that high-value refunds escalate properly
    """
    coordinator = CoordinatorAgent()
    
    result = coordinator.process_inquiry(
        customer_message="I need a refund for my $1,200 laptop order",
        customer_id="CUST-456"
    )
    
    assert result["escalated"] == True
    assert result["reason"] == "high_value_transaction"
    assert "ticket_id" in result
```

---

## 8. Deployment

### Production Checklist

```markdown
## Pre-Deployment Checklist

### Configuration
- [ ] All environment variables set
- [ ] Database connections tested
- [ ] API keys validated
- [ ] MCP servers configured

### Security
- [ ] Refund limit hook tested ($500 enforcement)
- [ ] Customer data isolation verified
- [ ] Audit logging enabled
- [ ] PII handling compliant

### Reliability
- [ ] Circuit breakers configured
- [ ] Retry logic tested
- [ ] Partial results handling verified
- [ ] Error propagation tested

### Monitoring
- [ ] Metrics collection enabled
- [ ] Alert thresholds configured
- [ ] Audit logs accessible
- [ ] Performance monitoring active

### Testing
- [ ] Unit tests passing (100% coverage on hooks)
- [ ] Integration tests passing
- [ ] Load testing completed
- [ ] Escalation workflows tested
```

### Monitoring & Metrics

```python
# monitoring/metrics.py

"""
Track system performance and reliability
"""

class MetricsCollector:
    def __init__(self):
        self.metrics = {
            "total_inquiries": 0,
            "auto_resolved": 0,
            "escalated": 0,
            "refunds_processed": 0,
            "refunds_blocked": 0,
            "average_response_time": [],
            "errors_by_category": {}
        }
    
    def track_inquiry(self, result, response_time):
        """Track inquiry metrics"""
        self.metrics["total_inquiries"] += 1
        self.metrics["average_response_time"].append(response_time)
        
        if result.get("escalated"):
            self.metrics["escalated"] += 1
        else:
            self.metrics["auto_resolved"] += 1
    
    def track_refund(self, amount, blocked=False):
        """Track refund metrics"""
        if blocked:
            self.metrics["refunds_blocked"] += 1
        else:
            self.metrics["refunds_processed"] += 1
    
    def get_automation_rate(self):
        """Calculate % of inquiries resolved without human"""
        total = self.metrics["total_inquiries"]
        if total == 0:
            return 0
        return (self.metrics["auto_resolved"] / total) * 100
    
    def get_average_response_time(self):
        """Calculate average response time"""
        times = self.metrics["average_response_time"]
        if not times:
            return 0
        return sum(times) / len(times)
```

---

## 🎯 Key Concepts Demonstrated

### Domain 1: Agentic Architecture
✅ Proper agentic loop with stop_reason checking
✅ Hub-and-spoke multi-agent pattern
✅ Explicit context passing between agents
✅ PreToolUse and PostToolUse hooks for critical rules
✅ Session management and context isolation

### Domain 2: Tool Design & MCP
✅ Clear, descriptive tool names and descriptions
✅ 4-5 tools per agent for optimal selection
✅ Structured error responses with categories
✅ Empty results handled separately from errors
✅ MCP configuration for external services

### Domain 3: Configuration & Workflows
✅ CLAUDE.md under 200 lines, focused on critical rules
✅ Clear separation of concerns (coordinator vs subagents)
✅ Proper tool restrictions per agent

### Domain 4: Prompt Engineering
✅ Explicit system prompts with clear requirements
✅ Specific success criteria and constraints
✅ Examples in tool descriptions

### Domain 5: Context Management & Reliability
✅ Structured error propagation with full context
✅ Explicit escalation rules (NOT sentiment-based)
✅ Local recovery before escalation
✅ Circuit breaker pattern
✅ Partial results handling
✅ Comprehensive audit logging

---

## 📚 Next Steps

1. **Study the code** - Understand how concepts are applied
2. **Run the examples** - Test the workflows
3. **Modify and experiment** - Try different scenarios
4. **Build your own** - Apply to your use case

This architecture demonstrates production-ready patterns you can adapt for any agentic system!

---

**Related Materials:**
- [Domain 1 Concepts](./Domain-1-Concepts.md) - Agentic Architecture
- [Domain 2 Concepts](./Domain-2-Concepts.md) - Tool Design
- [Domain 5 Concepts](./Domain-5-Concepts.md) - Reliability
- [Practice Scenarios](./Practice-Scenarios.md) - More examples
