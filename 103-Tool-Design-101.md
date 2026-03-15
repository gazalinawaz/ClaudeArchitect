# Tool Design 101: Creating Tools for Your AI Agent

## 🔧 What Are Tools?

### The Simple Explanation
**Tools** are actions that your AI agent can perform. They're like giving your agent hands to interact with the world!

### Real-World Analogy
Think of Claude as a **super-smart brain in a jar**:
- **Without tools:** Can think and talk, but can't do anything
- **With tools:** Can search, read files, send emails, calculate, etc.

**Example:**
```
You: "What's the weather in Tokyo?"

Without tools:
Claude: "I can't check real-time weather, but typically Tokyo in 
summer is hot and humid..."

With tools:
Claude: *Uses weather_tool* 
"It's currently 24°C and sunny in Tokyo with 60% humidity."
```

---

## 🎯 Why Tools Matter

### What Agents Can Do Without Tools
- Answer questions from training data
- Explain concepts
- Write text
- Reason through problems
- Have conversations

### What Agents Can Do WITH Tools
- **Everything above, PLUS:**
- Access real-time information
- Read and write files
- Search databases
- Send emails
- Make calculations
- Call APIs
- Interact with systems

**Key Insight:** Tools transform agents from "thinkers" to "doers"!

---

## 🏗️ Anatomy of a Tool

Every tool has **three essential parts**:

### 1. Name
**What the tool is called**

```
Good names:
- search_customer
- send_email
- calculate_total
- read_file

Bad names:
- do_thing
- helper
- tool1
- process
```

**Rule:** Name should clearly describe what it does!

### 2. Description
**What the tool does and when to use it**

```
❌ Bad Description:
"Searches stuff"

✅ Good Description:
"Searches customer database by email or phone number. 
Returns customer profile including name, order history, 
and account status. Use when you need customer information."
```

**Rule:** Be specific! Include what it does, what it needs, and what it returns.

### 3. Parameters (Inputs)
**What information the tool needs**

```
Tool: send_email

Parameters:
- to: Email address (required)
- subject: Email subject line (required)
- body: Email content (required)
- cc: CC recipients (optional)
```

**Rule:** Clearly mark what's required vs. optional!

---

## 🎨 Your First Tool: Calculator

Let's design a simple calculator tool step by step.

### Step 1: Define the Purpose
**What should it do?**
"Perform basic math operations: add, subtract, multiply, divide"

### Step 2: Choose a Name
```
Tool name: calculate
```

### Step 3: Write the Description
```
Description: "Performs basic mathematical calculations. 
Supports addition, subtraction, multiplication, and division. 
Use when you need to compute numerical results."
```

### Step 4: Define Parameters
```
Parameters:
- operation: Type of calculation (add, subtract, multiply, divide) - REQUIRED
- number1: First number - REQUIRED
- number2: Second number - REQUIRED
```

### Step 5: Complete Tool Definition
```json
{
  "name": "calculate",
  "description": "Performs basic mathematical calculations. Supports addition, subtraction, multiplication, and division. Use when you need to compute numerical results.",
  "parameters": {
    "operation": {
      "type": "string",
      "description": "Type of calculation",
      "options": ["add", "subtract", "multiply", "divide"],
      "required": true
    },
    "number1": {
      "type": "number",
      "description": "First number",
      "required": true
    },
    "number2": {
      "type": "number",
      "description": "Second number",
      "required": true
    }
  }
}
```

### Step 6: How Agent Uses It
```
User: "What's 15% of 200?"

Agent thinks: "I need to calculate 200 × 0.15"

Agent calls tool:
calculate(operation="multiply", number1=200, number2=0.15)

Tool returns: 30

Agent responds: "15% of 200 is 30"
```

---

## 📋 Tool Design Best Practices

### Practice 1: One Tool, One Purpose
❌ **Bad:** `do_everything` tool that searches, calculates, and sends emails
✅ **Good:** Separate tools for each function

### Practice 2: Clear, Descriptive Names
❌ **Bad:** `tool1`, `helper`, `process`
✅ **Good:** `search_database`, `send_email`, `calculate_price`

### Practice 3: Detailed Descriptions
❌ **Bad:** "Gets data"
✅ **Good:** "Retrieves customer data from database by email or customer ID. Returns name, email, phone, and order history."

### Practice 4: Include Examples in Description
```
Description: "Searches products by name or category. 

Examples:
- search_products(query='laptop', category='electronics')
- search_products(query='nike shoes', category='sports')

Returns: List of products with name, price, and availability."
```

### Practice 5: Handle Errors Gracefully
```
Tool: get_weather

If location not found:
Return: {
  "error": true,
  "message": "Location 'Atlantis' not found. Please check spelling."
}

If API fails:
Return: {
  "error": true,
  "message": "Weather service temporarily unavailable. Try again in a moment."
}
```

---

## 🎓 Hands-On Exercise 1: Design a Search Tool

Design a tool that searches a product database.

### Requirements:
- Search by product name or category
- Return product name, price, and stock status
- Handle cases where no products found

### Your Task:
Fill in the blanks:

```
Tool Name: _____________

Description: _____________________________________________
___________________________________________________________

Parameters:
- query: _______________________________________________
- category: ____________________________________________

Example usage: _________________________________________

Error handling: ________________________________________
```

### Example Solution:
```
Tool Name: search_products

Description: "Searches product database by name or category. 
Returns matching products with name, price, and stock status. 
Use when customer asks about product availability or pricing."

Parameters:
- query: Product name to search for (required)
- category: Product category to filter by (optional)

Example usage: 
search_products(query="laptop", category="electronics")

Error handling:
- If no products found: Return empty list with message
- If database error: Return error message with retry suggestion
```

---

## 🛠️ Common Tool Patterns

### Pattern 1: Search/Query Tools
**Purpose:** Find information

```
Tool: search_customer
Input: email or phone
Output: Customer profile

Tool: search_products
Input: product name or category
Output: List of products

Tool: search_orders
Input: order ID or customer ID
Output: Order details
```

### Pattern 2: Create/Write Tools
**Purpose:** Create new data

```
Tool: create_customer
Input: name, email, phone
Output: New customer ID

Tool: create_order
Input: customer ID, products, total
Output: Order confirmation

Tool: send_email
Input: to, subject, body
Output: Sent confirmation
```

### Pattern 3: Update/Modify Tools
**Purpose:** Change existing data

```
Tool: update_customer
Input: customer ID, fields to update
Output: Updated customer profile

Tool: cancel_order
Input: order ID, reason
Output: Cancellation confirmation
```

### Pattern 4: Calculate/Process Tools
**Purpose:** Perform computations

```
Tool: calculate_total
Input: items, tax rate, discount
Output: Final total

Tool: calculate_shipping
Input: weight, destination
Output: Shipping cost
```

---

## 🎓 Hands-On Exercise 2: Match Tool to Task

Match each task to the type of tool needed:

### Tasks:
1. "Find customer John Smith's order history"
2. "Send confirmation email to customer"
3. "Calculate 15% discount on $100"
4. "Update customer's phone number"
5. "Create a new support ticket"

### Tool Types:
A. Search/Query Tool
B. Create/Write Tool
C. Update/Modify Tool
D. Calculate/Process Tool

### Answers:
1. _____ (A - Search)
2. _____ (B - Create/Write)
3. _____ (D - Calculate)
4. _____ (C - Update)
5. _____ (B - Create/Write)

---

## 🎯 Tool Parameters: Types and Validation

### Common Parameter Types

```
1. String (text)
   Example: name, email, description

2. Number (integer or decimal)
   Example: age, price, quantity

3. Boolean (true/false)
   Example: is_active, send_notification

4. Array (list)
   Example: tags, categories, recipients

5. Object (structured data)
   Example: address {street, city, zip}
```

### Parameter Validation

```
Tool: create_user

Parameters with validation:
- email: 
  - Type: string
  - Format: Must be valid email
  - Required: Yes
  
- age:
  - Type: number
  - Range: 18-120
  - Required: Yes
  
- phone:
  - Type: string
  - Format: (XXX) XXX-XXXX
  - Required: No
```

---

## 🚫 Common Tool Design Mistakes

### Mistake 1: Vague Descriptions
❌ **Bad:**
```
Tool: get_data
Description: "Gets data"
```

✅ **Good:**
```
Tool: get_customer_orders
Description: "Retrieves all orders for a specific customer by customer ID. 
Returns order ID, date, total, and status for each order."
```

### Mistake 2: Too Many Parameters
❌ **Bad:**
```
Tool: create_customer
Parameters: name, email, phone, address, city, state, zip, 
country, birthday, preferences, notes, tags, status...
(15+ parameters!)
```

✅ **Good:**
```
Tool: create_customer
Parameters: 
- name (required)
- email (required)
- phone (optional)
- address_object (optional) - contains street, city, state, zip
```

### Mistake 3: Unclear Parameter Names
❌ **Bad:**
```
Parameters:
- val1
- val2
- data
- info
```

✅ **Good:**
```
Parameters:
- customer_email
- order_id
- shipping_address
- payment_method
```

### Mistake 4: No Error Handling
❌ **Bad:**
```
If error occurs: *crashes*
```

✅ **Good:**
```
If error occurs:
Return {
  "success": false,
  "error": "Customer not found",
  "suggestion": "Check customer ID and try again"
}
```

---

## 🎓 Hands-On Exercise 3: Fix the Bad Tool

Here's a poorly designed tool. Fix it!

### Bad Tool:
```
Tool: do_thing
Description: "Does stuff with data"
Parameters:
- x
- y
- z
```

### Your Task:
Redesign this tool to send a welcome email to new customers.

### Example Solution:
```
Tool: send_welcome_email
Description: "Sends automated welcome email to new customers. 
Includes account setup instructions and support contact information. 
Use immediately after customer account creation."

Parameters:
- customer_email: Email address of new customer (required)
- customer_name: Customer's first name for personalization (required)
- account_id: New account ID for reference (required)
- include_promo: Whether to include promotional offer (optional, default: true)

Returns:
{
  "success": true,
  "email_sent_to": "customer@example.com",
  "sent_at": "2024-03-15T10:30:00Z"
}

Error handling:
- Invalid email: Returns error with format requirements
- Email service down: Returns error with retry suggestion
```

---

## 📊 Tool Response Formats

### Success Response
```json
{
  "success": true,
  "data": {
    "customer_id": "12345",
    "name": "John Smith",
    "email": "john@example.com",
    "orders": [
      {"id": "ORD-001", "total": 99.99},
      {"id": "ORD-002", "total": 149.99}
    ]
  }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "CUSTOMER_NOT_FOUND",
    "message": "No customer found with email: john@example.com",
    "suggestion": "Check email spelling or search by customer ID instead"
  }
}
```

### Partial Success Response
```json
{
  "success": "partial",
  "completed": ["send_email", "update_database"],
  "failed": ["send_sms"],
  "errors": {
    "send_sms": "Phone number invalid"
  }
}
```

---

## 🎯 How Many Tools Should an Agent Have?

### The Sweet Spot: 4-5 Tools

**Why?**
- Too few (1-2): Agent is limited
- Just right (4-5): Agent can choose effectively
- Too many (18+): Agent gets confused

### Example: Customer Support Agent

**Good (5 tools):**
1. search_customer
2. search_orders
3. process_refund
4. send_email
5. create_ticket

**Too many (15 tools):**
1. search_customer_by_email
2. search_customer_by_phone
3. search_customer_by_id
4. search_customer_by_name
5. get_order_by_id
6. get_orders_by_customer
7. get_recent_orders
... (and 8 more!)

**Better approach:** Combine related functions into one flexible tool!

---

## 🎓 Hands-On Exercise 4: Design a Complete Toolset

Design a complete toolset for a **Recipe Assistant Agent**.

### Agent Purpose:
Help users find recipes, check ingredients, and create shopping lists.

### Your Task:
Design 4-5 tools that would help this agent succeed.

### Example Solution:
```
1. Tool: search_recipes
   Description: "Searches recipe database by dish name, cuisine type, 
   or dietary restriction. Returns recipe name, ingredients, and cooking time."
   Parameters:
   - query: Search term (required)
   - cuisine: Cuisine type filter (optional)
   - dietary: Dietary restrictions (optional)

2. Tool: get_recipe_details
   Description: "Gets full recipe including ingredients, instructions, 
   nutrition info, and cooking time."
   Parameters:
   - recipe_id: Recipe identifier (required)

3. Tool: check_ingredient_substitutes
   Description: "Finds substitute ingredients when original isn't available."
   Parameters:
   - ingredient: Original ingredient (required)
   - recipe_context: What it's being used for (optional)

4. Tool: create_shopping_list
   Description: "Creates formatted shopping list from recipe ingredients."
   Parameters:
   - recipe_ids: List of recipe IDs (required)
   - servings: Number of servings (optional, default: 4)

5. Tool: calculate_nutrition
   Description: "Calculates total nutrition facts for a recipe."
   Parameters:
   - recipe_id: Recipe identifier (required)
   - servings: Number of servings (required)
```

---

## ✅ Tool Design Checklist

Before finalizing a tool, check:

- [ ] Name clearly describes what it does
- [ ] Description includes what it does, when to use it, and what it returns
- [ ] All parameters have clear names
- [ ] Required vs. optional parameters are marked
- [ ] Parameter types are specified
- [ ] Examples are included in description
- [ ] Error handling is defined
- [ ] Response format is consistent
- [ ] Tool has one clear purpose
- [ ] Tool name and description would make sense to someone else

---

## 🚀 Next Steps

Excellent work! You now know how to design effective tools for AI agents!

**Ready for more?**

Move on to **[Claude Code Basics](./104-Claude-Code-Basics.md)** where you'll learn:
- What Claude Code is and how it works
- Setting up your first project
- Basic configuration
- Working with real tools

---

## 💡 Practice Projects

### Project 1: Tool Design Portfolio
Design complete toolsets for these agents:
1. Email Management Agent (5 tools)
2. Calendar Assistant Agent (4 tools)
3. Data Analysis Agent (5 tools)

### Project 2: Tool Improvement
Take these bad tools and redesign them properly:
1. `do_search` - "searches"
2. `get_stuff` - "gets things"
3. `process` - "processes data"

### Project 3: Error Handling
For each tool, write 3 possible error scenarios and how to handle them:
1. send_email tool
2. search_database tool
3. process_payment tool

---

## 🎯 Real-World Tool Examples

### E-commerce Agent Tools
```
1. search_products(query, category, price_range)
2. get_product_details(product_id)
3. check_inventory(product_id, location)
4. add_to_cart(product_id, quantity)
5. process_checkout(cart_id, payment_method)
```

### Support Ticket Agent Tools
```
1. search_tickets(customer_id, status, priority)
2. create_ticket(customer_id, subject, description)
3. update_ticket(ticket_id, status, notes)
4. assign_ticket(ticket_id, agent_id)
5. send_update_email(ticket_id, message)
```

### Content Creation Agent Tools
```
1. search_topics(keyword, category)
2. generate_outline(topic, target_length)
3. research_facts(topic, sources)
4. create_draft(outline, research_data)
5. save_document(content, format, filename)
```

---

**Remember:** Good tools are clear, focused, and well-documented!

**Next:** [Claude Code Basics →](./104-Claude-Code-Basics.md)
