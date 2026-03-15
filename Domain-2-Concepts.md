# Domain 2: Tool Design & MCP Integration - Concept Guide

**Study this guide BEFORE attempting Domain 2 questions**

This guide explains everything you need to know about designing tools and integrating with MCP (Model Context Protocol).

---

## �️ Concept Map

```
TOOL DESIGN & MCP INTEGRATION
│
├── TOOL ANATOMY (3 Essential Parts)
│   ├── 1. Name
│   │   ├── ✓ Good: search_customer, send_email
│   │   └── ✗ Bad: tool1, helper, do_thing
│   ├── 2. Description
│   │   ├── What it does
│   │   ├── What it needs (parameters)
│   │   ├── What it returns
│   │   └── When to use it
│   └── 3. Parameters
│       ├── Types: string, number, boolean, array, object
│       ├── Required vs Optional
│       └── Clear naming
│
├── TOOL DESIGN PRINCIPLES
│   ├── One Tool, One Purpose
│   ├── Optimal: 4-5 tools per agent
│   ├── Combine Related Operations
│   │   └── Example: search_customer(type, value) vs 20 separate tools
│   ├── Clear Parameter Names
│   └── Include Examples in Description
│
├── MCP (Model Context Protocol)
│   ├── Configuration Scopes (Hierarchy)
│   │   ├── Managed (Highest priority)
│   │   ├── Project (.mcp.json in root)
│   │   ├── User (~/.claude.json)
│   │   └── Local (Lowest priority)
│   ├── Environment Variables
│   │   ├── ${VAR} - Use variable
│   │   └── ${VAR:-default} - Use VAR or default
│   └── MCP Servers
│       └── Standardized tool/resource providers
│
├── BUILT-IN TOOLS
│   ├── Read (Read file contents)
│   ├── Write (Create new files)
│   ├── Edit (Modify existing files)
│   ├── Grep (Search file contents)
│   ├── Glob (Find files by pattern)
│   └── Bash (Run shell commands)
│
├── ERROR HANDLING
│   ├── Structured Error Response
│   │   ├── success: false
│   │   ├── errorCategory
│   │   ├── isRetryable (true/false)
│   │   ├── message
│   │   ├── suggestedAction
│   │   └── context
│   ├── Error Categories
│   │   ├── Retryable: network_timeout, rate_limit, service_unavailable
│   │   └── Not Retryable: auth_failed, invalid_input, not_found
│   └── Empty Results ≠ Error
│       ├── Empty: Valid result (no data found)
│       └── Error: Access failure
│
└── TOOL CHOICE MODES
    ├── "auto" (Default)
    │   └── Claude decides whether to use tools
    ├── "any" (Force tool use)
    │   └── Use for: Guaranteed structured output
    └── Specific Tool
        └── Force exact tool: {"type": "tool", "name": "..."}
```

---

## �📚 Table of Contents

1. [What Are Tools?](#what-are-tools)
2. [Tool Anatomy](#tool-anatomy)
3. [Tool Design Best Practices](#tool-design-best-practices)
4. [MCP (Model Context Protocol)](#mcp-model-context-protocol)
5. [Built-in Tools](#built-in-tools)
6. [Error Handling in Tools](#error-handling-in-tools)
7. [Tool Choice Modes](#tool-choice-modes)

---

## 1. What Are Tools?

### Simple Explanation

**Tools** give Claude the ability to DO things, not just talk about them.

**Without tools:**
- Claude: "To check the weather, you should visit weather.com..."

**With tools:**
- Claude: *Uses weather tool* "It's currently 24°C and sunny in Tokyo"

### Real-World Analogy

Think of Claude as a **super-smart brain in a jar**:
- **Without tools:** Can think and advise, but can't act
- **With tools:** Can search, read files, send emails, calculate, etc.

### Common Tool Examples

| Tool | What It Does | Example |
|------|--------------|---------|
| `search_web` | Searches the internet | Find latest news |
| `read_file` | Reads file contents | Read config.json |
| `send_email` | Sends emails | Email team update |
| `calculate` | Performs math | Calculate 15% of $200 |
| `search_database` | Queries database | Find customer by email |

---

## 2. Tool Anatomy

Every tool has **three essential parts**:

### Part 1: Name

**What the tool is called**

```
✓ GOOD names:
- search_customer
- send_email
- calculate_total
- read_file

❌ BAD names:
- tool1
- helper
- do_thing
- process
```

**Rule:** Name should clearly describe what it does!

### Part 2: Description

**What the tool does, when to use it, what it returns**

```json
❌ BAD:
{
  "description": "Searches stuff"
}

✓ GOOD:
{
  "description": "Searches customer database by email or phone number. 
                 Returns customer profile including name, order history, 
                 and account status. Use when you need customer information."
}
```

**Rule:** Be specific! Include:
- What it does
- What inputs it needs
- What it returns
- When to use it

### Part 3: Parameters

**What information the tool needs**

```json
{
  "name": "send_email",
  "parameters": {
    "to": {
      "type": "string",
      "description": "Email address",
      "required": true
    },
    "subject": {
      "type": "string",
      "description": "Email subject line",
      "required": true
    },
    "body": {
      "type": "string",
      "description": "Email content",
      "required": true
    },
    "cc": {
      "type": "string",
      "description": "CC recipients",
      "required": false
    }
  }
}
```

**Parameter Types:**
- `string` - Text (e.g., "hello")
- `number` - Numbers (e.g., 42, 3.14)
- `boolean` - True/false
- `array` - List of items
- `object` - Structured data

---

## 3. Tool Design Best Practices

### Practice 1: One Tool, One Purpose

❌ **BAD:** One tool that does everything
```
do_everything(action, data, options, mode, type...)
```

✓ **GOOD:** Focused tools
```
search_customer(email)
create_order(customer_id, items)
send_email(to, subject, body)
```

### Practice 2: Combine Related Operations

❌ **BAD:** 20 separate search tools
```
search_by_email
search_by_phone
search_by_id
search_by_name
... (17 more)
```

✓ **GOOD:** One flexible tool
```
search_customer(search_type, search_value)
# search_type: "email" | "phone" | "id" | "name"
```

### Practice 3: Clear Parameter Names

❌ **BAD:**
```
process(x, y, z, data, info)
```

✓ **GOOD:**
```
process_refund(order_id, amount, reason)
```

### Practice 4: Include Examples in Description

```json
{
  "description": "Searches products by name or category.
  
  Examples:
  - search_products(query='laptop', category='electronics')
  - search_products(query='nike shoes', category='sports')
  
  Returns: List of products with name, price, and availability."
}
```

### Practice 5: Handle Empty Results Properly

**Empty results ≠ Error**

```python
# Customer search returns no results

❌ BAD:
return {"error": "Customer not found"}

✓ GOOD:
return {
  "success": true,
  "results": [],
  "message": "No customers found matching 'john@example.com'"
}
```

---

## 4. MCP (Model Context Protocol)

### What is MCP?

**MCP** is a standardized way to provide tools and resources to Claude.

Think of it like **USB** for AI:
- USB = Standard way to connect devices
- MCP = Standard way to connect tools

### MCP Configuration Scopes

There are **3 levels** of MCP configuration:

| Scope | Location | Purpose | Priority |
|-------|----------|---------|----------|
| **Local** | Command line | One-time use | Lowest |
| **Project** | `.mcp.json` in project root | Team/project settings | Medium |
| **User** | `~/.claude.json` | Personal settings | High |
| **Managed** | Cloud/admin | Organization settings | Highest |

**Hierarchy:** Managed > Project > User > Local

### Project-Scope Configuration

**File:** `.mcp.json` in project root

```json
{
  "mcpServers": {
    "database": {
      "command": "node",
      "args": ["./mcp-servers/database.js"],
      "env": {
        "DB_HOST": "${DB_HOST}",
        "DB_PORT": "${DB_PORT:-5432}"
      }
    }
  }
}
```

### User-Scope Configuration

**File:** `~/.claude.json`

```json
{
  "mcpServers": {
    "personal-tools": {
      "command": "python",
      "args": ["~/mcp-servers/personal.py"]
    }
  }
}
```

### Environment Variables

Use `${VAR}` syntax for environment variables:

```json
{
  "env": {
    "API_KEY": "${API_KEY}",              // Required
    "PORT": "${PORT:-3000}",              // Default: 3000
    "ENV": "${ENVIRONMENT:-development}"  // Default: development
  }
}
```

**Syntax:**
- `${VAR}` - Use environment variable VAR
- `${VAR:-default}` - Use VAR, or "default" if not set

---

## 5. Built-in Tools

Claude Code comes with built-in tools:

### Read Tool

**Purpose:** Read file contents

```python
Read(file_path="/path/to/file.txt")
```

### Write Tool

**Purpose:** Create new files

```python
Write(file_path="/path/to/new-file.txt", content="Hello World")
```

**When to use:** Creating NEW files

### Edit Tool

**Purpose:** Modify existing files

```python
Edit(
  file_path="/path/to/existing.txt",
  old_string="Hello",
  new_string="Hi"
)
```

**When to use:** Modifying EXISTING files (preserves unchanged content)

### Grep Tool

**Purpose:** Search file contents

```python
Grep(pattern="TODO", path="/src")
```

### Glob Tool

**Purpose:** Find files by pattern

```python
Glob(pattern="*.js", path="/src")
```

### Bash Tool

**Purpose:** Run shell commands

```python
Bash(command="npm test")
```

---

## 6. Error Handling in Tools

### Structured Error Responses

Every tool should return structured errors:

```json
{
  "success": false,
  "errorCategory": "rate_limit_exceeded",
  "isRetryable": true,
  "message": "API rate limit exceeded. 50 requests/minute allowed.",
  "suggestedAction": "Wait 60 seconds and retry",
  "context": {
    "attempted": "search_products",
    "query": "laptop"
  }
}
```

### Error Categories

| Category | isRetryable | When to Use |
|----------|-------------|-------------|
| `network_timeout` | true | Network issues |
| `rate_limit_exceeded` | true | Too many requests |
| `service_unavailable` | true | Temporary outage |
| `authentication_failed` | false | Invalid credentials |
| `invalid_input` | false | Bad parameters |
| `resource_not_found` | false | Item doesn't exist |
| `permission_denied` | false | Unauthorized |

### isRetryable Flag

**isRetryable: true** - Safe to retry
- Network timeouts
- Rate limits
- Temporary service issues

**isRetryable: false** - Don't retry
- Authentication failures
- Invalid input
- Resource not found
- Permission denied

### Example: Tool Error Handling

```python
def search_customer(email):
    try:
        result = database.query(email)
        
        if not result:
            # Empty result is NOT an error
            return {
                "success": true,
                "results": [],
                "message": f"No customer found with email: {email}"
            }
        
        return {
            "success": true,
            "results": result
        }
        
    except NetworkTimeout:
        return {
            "success": false,
            "errorCategory": "network_timeout",
            "isRetryable": true,
            "message": "Database connection timeout",
            "suggestedAction": "Retry with exponential backoff"
        }
        
    except AuthenticationError:
        return {
            "success": false,
            "errorCategory": "authentication_failed",
            "isRetryable": false,
            "message": "Database authentication failed",
            "suggestedAction": "Check database credentials"
        }
```

---

## 7. Tool Choice Modes

### What is tool_choice?

`tool_choice` controls whether and how Claude uses tools.

### Three Modes

#### Mode 1: "auto" (Default)

**Claude decides** whether to use tools

```python
tool_choice = "auto"
```

**Use when:** Normal operation, Claude chooses best approach

**Example:**
- User: "What's 2+2?"
- Claude: "4" (no tool needed)
- User: "What's the weather in Tokyo?"
- Claude: *Uses weather tool* "24°C and sunny"

#### Mode 2: "any"

**Force tool use** - Claude MUST use a tool (picks which one)

```python
tool_choice = "any"
```

**Use when:** You need guaranteed structured output

**Example:**
```python
# Extract data in specific format
response = claude.message(
    prompt="Extract invoice data",
    tools=[extract_invoice_tool],
    tool_choice="any"  # MUST use the tool
)
```

#### Mode 3: Specific Tool

**Force specific tool** use

```python
tool_choice = {
    "type": "tool",
    "name": "extract_invoice"
}
```

**Use when:** You know exactly which tool should be used

### When to Use Each Mode

| Scenario | Mode | Reason |
|----------|------|--------|
| General conversation | `"auto"` | Let Claude decide |
| Structured data extraction | `"any"` | Guarantee tool use |
| Specific operation required | `{"type": "tool", "name": "..."}` | Force exact tool |
| JSON output required | `"any"` | Ensure structured format |

---

## 🎯 Key Takeaways

### Critical Concepts

1. **Tools give Claude capabilities** - Search, read, write, calculate, etc.
2. **Three parts:** Name, Description, Parameters
3. **4-5 tools optimal** - Too many degrades selection
4. **Clear descriptions** - Help Claude choose correctly
5. **Structured errors** - Include category, isRetryable, context
6. **Empty ≠ Error** - No results is valid, not an error
7. **MCP hierarchy** - Managed > Project > User > Local

### Common Anti-Patterns

❌ Vague tool descriptions ("does stuff")
❌ Too many parameters (15+)
❌ Generic error messages
❌ Treating empty results as errors
❌ 18+ tools per agent
❌ Unclear parameter names

### Design Checklist

Before finalizing a tool:
- [ ] Name clearly describes function
- [ ] Description includes what/when/returns
- [ ] Parameters have clear names
- [ ] Required vs optional marked
- [ ] Examples included
- [ ] Error handling defined
- [ ] Empty results handled properly

---

## 📖 Study Flow

**Now that you understand these concepts:**

1. ✅ Read this concept guide
2. ✅ Review the examples
3. ✅ Understand tool anatomy
4. ➡️ **Now attempt Domain 2 Questions** → [Domain-2-Questions.md](./Domain-2-Questions.md)

---

**Previous:** [Domain 1 Concepts](./Domain-1-Concepts.md) | **Next:** [Domain 3 Concepts →](./Domain-3-Concepts.md)
