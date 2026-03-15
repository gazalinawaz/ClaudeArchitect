# Practical Agentic Architecture: JavaScript/Node.js Implementation

**Complete customer support system using JavaScript/Node.js with all certification concepts**

This guide provides JavaScript implementations of all the Python examples from the certification, making it accessible for JavaScript developers.

---

## 📋 Table of Contents

1. [Setup & Dependencies](#setup--dependencies)
2. [Coordinator Agent](#coordinator-agent)
3. [Subagent Implementation](#subagent-implementation)
4. [Tool Design](#tool-design)
5. [Hooks Implementation](#hooks-implementation)
6. [Error Handling](#error-handling)
7. [Session Management](#session-management)
8. [Complete Example](#complete-example)

---

## 1. Setup & Dependencies

### Package Installation

```bash
npm install @anthropic-ai/sdk
npm install express redis ioredis
npm install dotenv winston
```

### package.json

```json
{
  "name": "agentic-customer-support",
  "version": "1.0.0",
  "type": "module",
  "dependencies": {
    "@anthropic-ai/sdk": "^0.20.0",
    "express": "^4.18.2",
    "ioredis": "^5.3.2",
    "dotenv": "^16.3.1",
    "winston": "^3.11.0"
  }
}
```

### Environment Configuration

```javascript
// config.js
import dotenv from 'dotenv';
dotenv.config();

export const config = {
  anthropic: {
    apiKey: process.env.ANTHROPIC_API_KEY,
    model: 'claude-3-5-sonnet-20241022',
    maxTokens: 4096,
    temperature: 0.0
  },
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: process.env.REDIS_PORT || 6379,
    ttl: 86400 // 24 hours
  },
  business: {
    maxAutoRefund: 500,
    refundWindowDays: 30
  }
};
```

---

## 2. Coordinator Agent

### Coordinator Implementation

```javascript
// coordinatorAgent.js
import Anthropic from '@anthropic-ai/sdk';
import { config } from './config.js';
import { SessionManager } from './sessionManager.js';
import { logger } from './logger.js';

export class CoordinatorAgent {
  constructor() {
    this.client = new Anthropic({ apiKey: config.anthropic.apiKey });
    this.sessionManager = new SessionManager();
    this.systemPrompt = this.buildSystemPrompt();
  }

  buildSystemPrompt() {
    return `You are a customer support coordinator agent for an e-commerce platform.

Your responsibilities:
1. Analyze customer inquiries and determine the appropriate workflow
2. Delegate tasks to specialized subagents
3. Synthesize responses from multiple subagents
4. Escalate to human agents when necessary

Available subagents:
- search_agent: Find orders, track shipments, get customer history
- refund_agent: Process refunds up to $500 (auto-escalate above)
- account_agent: Password resets, account updates, verification
- ticket_agent: Create support tickets, escalate complex issues

Escalation rules (CRITICAL - MUST FOLLOW):
- Refunds >$500: MUST escalate to human
- Legal language (lawsuit, lawyer, sue): MUST escalate
- Policy gaps: MUST escalate
- Explicit customer request for human: MUST escalate

Response format: Professional, empathetic, clear next steps.`;
  }

  async processRequest(customerId, message, sessionId = null) {
    try {
      // Load or create session
      const session = await this.sessionManager.getSession(sessionId, customerId);
      
      // Add user message to history
      session.messages.push({
        role: 'user',
        content: message
      });

      // Run agentic loop
      const result = await this.agenticLoop(session);

      // Save session
      await this.sessionManager.saveSession(session);

      return result;

    } catch (error) {
      logger.error('Coordinator error:', error);
      return {
        success: false,
        error: 'An error occurred processing your request',
        shouldEscalate: true
      };
    }
  }

  async agenticLoop(session) {
    const maxIterations = 10;
    let iteration = 0;
    let finalResponse = null;

    while (iteration < maxIterations) {
      iteration++;
      
      logger.info(`Agentic loop iteration ${iteration}`);

      const response = await this.client.messages.create({
        model: config.anthropic.model,
        max_tokens: config.anthropic.maxTokens,
        temperature: config.anthropic.temperature,
        system: this.systemPrompt,
        messages: session.messages,
        tools: this.getTools()
      });

      // Check stop_reason (CRITICAL: Use programmatic signal, not text parsing)
      if (response.stop_reason === 'end_turn') {
        // Agent is done
        finalResponse = this.extractResponse(response);
        session.messages.push({
          role: 'assistant',
          content: response.content
        });
        break;
      }

      if (response.stop_reason === 'tool_use') {
        // Execute tools and continue loop
        const toolResults = await this.executeTools(response.content);
        
        session.messages.push({
          role: 'assistant',
          content: response.content
        });
        
        session.messages.push({
          role: 'user',
          content: toolResults
        });
        
        continue;
      }

      if (response.stop_reason === 'max_tokens') {
        logger.warn('Response truncated due to max_tokens');
        break;
      }
    }

    if (iteration >= maxIterations) {
      logger.warn('Max iterations reached');
      return {
        success: false,
        message: 'Request too complex, escalating to human agent',
        shouldEscalate: true
      };
    }

    return finalResponse;
  }

  async executeTools(content) {
    const toolUses = content.filter(block => block.type === 'tool_use');
    const results = [];

    for (const toolUse of toolUses) {
      const result = await this.executeTool(toolUse);
      results.push({
        type: 'tool_result',
        tool_use_id: toolUse.id,
        content: JSON.stringify(result)
      });
    }

    return results;
  }

  async executeTool(toolUse) {
    const { name, input } = toolUse;
    
    logger.info(`Executing tool: ${name}`, input);

    // Route to appropriate subagent
    switch (name) {
      case 'spawn_search_agent':
        return await this.spawnSubagent('search', input);
      case 'spawn_refund_agent':
        return await this.spawnSubagent('refund', input);
      case 'spawn_account_agent':
        return await this.spawnSubagent('account', input);
      case 'spawn_ticket_agent':
        return await this.spawnSubagent('ticket', input);
      default:
        return {
          success: false,
          error: `Unknown tool: ${name}`
        };
    }
  }

  async spawnSubagent(agentType, context) {
    // Import subagent dynamically
    const { createSubagent } = await import('./subagents/index.js');
    const subagent = createSubagent(agentType);
    
    // Execute subagent with explicit context passing
    return await subagent.execute(context);
  }

  getTools() {
    return [
      {
        name: 'spawn_search_agent',
        description: 'Search for orders, track shipments, get customer history. Use when customer asks about order status, tracking, or history.',
        input_schema: {
          type: 'object',
          properties: {
            task: { type: 'string', description: 'What to search for' },
            customerId: { type: 'string', description: 'Customer ID' },
            orderId: { type: 'string', description: 'Order ID (optional)' }
          },
          required: ['task', 'customerId']
        }
      },
      {
        name: 'spawn_refund_agent',
        description: 'Process refund requests. CRITICAL: Auto-escalates refunds >$500. Use when customer requests refund.',
        input_schema: {
          type: 'object',
          properties: {
            orderId: { type: 'string', description: 'Order ID to refund' },
            reason: { type: 'string', description: 'Refund reason' },
            customerId: { type: 'string', description: 'Customer ID' }
          },
          required: ['orderId', 'reason', 'customerId']
        }
      },
      {
        name: 'spawn_account_agent',
        description: 'Handle account-related tasks: password reset, profile updates, verification. Use for account issues.',
        input_schema: {
          type: 'object',
          properties: {
            task: { type: 'string', description: 'Account task to perform' },
            customerId: { type: 'string', description: 'Customer ID' }
          },
          required: ['task', 'customerId']
        }
      },
      {
        name: 'spawn_ticket_agent',
        description: 'Create support tickets for complex issues or escalations. Use when issue cannot be resolved automatically.',
        input_schema: {
          type: 'object',
          properties: {
            issue: { type: 'string', description: 'Issue description' },
            priority: { type: 'string', enum: ['low', 'medium', 'high', 'urgent'] },
            customerId: { type: 'string', description: 'Customer ID' }
          },
          required: ['issue', 'priority', 'customerId']
        }
      }
    ];
  }

  extractResponse(response) {
    const textContent = response.content.find(block => block.type === 'text');
    return {
      success: true,
      message: textContent?.text || 'Request processed',
      shouldEscalate: false
    };
  }
}
```

---

## 3. Subagent Implementation

### Refund Agent (Example Subagent)

```javascript
// subagents/refundAgent.js
import Anthropic from '@anthropic-ai/sdk';
import { config } from '../config.js';
import { ToolService } from '../toolService.js';
import { logger } from '../logger.js';

export class RefundAgent {
  constructor() {
    this.client = new Anthropic({ apiKey: config.anthropic.apiKey });
    this.toolService = new ToolService();
    this.systemPrompt = this.buildSystemPrompt();
  }

  buildSystemPrompt() {
    return `You are a refund processing agent.

Your responsibilities:
1. Validate refund eligibility (within 30 days, valid order)
2. Calculate refund amount
3. Process refunds up to $500
4. CRITICAL: Refunds >$500 MUST be escalated (enforced by hook)

Available tools (4 total - optimal):
- get_order: Retrieve order details
- validate_refund_eligibility: Check if refund is allowed
- calculate_refund_amount: Determine refund amount
- process_refund: Execute the refund (hook enforces $500 limit)

Always:
- Verify customer identity
- Check refund eligibility
- Explain refund timeline
- Provide confirmation`;
  }

  async execute(context) {
    const { orderId, reason, customerId } = context;

    try {
      const messages = [
        {
          role: 'user',
          content: `Process refund for order ${orderId}. Customer: ${customerId}. Reason: ${reason}`
        }
      ];

      // Run agentic loop with local recovery
      const result = await this.agenticLoopWithRecovery(messages);
      return result;

    } catch (error) {
      logger.error('Refund agent error:', error);
      
      // Structured error propagation
      return {
        success: false,
        errorCategory: 'agent_execution_failed',
        isRetryable: false,
        message: 'Failed to process refund',
        context: { orderId, customerId },
        suggestedAction: 'Escalate to human agent'
      };
    }
  }

  async agenticLoopWithRecovery(messages, maxRetries = 3) {
    let attempt = 0;

    while (attempt < maxRetries) {
      attempt++;

      try {
        return await this.agenticLoop(messages);
      } catch (error) {
        logger.warn(`Attempt ${attempt} failed:`, error);

        if (attempt >= maxRetries) {
          throw error;
        }

        // Exponential backoff
        await this.sleep(Math.pow(2, attempt) * 1000);
      }
    }
  }

  async agenticLoop(messages) {
    const maxIterations = 5;
    let iteration = 0;

    while (iteration < maxIterations) {
      iteration++;

      const response = await this.client.messages.create({
        model: config.anthropic.model,
        max_tokens: 2048,
        temperature: 0.0,
        system: this.systemPrompt,
        messages: messages,
        tools: this.getTools()
      });

      if (response.stop_reason === 'end_turn') {
        return this.extractResult(response);
      }

      if (response.stop_reason === 'tool_use') {
        const toolResults = await this.executeToolsWithHooks(response.content);
        
        messages.push({
          role: 'assistant',
          content: response.content
        });
        
        messages.push({
          role: 'user',
          content: toolResults
        });
        
        continue;
      }
    }

    return {
      success: false,
      message: 'Max iterations reached',
      partialResults: messages
    };
  }

  async executeToolsWithHooks(content) {
    const toolUses = content.filter(block => block.type === 'tool_use');
    const results = [];

    for (const toolUse of toolUses) {
      // PreToolUse Hook
      const preHookResult = await this.toolService.preToolUseHook(
        toolUse.name,
        toolUse.input
      );

      if (preHookResult.blocked) {
        results.push({
          type: 'tool_result',
          tool_use_id: toolUse.id,
          content: JSON.stringify({
            success: false,
            error: preHookResult.reason,
            blocked: true
          })
        });
        continue;
      }

      // Execute tool
      const toolResult = await this.toolService.executeTool(
        toolUse.name,
        toolUse.input
      );

      // PostToolUse Hook
      const postHookResult = await this.toolService.postToolUseHook(
        toolUse.name,
        toolResult
      );

      results.push({
        type: 'tool_result',
        tool_use_id: toolUse.id,
        content: JSON.stringify(postHookResult)
      });
    }

    return results;
  }

  getTools() {
    return [
      {
        name: 'get_order',
        description: 'Retrieve order details including items, total, date, and status',
        input_schema: {
          type: 'object',
          properties: {
            orderId: { type: 'string' },
            customerId: { type: 'string' }
          },
          required: ['orderId', 'customerId']
        }
      },
      {
        name: 'validate_refund_eligibility',
        description: 'Check if order is eligible for refund (within 30 days, not already refunded)',
        input_schema: {
          type: 'object',
          properties: {
            orderId: { type: 'string' }
          },
          required: ['orderId']
        }
      },
      {
        name: 'calculate_refund_amount',
        description: 'Calculate refund amount based on order and items',
        input_schema: {
          type: 'object',
          properties: {
            orderId: { type: 'string' },
            items: { 
              type: 'array',
              items: { type: 'string' },
              description: 'Item IDs to refund (optional, defaults to all)'
            }
          },
          required: ['orderId']
        }
      },
      {
        name: 'process_refund',
        description: 'Process the refund. CRITICAL: Amounts >$500 will be blocked by hook and escalated.',
        input_schema: {
          type: 'object',
          properties: {
            orderId: { type: 'string' },
            amount: { type: 'number' },
            reason: { type: 'string' }
          },
          required: ['orderId', 'amount', 'reason']
        }
      }
    ];
  }

  extractResult(response) {
    const textContent = response.content.find(block => block.type === 'text');
    return {
      success: true,
      message: textContent?.text || 'Refund processed',
      agent: 'refund'
    };
  }

  sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

---

## 4. Tool Design

### Tool Service with Hooks

```javascript
// toolService.js
import { config } from './config.js';
import { logger } from './logger.js';
import { DatabaseClient } from './database.js';

export class ToolService {
  constructor() {
    this.db = new DatabaseClient();
    this.circuitBreaker = new Map();
  }

  // PreToolUse Hook - Validate BEFORE execution
  async preToolUseHook(toolName, input) {
    logger.info(`PreToolUse hook: ${toolName}`, input);

    // CRITICAL: Enforce $500 refund limit
    if (toolName === 'process_refund') {
      if (input.amount > config.business.maxAutoRefund) {
        logger.warn(`Refund blocked: $${input.amount} exceeds $${config.business.maxAutoRefund} limit`);
        return {
          blocked: true,
          reason: `Refund amount $${input.amount} exceeds automatic approval limit of $${config.business.maxAutoRefund}. Manager approval required.`,
          escalate: true
        };
      }
    }

    // Check circuit breaker
    if (this.isCircuitOpen(toolName)) {
      return {
        blocked: true,
        reason: `Circuit breaker open for ${toolName}. Service temporarily unavailable.`,
        isRetryable: true
      };
    }

    return { blocked: false };
  }

  // PostToolUse Hook - Validate AFTER execution
  async postToolUseHook(toolName, result) {
    logger.info(`PostToolUse hook: ${toolName}`, result);

    // Audit logging for refunds
    if (toolName === 'process_refund' && result.success) {
      await this.auditLog({
        event: 'refund_processed',
        toolName,
        result,
        timestamp: new Date().toISOString()
      });
    }

    // Track failures for circuit breaker
    if (!result.success) {
      this.recordFailure(toolName);
    } else {
      this.recordSuccess(toolName);
    }

    return result;
  }

  // Tool Execution
  async executeTool(toolName, input) {
    try {
      switch (toolName) {
        case 'get_order':
          return await this.getOrder(input);
        case 'validate_refund_eligibility':
          return await this.validateRefundEligibility(input);
        case 'calculate_refund_amount':
          return await this.calculateRefundAmount(input);
        case 'process_refund':
          return await this.processRefund(input);
        default:
          return {
            success: false,
            errorCategory: 'unknown_tool',
            isRetryable: false,
            message: `Unknown tool: ${toolName}`
          };
      }
    } catch (error) {
      logger.error(`Tool execution error: ${toolName}`, error);
      
      // Structured error response
      return {
        success: false,
        errorCategory: this.categorizeError(error),
        isRetryable: this.isRetryable(error),
        message: error.message,
        context: { toolName, input },
        suggestedAction: this.suggestAction(error)
      };
    }
  }

  // Tool Implementations
  async getOrder({ orderId, customerId }) {
    const order = await this.db.query(
      'SELECT * FROM orders WHERE order_id = $1 AND customer_id = $2',
      [orderId, customerId]
    );

    if (!order) {
      // Empty result is NOT an error
      return {
        success: true,
        results: [],
        message: `No order found with ID: ${orderId}`
      };
    }

    return {
      success: true,
      results: order
    };
  }

  async validateRefundEligibility({ orderId }) {
    const order = await this.db.query(
      'SELECT * FROM orders WHERE order_id = $1',
      [orderId]
    );

    if (!order) {
      return {
        success: false,
        eligible: false,
        reason: 'Order not found'
      };
    }

    const orderDate = new Date(order.created_at);
    const daysSinceOrder = (Date.now() - orderDate.getTime()) / (1000 * 60 * 60 * 24);

    if (daysSinceOrder > config.business.refundWindowDays) {
      return {
        success: true,
        eligible: false,
        reason: `Order is ${Math.floor(daysSinceOrder)} days old. Refund window is ${config.business.refundWindowDays} days.`
      };
    }

    if (order.status === 'refunded') {
      return {
        success: true,
        eligible: false,
        reason: 'Order already refunded'
      };
    }

    return {
      success: true,
      eligible: true,
      reason: 'Order is eligible for refund'
    };
  }

  async calculateRefundAmount({ orderId, items = null }) {
    const order = await this.db.query(
      'SELECT * FROM orders WHERE order_id = $1',
      [orderId]
    );

    if (!order) {
      return {
        success: false,
        errorCategory: 'resource_not_found',
        isRetryable: false,
        message: 'Order not found'
      };
    }

    let refundAmount = order.total;

    if (items && items.length > 0) {
      // Partial refund
      const orderItems = JSON.parse(order.items);
      refundAmount = orderItems
        .filter(item => items.includes(item.id))
        .reduce((sum, item) => sum + item.price, 0);
    }

    return {
      success: true,
      amount: refundAmount,
      currency: 'USD'
    };
  }

  async processRefund({ orderId, amount, reason }) {
    // This would integrate with payment gateway
    // Simulated for example
    
    const refundId = `REF-${Date.now()}`;
    
    await this.db.query(
      `INSERT INTO refunds (refund_id, order_id, amount, reason, status, created_at)
       VALUES ($1, $2, $3, $4, $5, $6)`,
      [refundId, orderId, amount, reason, 'processed', new Date()]
    );

    await this.db.query(
      `UPDATE orders SET status = $1 WHERE order_id = $2`,
      ['refunded', orderId]
    );

    return {
      success: true,
      refundId,
      amount,
      message: `Refund of $${amount} processed successfully. Refund ID: ${refundId}`
    };
  }

  // Circuit Breaker Implementation
  isCircuitOpen(toolName) {
    const breaker = this.circuitBreaker.get(toolName);
    if (!breaker) return false;

    if (breaker.state === 'open') {
      // Check if cooldown period has passed
      if (Date.now() - breaker.openedAt > 60000) { // 60 second cooldown
        breaker.state = 'half-open';
        breaker.failures = 0;
        return false;
      }
      return true;
    }

    return false;
  }

  recordFailure(toolName) {
    let breaker = this.circuitBreaker.get(toolName);
    if (!breaker) {
      breaker = { failures: 0, successes: 0, state: 'closed' };
      this.circuitBreaker.set(toolName, breaker);
    }

    breaker.failures++;

    // Open circuit after 5 failures
    if (breaker.failures >= 5) {
      breaker.state = 'open';
      breaker.openedAt = Date.now();
      logger.warn(`Circuit breaker opened for ${toolName}`);
    }
  }

  recordSuccess(toolName) {
    let breaker = this.circuitBreaker.get(toolName);
    if (!breaker) return;

    if (breaker.state === 'half-open') {
      // Close circuit after success in half-open state
      breaker.state = 'closed';
      breaker.failures = 0;
      logger.info(`Circuit breaker closed for ${toolName}`);
    }
  }

  // Error Categorization
  categorizeError(error) {
    if (error.code === 'ECONNREFUSED') return 'network_timeout';
    if (error.code === '23505') return 'duplicate_entry';
    if (error.message.includes('not found')) return 'resource_not_found';
    if (error.message.includes('unauthorized')) return 'authentication_failed';
    return 'unknown_error';
  }

  isRetryable(error) {
    const retryableCategories = [
      'network_timeout',
      'rate_limit_exceeded',
      'service_unavailable'
    ];
    return retryableCategories.includes(this.categorizeError(error));
  }

  suggestAction(error) {
    const category = this.categorizeError(error);
    const actions = {
      'network_timeout': 'Retry with exponential backoff',
      'rate_limit_exceeded': 'Wait 60 seconds and retry',
      'authentication_failed': 'Check credentials and permissions',
      'resource_not_found': 'Verify resource ID and try again',
      'duplicate_entry': 'Resource already exists'
    };
    return actions[category] || 'Contact system administrator';
  }

  async auditLog(entry) {
    await this.db.query(
      `INSERT INTO audit_logs (event_type, details, timestamp)
       VALUES ($1, $2, $3)`,
      [entry.event, JSON.stringify(entry), entry.timestamp]
    );
  }
}
```

---

## 5. Hooks Implementation

### Standalone Hook Examples

```javascript
// hooks.js

// PreToolUse Hook - Business Rule Enforcement
export async function preToolUseHook(toolName, input) {
  const hooks = {
    process_refund: async (input) => {
      // CRITICAL: $500 refund limit
      if (input.amount > 500) {
        return {
          blocked: true,
          reason: 'Refund exceeds $500 automatic approval limit',
          escalate: true,
          requiresApproval: 'manager'
        };
      }
      return { blocked: false };
    },

    update_customer_credit: async (input) => {
      // Credit limit enforcement
      if (input.creditAmount > 1000) {
        return {
          blocked: true,
          reason: 'Credit adjustment exceeds $1000 limit',
          escalate: true
        };
      }
      return { blocked: false };
    },

    delete_customer_data: async (input) => {
      // GDPR compliance check
      const hasActiveOrders = await checkActiveOrders(input.customerId);
      if (hasActiveOrders) {
        return {
          blocked: true,
          reason: 'Cannot delete customer with active orders',
          escalate: false
        };
      }
      return { blocked: false };
    }
  };

  const hook = hooks[toolName];
  return hook ? await hook(input) : { blocked: false };
}

// PostToolUse Hook - Validation & Audit
export async function postToolUseHook(toolName, result) {
  const hooks = {
    process_refund: async (result) => {
      if (result.success) {
        // Audit log all refunds
        await auditLog({
          event: 'refund_processed',
          refundId: result.refundId,
          amount: result.amount,
          timestamp: new Date().toISOString()
        });

        // Send confirmation email
        await sendEmail({
          to: result.customerEmail,
          subject: 'Refund Processed',
          body: `Your refund of $${result.amount} has been processed.`
        });
      }
      return result;
    },

    create_ticket: async (result) => {
      if (result.success && result.priority === 'urgent') {
        // Notify on-call team for urgent tickets
        await notifyOnCall({
          ticketId: result.ticketId,
          priority: result.priority
        });
      }
      return result;
    }
  };

  const hook = hooks[toolName];
  return hook ? await hook(result) : result;
}
```

---

## 6. Error Handling

### Structured Error Propagation

```javascript
// errorHandler.js

export class ErrorHandler {
  static createError(category, message, context = {}, isRetryable = false) {
    return {
      success: false,
      errorCategory: category,
      isRetryable,
      message,
      context,
      suggestedAction: this.getSuggestedAction(category),
      timestamp: new Date().toISOString()
    };
  }

  static getSuggestedAction(category) {
    const actions = {
      'network_timeout': 'Retry with exponential backoff',
      'rate_limit_exceeded': 'Wait and retry after cooldown period',
      'authentication_failed': 'Verify API credentials',
      'invalid_input': 'Check input parameters and format',
      'resource_not_found': 'Verify resource ID exists',
      'permission_denied': 'Check user permissions',
      'service_unavailable': 'Service temporarily down, retry later',
      'database_error': 'Check database connection and query'
    };
    return actions[category] || 'Contact support';
  }

  static async handleWithRetry(fn, maxRetries = 3, backoffMs = 1000) {
    let lastError;

    for (let attempt = 1; attempt <= maxRetries; attempt++) {
      try {
        return await fn();
      } catch (error) {
        lastError = error;
        
        if (!this.isRetryable(error) || attempt === maxRetries) {
          throw error;
        }

        // Exponential backoff
        const delay = backoffMs * Math.pow(2, attempt - 1);
        await this.sleep(delay);
      }
    }

    throw lastError;
  }

  static isRetryable(error) {
    const retryableCategories = [
      'network_timeout',
      'rate_limit_exceeded',
      'service_unavailable',
      'database_connection_error'
    ];
    return retryableCategories.includes(error.errorCategory);
  }

  static sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  // Partial Results Handler
  static handlePartialResults(results) {
    const completed = results.filter(r => r.success);
    const failed = results.filter(r => !r.success);

    return {
      status: failed.length === 0 ? 'success' : 
              completed.length === 0 ? 'failed' : 'partial_success',
      completed: completed.map(r => ({
        agent: r.agent,
        result: r.data
      })),
      failed: failed.map(r => ({
        agent: r.agent,
        error: r.error,
        context: r.context
      })),
      summary: {
        total: results.length,
        succeeded: completed.length,
        failed: failed.length
      }
    };
  }
}
```

---

## 7. Session Management

### Redis Session Manager

```javascript
// sessionManager.js
import Redis from 'ioredis';
import { config } from './config.js';
import { logger } from './logger.js';

export class SessionManager {
  constructor() {
    this.redis = new Redis({
      host: config.redis.host,
      port: config.redis.port
    });
  }

  async getSession(sessionId, customerId) {
    if (sessionId) {
      const cached = await this.redis.get(`session:${sessionId}`);
      if (cached) {
        return JSON.parse(cached);
      }
    }

    // Create new session
    const newSessionId = `sess_${Date.now()}_${customerId}`;
    return {
      sessionId: newSessionId,
      customerId,
      messages: [],
      caseFacts: {}, // Never summarized
      metadata: {
        createdAt: new Date().toISOString(),
        lastActivity: new Date().toISOString()
      }
    };
  }

  async saveSession(session) {
    const key = `session:${session.sessionId}`;
    
    // Progressive summarization if context too large
    if (session.messages.length > 20) {
      session = await this.compactSession(session);
    }

    await this.redis.setex(
      key,
      config.redis.ttl,
      JSON.stringify(session)
    );

    logger.info(`Session saved: ${session.sessionId}`);
  }

  async compactSession(session) {
    // Keep recent messages, summarize older ones
    const recentMessages = session.messages.slice(-10);
    const olderMessages = session.messages.slice(0, -10);

    if (olderMessages.length > 0) {
      const summary = this.summarizeMessages(olderMessages);
      
      return {
        ...session,
        messages: [
          {
            role: 'user',
            content: `[Previous conversation summary: ${summary}]`
          },
          ...recentMessages
        ]
      };
    }

    return session;
  }

  summarizeMessages(messages) {
    // Simple summarization - in production, use Claude for this
    const topics = new Set();
    messages.forEach(msg => {
      if (msg.content.includes('refund')) topics.add('refund request');
      if (msg.content.includes('order')) topics.add('order inquiry');
      if (msg.content.includes('account')) topics.add('account issue');
    });
    return `Discussed: ${Array.from(topics).join(', ')}`;
  }

  async deleteSession(sessionId) {
    await this.redis.del(`session:${sessionId}`);
  }
}
```

---

## 8. Complete Example

### Express API Server

```javascript
// server.js
import express from 'express';
import { CoordinatorAgent } from './coordinatorAgent.js';
import { logger } from './logger.js';

const app = express();
app.use(express.json());

const coordinator = new CoordinatorAgent();

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'healthy' });
});

// Process customer request
app.post('/api/support', async (req, res) => {
  try {
    const { customerId, message, sessionId } = req.body;

    if (!customerId || !message) {
      return res.status(400).json({
        error: 'Missing required fields: customerId, message'
      });
    }

    logger.info(`Request from customer ${customerId}`);

    const result = await coordinator.processRequest(
      customerId,
      message,
      sessionId
    );

    res.json(result);

  } catch (error) {
    logger.error('API error:', error);
    res.status(500).json({
      error: 'Internal server error',
      message: error.message
    });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  logger.info(`Server running on port ${PORT}`);
});
```

### Usage Example

```javascript
// example.js
import fetch from 'node-fetch';

async function testCustomerSupport() {
  const response = await fetch('http://localhost:3000/api/support', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      customerId: 'CUST-12345',
      message: 'I want to return my order #ORD-789 and get a refund'
    })
  });

  const result = await response.json();
  console.log('Response:', JSON.stringify(result, null, 2));
}

testCustomerSupport();
```

---

## 🎯 Key Certification Concepts (JavaScript)

### Domain 1: Agentic Architecture
✅ **Hub-and-Spoke**: Coordinator + Subagents  
✅ **Agentic Loop**: `while` loop with `stop_reason` checking  
✅ **Explicit Context Passing**: `spawnSubagent(type, context)`  
✅ **Hooks**: `preToolUseHook()` and `postToolUseHook()`

### Domain 2: Tool Design
✅ **4-5 Tools**: Refund agent has exactly 4 tools  
✅ **Clear Descriptions**: Each tool has detailed `description`  
✅ **Structured Errors**: `{ success, errorCategory, isRetryable }`  
✅ **MCP Integration**: Can be added via `@anthropic-ai/sdk`

### Domain 3: Configuration
✅ **Environment Config**: `config.js` with dotenv  
✅ **Modular Design**: Separate files for each component  
✅ **Business Rules**: Enforced in hooks and config

### Domain 4: Prompt Engineering
✅ **Explicit Prompts**: Detailed system prompts  
✅ **Temperature 0.0**: For consistent, factual responses  
✅ **Structured Output**: JSON schemas in tool definitions

### Domain 5: Reliability
✅ **Circuit Breaker**: `isCircuitOpen()` implementation  
✅ **Retry Logic**: Exponential backoff with `handleWithRetry()`  
✅ **Partial Results**: `handlePartialResults()` function  
✅ **Session Management**: Redis with progressive summarization

---

## 📦 Project Structure

```
customer-support-agent/
├── package.json
├── .env
├── config.js
├── server.js
├── coordinatorAgent.js
├── sessionManager.js
├── toolService.js
├── errorHandler.js
├── hooks.js
├── logger.js
├── database.js
├── subagents/
│   ├── index.js
│   ├── refundAgent.js
│   ├── searchAgent.js
│   ├── accountAgent.js
│   └── ticketAgent.js
└── tests/
    ├── coordinator.test.js
    ├── refundAgent.test.js
    └── hooks.test.js
```

---

## 🚀 Getting Started

```bash
# Install dependencies
npm install

# Set up environment
cp .env.example .env
# Edit .env with your ANTHROPIC_API_KEY

# Start Redis
docker run -d -p 6379:6379 redis

# Run server
node server.js

# Test
curl -X POST http://localhost:3000/api/support \
  -H "Content-Type: application/json" \
  -d '{
    "customerId": "CUST-123",
    "message": "I need a refund for order ORD-456"
  }'
```

---

**All Python concepts from the certification are now available in JavaScript!** 🎉
