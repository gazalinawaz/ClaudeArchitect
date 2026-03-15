# Agentic Architecture 101: Building Your First AI Agent

## 🤖 What is an AI Agent?

### The Simple Explanation
An **AI agent** is like a smart assistant that can:
- Understand what you want
- Make decisions on its own
- Take actions to complete tasks
- Use tools to get things done
- Keep working until the job is finished

### Real-World Analogy
Think of an agent like a **personal assistant at work**:

**Regular Claude (Conversational):**
- You: "What's on my calendar today?"
- Assistant: "You have 3 meetings..."
- You: "Cancel the 2pm meeting"
- Assistant: "I can't cancel it, but here's how you can..."

**Claude Agent (Agentic):**
- You: "Manage my calendar for today"
- Agent: *Checks calendar* → *Sees conflict* → *Cancels less important meeting* → *Sends notifications* → *Updates you*
- Agent: "Done! I cancelled your 2pm meeting because it conflicted with the client call. Everyone has been notified."

**Key Difference:** The agent takes action without asking for permission at each step!

---

## 🎯 Agent vs. Chatbot: What's the Difference?

### Chatbot (Conversational)
```
You → Ask question → Claude → Responds → Done
```
- One question, one answer
- No actions taken
- You drive everything

### Agent (Agentic)
```
You → Give task → Agent → Plans → Uses tools → Takes actions → Reports back
```
- One task, multiple steps
- Agent decides what to do
- Agent drives the process

### Example Comparison

**Chatbot Interaction:**
```
You: "I need to book a flight to Paris"
Chatbot: "I can help you with that! You'll need to:
1. Visit an airline website
2. Enter your dates
3. Compare prices
4. Make a booking
Would you like tips on finding cheap flights?"
```

**Agent Interaction:**
```
You: "Book me a flight to Paris for next week"
Agent: *Searches flights* → *Compares prices* → *Finds best option* → *Books ticket*
Agent: "Done! I booked you on Air France, departing Monday at 9am, 
returning Friday at 6pm. Total: $450. Confirmation sent to your email."
```

---

## 🔄 The Agentic Loop: How Agents Think

### The Basic Loop

```
1. Receive task
   ↓
2. Think: What do I need to do?
   ↓
3. Decide: What action should I take?
   ↓
4. Act: Use a tool or respond
   ↓
5. Check: Am I done?
   ↓
   NO → Go back to step 2
   YES → Report results
```

### Real Example: Research Agent

**Task:** "Find the top 3 programming languages in 2024"

**Agent's Loop:**
```
Step 1: Receive task ✓

Step 2: Think
"I need to search for current programming language rankings"

Step 3: Decide
"I'll use the search tool"

Step 4: Act
*Uses search tool* → Gets results

Step 5: Check
"I have results, but need to verify and format them"
→ NOT DONE, continue loop

Step 2: Think (again)
"I should analyze these results and pick top 3"

Step 3: Decide
"I'll process the data and create a summary"

Step 4: Act
*Analyzes data* → Creates summary

Step 5: Check
"I have a clear answer with top 3 languages"
→ DONE!

Final Response:
"The top 3 programming languages in 2024 are:
1. Python - 29% market share
2. JavaScript - 24% market share  
3. Java - 18% market share"
```

---

## 🛠️ Tools: Giving Your Agent Superpowers

### What are Tools?

**Tools** are actions your agent can take. Think of them as superpowers!

**Without Tools:**
- Agent can only talk
- Can't access information
- Can't take actions
- Limited to what it knows

**With Tools:**
- Agent can search the web
- Agent can read/write files
- Agent can call APIs
- Agent can perform calculations
- Agent can interact with systems

### Common Tool Examples

| Tool | What It Does | Example Use |
|------|--------------|-------------|
| **Search** | Looks up information | "Search for weather in Tokyo" |
| **Calculator** | Does math | "Calculate 15% tip on $87.50" |
| **File Reader** | Reads files | "Read config.json" |
| **File Writer** | Creates/updates files | "Save this data to report.txt" |
| **Email Sender** | Sends emails | "Email the team this update" |
| **Database Query** | Gets data from database | "Find all orders from last week" |

### Tool Anatomy (Simple Version)

Every tool has three parts:

1. **Name**: What it's called
   - Example: `search_web`

2. **Description**: What it does
   - Example: "Searches the internet for information"

3. **Parameters**: What information it needs
   - Example: `query` (what to search for)

---

## 🎨 Your First Simple Agent (Conceptual)

Let's build a **Weather Agent** that tells you if you need an umbrella.

### Agent Design

**Task:** "Should I bring an umbrella today?"

**Tools Available:**
- `get_weather` - Gets current weather
- `get_forecast` - Gets weather forecast

**Agent Logic:**
```
1. Receive task: "Should I bring an umbrella?"

2. Think: "I need to check if it will rain"

3. Decide: "I'll use get_forecast tool"

4. Act: get_forecast(location="current", hours=12)
   Result: "60% chance of rain at 3pm"

5. Check: "I have the info I need"
   → DONE

6. Respond: "Yes, bring an umbrella! There's a 60% chance 
   of rain this afternoon at 3pm."
```

### The Code Concept (Don't worry about syntax yet!)

```python
# Define the agent's tools
tools = [
    {
        "name": "get_forecast",
        "description": "Gets weather forecast",
        "parameters": ["location", "hours"]
    }
]

# Give the agent a task
task = "Should I bring an umbrella today?"

# Agent runs its loop
agent.run(task, tools=tools)

# Agent uses tools and responds
# Output: "Yes, bring an umbrella! 60% chance of rain at 3pm."
```

---

## 🧩 The Stop Reason: How Agents Know They're Done

### What is a Stop Reason?

A **stop reason** tells you WHY the agent stopped. It's like the agent saying "Here's why I'm pausing."

### The Two Main Stop Reasons

1. **"tool_use"** - "I need to use a tool"
   ```
   Agent: "I want to search for information"
   → Uses search tool
   → Continues working
   ```

2. **"end_turn"** - "I'm done with the task"
   ```
   Agent: "I have the answer!"
   → Provides final response
   → Stops
   ```

### Visual Example

```
User: "What's the weather in Paris?"

Agent Loop:
┌─────────────────────────────────┐
│ Think: Need weather data        │
│ Stop Reason: "tool_use"         │
│ Action: Use get_weather tool    │
└─────────────────────────────────┘
         ↓
┌─────────────────────────────────┐
│ Got result: "Sunny, 22°C"       │
│ Think: I have the answer        │
│ Stop Reason: "end_turn"         │
│ Response: "It's sunny and 22°C" │
└─────────────────────────────────┘
         ↓
       DONE!
```

### Why This Matters

**Wrong Way (Beginner Mistake):**
```python
# Checking if agent said "done" in its response
if "done" in agent_response:
    stop = True
```
❌ Unreliable! Agent might not say "done"

**Right Way:**
```python
# Checking the stop_reason
if stop_reason == "end_turn":
    stop = True
```
✅ Reliable! Official signal from the agent

---

## 🎓 Hands-On Exercise 1: Design Your First Agent

Design a **Shopping List Agent** on paper (no coding yet!).

### Requirements:
- Task: "Create a shopping list for pasta dinner"
- Tools available:
  - `search_recipes` - Finds recipes
  - `get_ingredients` - Lists ingredients needed
  - `create_list` - Makes a shopping list

### Your Exercise:
1. Write out the agent's loop steps
2. Identify when it would use each tool
3. Determine when it would stop (end_turn)

### Example Solution:
```
Step 1: Receive task ✓

Step 2: Think - "I need a pasta recipe first"
Step 3: Decide - "Use search_recipes"
Step 4: Act - search_recipes("pasta dinner")
Step 5: Check - "Got recipe, but need ingredients" → Continue

Step 2: Think - "Now I need the ingredient list"
Step 3: Decide - "Use get_ingredients"  
Step 4: Act - get_ingredients(recipe_id)
Step 5: Check - "Got ingredients, need to format" → Continue

Step 2: Think - "Create the final shopping list"
Step 3: Decide - "Use create_list"
Step 4: Act - create_list(ingredients)
Step 5: Check - "List is ready!" → DONE

Stop Reason: "end_turn"
Response: "Here's your shopping list: pasta, tomatoes, garlic..."
```

---

## 🎓 Hands-On Exercise 2: Identify the Stop Reason

For each scenario, identify if the stop reason would be "tool_use" or "end_turn":

### Scenario A:
```
User: "What's 15% of 200?"
Agent: "I need to calculate this..."
```
**Stop Reason:** _________ (tool_use - needs calculator)

### Scenario B:
```
User: "What's 15% of 200?"
Agent: "15% of 200 is 30."
```
**Stop Reason:** _________ (end_turn - has the answer)

### Scenario C:
```
User: "Find the cheapest flight to Tokyo"
Agent: "Let me search for flights..."
```
**Stop Reason:** _________ (tool_use - needs to search)

### Scenario D:
```
User: "Explain what an API is"
Agent: "An API is a way for programs to communicate..."
```
**Stop Reason:** _________ (end_turn - no tools needed)

---

## 🏗️ Agent Architecture Patterns

### Pattern 1: Single-Purpose Agent
**One task, one agent**

```
Task: "Summarize this article"
Agent: Article Summarizer
Tools: read_file
Result: Summary
```

**When to use:** Simple, focused tasks

### Pattern 2: Multi-Tool Agent
**One agent, multiple tools**

```
Task: "Research and create a report"
Agent: Research Agent
Tools: search_web, read_files, create_document
Result: Complete report
```

**When to use:** Complex tasks requiring different actions

### Pattern 3: Conversational Agent
**Back-and-forth interaction**

```
User: "Help me plan a trip"
Agent: "Where would you like to go?"
User: "Japan"
Agent: "What's your budget?"
User: "$2000"
Agent: *Creates personalized plan*
```

**When to use:** Tasks needing user input

---

## 🚫 Common Beginner Mistakes

### Mistake 1: Giving Too Many Tools
❌ **Bad:** Give agent 20 different tools
✅ **Good:** Give agent 4-5 relevant tools

**Why?** Too many options confuse the agent!

### Mistake 2: Unclear Tool Descriptions
❌ **Bad:** 
```
Tool: "do_thing"
Description: "Does stuff"
```

✅ **Good:**
```
Tool: "search_database"
Description: "Searches customer database by email or phone number. 
Returns customer profile with order history."
```

### Mistake 3: Not Checking Stop Reason
❌ **Bad:** Assume agent is done after one response
✅ **Good:** Check stop_reason to know if agent needs to continue

### Mistake 4: Expecting Perfection Immediately
❌ **Bad:** Give up if agent doesn't work perfectly first try
✅ **Good:** Iterate and improve based on results

---

## 🎯 Key Concepts Summary

### The Agentic Loop
```
Receive → Think → Decide → Act → Check → Repeat or Done
```

### Stop Reasons
- **tool_use** = Agent needs to use a tool
- **end_turn** = Agent is finished

### Tools
- Give agents capabilities
- Should be focused and clear
- 4-5 tools per agent is optimal

### Agent Types
- **Single-purpose** - One specific task
- **Multi-tool** - Complex tasks with multiple steps
- **Conversational** - Interactive with user

---

## 📊 Agent Decision Making

### How Agents Choose Tools

```
Agent sees task: "Book a flight to Paris"

Available tools:
1. search_flights
2. book_flight  
3. send_email
4. calculate_price

Agent thinks:
"To book a flight, I first need to search for options"
→ Chooses: search_flights

After getting results:
"Now I can book the best option"
→ Chooses: book_flight

After booking:
"I should confirm with the user"
→ Chooses: send_email

Task complete!
```

**Key Insight:** Agents choose tools based on the current step, not all at once!

---

## 🎓 Hands-On Exercise 3: Build an Agent Flow

Design a **File Organizer Agent** that organizes messy files.

### Available Tools:
- `list_files` - Shows all files in a folder
- `read_file` - Reads file content
- `move_file` - Moves file to a folder
- `create_folder` - Creates a new folder

### Task: "Organize my Downloads folder by file type"

### Your Challenge:
Draw out the agent's decision flow:
1. What tool does it use first?
2. What tool does it use next?
3. When does it stop?

### Example Solution:
```
1. list_files("Downloads")
   → Gets: photo.jpg, document.pdf, song.mp3, report.pdf

2. Think: "I need folders for each type"
   create_folder("Images")
   create_folder("Documents")
   create_folder("Music")

3. For each file:
   - photo.jpg → move_file("photo.jpg", "Images")
   - document.pdf → move_file("document.pdf", "Documents")
   - song.mp3 → move_file("song.mp3", "Music")
   - report.pdf → move_file("report.pdf", "Documents")

4. Stop Reason: "end_turn"
   Response: "Organized! Created 3 folders and moved 4 files."
```

---

## ✅ Self-Check: Ready for Next Module?

Before moving to **Tool Design 101**, make sure you can:

- [ ] Explain what an AI agent is in your own words
- [ ] Describe the agentic loop (Receive → Think → Decide → Act → Check)
- [ ] Understand the difference between "tool_use" and "end_turn"
- [ ] Know why tools are important
- [ ] Design a simple agent flow on paper
- [ ] Identify when an agent would use a tool vs. respond directly
- [ ] Understand that agents make decisions autonomously

---

## 🚀 Next Steps

Great job! You now understand how AI agents work!

**Ready to learn more?**

Move on to **[Tool Design 101](./103-Tool-Design-101.md)** where you'll learn:
- How to design effective tools
- Creating your first tool
- Tool best practices
- Common tool patterns

---

## 💡 Practice Projects

### Project 1: Agent Flow Diagrams
Create flow diagrams for these agents:
1. Email Sorting Agent
2. News Summarizer Agent
3. Calendar Management Agent

### Project 2: Tool Selection
For each task, list which tools the agent would need:
1. "Create a weekly meal plan"
2. "Analyze my spending habits"
3. "Prepare for a job interview"

### Project 3: Stop Reason Practice
Write 10 agent responses and identify if each would have stop_reason "tool_use" or "end_turn"

---

## 🎯 Real-World Agent Examples

### Customer Support Agent
```
Task: Handle customer refund request
Tools: 
- check_order_status
- process_refund
- send_email

Flow:
1. Check order → Found
2. Verify eligibility → Eligible
3. Process refund → Success
4. Send confirmation → Sent
5. Done!
```

### Code Review Agent
```
Task: Review pull request
Tools:
- read_code
- run_tests
- check_style
- create_comment

Flow:
1. Read code changes
2. Run tests → 2 failed
3. Check style → 3 issues
4. Create review comment with findings
5. Done!
```

### Research Agent
```
Task: Research topic and create summary
Tools:
- search_web
- read_article
- create_document

Flow:
1. Search for articles
2. Read top 5 articles
3. Extract key points
4. Create summary document
5. Done!
```

---

**Remember:** Agents are just loops that use tools to complete tasks!

**Next:** [Tool Design 101 →](./103-Tool-Design-101.md)
