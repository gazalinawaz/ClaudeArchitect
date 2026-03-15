# Domain 1: Agentic Architecture & Orchestration (135 Questions)

**Weight: 27% of exam**

This domain covers agentic loops, multi-agent systems, hooks, session management, and workflow patterns.

---

## Beginner Level (45 Questions)

**Q1.** What is the definitive signal that an agentic loop should terminate?
- A) When the agent says "I'm done" in its response
- B) When the stop_reason equals "end_turn"
- C) After 10 iterations
- D) When the user stops responding

**Q2.** Which stop_reason indicates the agent wants to use a tool?
- A) "tool_needed"
- B) "tool_use"
- C) "execute_tool"
- D) "action_required"

**Q3.** In a multi-agent system, what is the primary role of the coordinator agent?
- A) Execute all tools directly
- B) Store shared memory for all agents
- C) Orchestrate workflow and delegate to subagents
- D) Monitor system performance

**Q4.** What happens to context between subagents in a multi-agent system?
- A) All subagents share the same context automatically
- B) Context is isolated; must be passed explicitly
- C) Context is stored in a central database
- D) The coordinator maintains all context

**Q5.** Which tool must be included in allowedTools for a coordinator to spawn subagents?
- A) "Spawn"
- B) "CreateAgent"
- C) "Task"
- D) "Subagent"

**Q6.** What is a PostToolUse hook used for?
- A) Preparing data before tool execution
- B) Intercepting and validating tool results after execution
- C) Logging tool usage statistics
- D) Scheduling tool execution

**Q7.** When should you use programmatic hooks instead of prompt-based enforcement?
- A) For stylistic preferences
- B) For critical business rules and compliance
- C) For general guidelines
- D) For user interface suggestions

**Q8.** What command resumes a previous Claude Code session?
- A) --continue
- B) --resume
- C) --restart
- D) --load

**Q9.** What does fork_session do?
- A) Deletes the current session
- B) Creates a branch from current state for exploration
- C) Merges two sessions together
- D) Saves the session to disk

**Q10.** How many tools per agent is considered optimal for selection accuracy?
- A) 1-2 tools
- B) 4-5 tools
- C) 10-12 tools
- D) 18+ tools

**Q11.** What is the main problem with having 18+ tools per agent?
- A) Too expensive to run
- B) Degrades tool selection quality
- C) Causes memory issues
- D) Slows down response time

**Q12.** In prompt chaining, how are tasks sequenced?
- A) Randomly based on agent decision
- B) Predefined sequence of prompts
- C) User determines order at runtime
- D) Based on tool availability

**Q13.** What is dynamic adaptive task decomposition?
- A) Fixed sequence of steps
- B) Agent determines next steps based on results
- C) User manually breaks down tasks
- D) Random task selection

**Q14.** What is a key anti-pattern in agentic loop termination?
- A) Checking stop_reason
- B) Parsing natural language for "done"
- C) Monitoring tool execution
- D) Tracking iteration count as metadata

**Q15.** What does the stop_reason "max_tokens" indicate?
- A) Agent completed successfully
- B) Response was truncated
- C) Too many tools used
- D) Session expired

**Q16.** Which is NOT a valid stop_reason?
- A) "tool_use"
- B) "end_turn"
- C) "max_tokens"
- D) "task_complete"

**Q17.** What is the purpose of named sessions in Claude Code?
- A) Faster execution
- B) Organize parallel work streams
- C) Reduce costs
- D) Improve accuracy

**Q18.** When does context degradation typically occur?
- A) After 5 minutes
- B) In extended sessions with accumulated context
- C) When using multiple tools
- D) During parallel execution

**Q19.** What is a scratchpad file used for?
- A) Temporary calculations
- B) Persistent state outside context window
- C) Tool configuration
- D) Error logging

**Q20.** What does /compact do in Claude Code?
- A) Compresses files
- B) Summarizes and reduces context
- C) Optimizes code
- D) Removes unused tools

**Q21.** In hub-and-spoke architecture, what is the "hub"?
- A) The database
- B) The coordinator agent
- C) The user interface
- D) The tool registry

**Q22.** What are the "spokes" in hub-and-spoke architecture?
- A) Database connections
- B) API endpoints
- C) Specialized subagents
- D) User sessions

**Q23.** How do you enable parallel subagent execution?
- A) Use parallel_mode flag
- B) Multiple Task calls in single response
- C) Create separate sessions
- D) Use threading library

**Q24.** What is a common pitfall of overly narrow task decomposition?
- A) Too expensive
- B) Coverage gaps
- C) Too slow
- D) Memory issues

**Q25.** What should a coordinator do when a subagent fails?
- A) Retry indefinitely
- B) Handle error with context and decide next action
- C) Ignore and continue
- D) Restart entire workflow

**Q26.** Which is a programmatic enforcement use case?
- A) Code formatting style
- B) Refund limit of $500
- C) Tone of responses
- D) Preferred wording

**Q27.** Which is a prompt-based guidance use case?
- A) Security policies
- B) Compliance requirements
- C) Stylistic preferences
- D) Critical business rules

**Q28.** What happens when a PostToolUse hook returns an error?
- A) Tool executes anyway
- B) Error is passed to agent, tool result blocked
- C) Session terminates
- D) User is prompted

**Q29.** Can hooks modify tool results before returning to agent?
- A) No, hooks are read-only
- B) Yes, for data normalization and validation
- C) Only for logging
- D) Only with user permission

**Q30.** What is the primary benefit of session forking?
- A) Faster execution
- B) Explore alternatives without affecting main session
- C) Reduce costs
- D) Better error handling

**Q31.** When should you reset a session instead of resuming?
- A) After every task
- B) When context has degraded or lost focus
- C) Every hour
- D) When using new tools

**Q32.** What is the maximum context window limitation?
- A) Agent can remember unlimited history
- B) Agent has limited memory for current conversation
- C) Agent remembers across all sessions
- D) Agent has no memory limitations

**Q33.** How does an agent decide which tool to use?
- A) Random selection
- B) Based on tool descriptions and current task
- C) User specifies
- D) Alphabetical order

**Q34.** What is the agentic loop?
- A) A bug in the code
- B) Receive → Think → Decide → Act → Check → Repeat
- C) A circular dependency
- D) An infinite loop error

**Q35.** Can an agent use multiple tools in sequence?
- A) No, only one tool per session
- B) Yes, as part of the agentic loop
- C) Only with user approval each time
- D) Only in parallel mode

**Q36.** What is explicit context passing?
- A) Automatic context sharing
- B) Manually providing context to subagents
- C) Database-stored context
- D) Encrypted context transfer

**Q37.** Why is context isolation important in multi-agent systems?
- A) Reduces costs
- B) Prevents cross-contamination between agents
- C) Improves speed
- D) Simplifies debugging

**Q38.** What is a coordinator's responsibility after receiving subagent results?
- A) Store in database
- B) Synthesize results and determine next steps
- C) Forward to user immediately
- D) Delete the results

**Q39.** Can a subagent spawn its own subagents?
- A) Yes, if it has Task tool access
- B) No, only coordinators can spawn
- C) Only with user permission
- D) Only in advanced mode

**Q40.** What is the recommended approach for handling partial failures in multi-agent systems?
- A) Fail entire workflow
- B) Return partial results with error context
- C) Retry silently
- D) Ignore failures

**Q41.** How should errors be propagated from subagents to coordinator?
- A) Generic error messages
- B) Structured errors with full context
- C) Silent failures
- D) User notifications only

**Q42.** What is the purpose of iteration caps in agentic loops?
- A) Primary stopping mechanism
- B) Safety mechanism, not primary termination
- C) Performance optimization
- D) Cost control

**Q43.** Why is parsing natural language for loop termination an anti-pattern?
- A) Too slow
- B) Unreliable compared to stop_reason
- C) Too expensive
- D) Not supported

**Q44.** What should you check to determine if an agent is done?
- A) Response text content
- B) stop_reason value
- C) Number of iterations
- D) Time elapsed

**Q45.** In a multi-step workflow, when should you use prompt chaining vs dynamic decomposition?
- A) Always use prompt chaining
- B) Chaining for known workflows, dynamic for exploratory
- C) Always use dynamic
- D) Randomly choose

---

## Intermediate Level (45 Questions)

**Q46.** You're building a customer support agent that must enforce a $500 refund limit. Which approach is correct?
- A) Add to system prompt: "Never approve refunds over $500"
- B) Implement PostToolUse hook that blocks refunds over $500
- C) Train agent with examples of rejecting high refunds
- D) Use temperature=0 for consistent refusal

**Q47.** Your agent achieves 55% first-contact resolution, below the 80% target. The agent has 18 tools. What's the most likely issue?
- A) Need more training data
- B) Too many tools degrading selection quality
- C) Temperature too low
- D) Context window too small

**Q48.** In a multi-agent research system, the search subagent finds 50 articles. How should this be passed to the analysis subagent?
- A) Automatically shared via global context
- B) Coordinator explicitly passes article list to analyzer
- C) Stored in database for analyzer to query
- D) Analysis subagent re-searches independently

**Q49.** Your agentic loop runs 100 iterations without completing. What's the most likely cause?
- A) stop_reason not being checked properly
- B) Too many tools available
- C) Network latency
- D) Insufficient memory

**Q50.** A coordinator spawns 5 parallel subagents. Two fail with errors. What should the coordinator do?
- A) Retry failed subagents indefinitely
- B) Return partial results with structured error context
- C) Fail entire workflow
- D) Continue without acknowledging failures

**Q51.** You need to ensure all customer data access is logged for compliance. Where should this be implemented?
- A) In the system prompt
- B) PostToolUse hook for data access tools
- C) In tool descriptions
- D) User-level logging

**Q52.** An agent needs to process 1000 customer records. What's the best approach?
- A) Single agentic loop processing all records
- B) Batch processing with periodic checkpoints
- C) 1000 separate agent sessions
- D) Prompt the agent to work faster

**Q53.** Your session has accumulated 50,000 tokens of context and responses are degrading. What should you do?
- A) Increase max_tokens
- B) Use /compact or start fresh session
- C) Reduce temperature
- D) Add more tools

**Q54.** A subagent needs to explore multiple solution paths. What's the best approach?
- A) Sequential exploration in same session
- B) fork_session for each path
- C) Create new coordinator
- D) Use higher temperature

**Q55.** You're building a code review agent. Should it review its own generated code in the same session?
- A) Yes, for efficiency
- B) No, use fresh session to avoid confirmation bias
- C) Yes, with higher temperature
- D) Only if explicitly asked

**Q56.** An agent must never process refunds on weekends. How do you enforce this?
- A) System prompt instruction
- B) PostToolUse hook checking current day
- C) Tool description warning
- D) User training

**Q57.** Your coordinator delegates to 3 subagents sequentially. How can you optimize for speed?
- A) Use faster model
- B) Parallel execution with multiple Task calls
- C) Reduce context
- D) Increase temperature

**Q58.** A subagent returns 10MB of data. How should the coordinator handle this?
- A) Pass all data to next subagent
- B) Summarize/trim before passing to next agent
- C) Store in database
- D) Ignore the data

**Q59.** You need an agent to follow a strict 5-step process. Which approach is best?
- A) Dynamic adaptive decomposition
- B) Prompt chaining with predefined steps
- C) Single prompt with all steps
- D) Multiple separate agents

**Q60.** An agent is making decisions based on self-reported confidence scores. What's the issue?
- A) Confidence scores are reliable
- B) Self-reported confidence is unreliable for decisions
- C) Need higher temperature
- D) Need more tools

**Q61.** Your agent needs to handle customer escalations. What should trigger escalation?
- A) Customer sentiment analysis
- B) Explicit business rules (policy gaps, high value, legal)
- C) Agent's confidence level
- D) Response length

**Q62.** A tool returns an empty array. How should this be handled?
- A) Treat as error
- B) Valid result (no data found), not an error
- C) Retry the tool
- D) Escalate to user

**Q63.** Your agent loop checks `if "done" in response.text`. What's wrong?
- A) Nothing, this is correct
- B) Should check stop_reason instead
- C) Should use regex
- D) Should check iteration count

**Q64.** A coordinator needs to synthesize results from 4 subagents. What's the best approach?
- A) Return first subagent result
- B) Aggregate all results with explicit synthesis step
- C) Average the results
- D) Let user choose

**Q65.** You're implementing a hook that blocks certain operations. When should the hook execute?
- A) Before tool call (PreToolUse)
- B) After tool call (PostToolUse)
- C) During tool execution
- D) After agent response

**Q66.** An agent needs to remember state across multiple sessions. What's the solution?
- A) Agent automatically remembers
- B) Use scratchpad files or external storage
- C) Increase context window
- D) Use higher temperature

**Q67.** Your multi-agent system has 1 coordinator and 10 subagents. What's the likely issue?
- A) Perfect design
- B) Too many subagents, high coordination overhead
- C) Need more subagents
- D) Need multiple coordinators

**Q68.** A subagent fails to find required information. What should it return?
- A) Empty response
- B) Structured error with what was attempted
- C) Generic "failed" message
- D) Retry silently

**Q69.** You need to enforce that all emails include a disclaimer. Where should this be implemented?
- A) System prompt
- B) PostToolUse hook for send_email tool
- C) Tool description
- D) User reminder

**Q70.** An agent is using stop_reason "tool_use" but no tools are being executed. What's wrong?
- A) Agent is broken
- B) Tool execution logic not implemented in loop
- C) Need more tools
- D) Temperature too low

**Q71.** Your agent needs to make a decision between 3 options. How many tools should it have access to?
- A) All available tools (20+)
- B) Only relevant tools for this decision (4-5)
- C) No tools needed
- D) Exactly 3 tools

**Q72.** A coordinator receives conflicting results from 2 subagents. What should it do?
- A) Choose first result
- B) Analyze conflict and determine resolution strategy
- C) Return error
- D) Average the results

**Q73.** You're building an agent that processes sensitive data. How do you ensure data isn't logged?
- A) System prompt instruction
- B) Programmatic hook preventing sensitive data logging
- C) Trust the agent
- D) Use encryption

**Q74.** An agent loop has run 50 iterations with stop_reason "tool_use" each time. What's likely wrong?
- A) Normal behavior
- B) Tool results not being appended to messages
- C) Need more iterations
- D) Temperature too high

**Q75.** Your agent needs to explore a codebase. The exploration generates 100KB of notes. What should you do?
- A) Keep all notes in context
- B) Use scratchpad file or subagent delegation
- C) Summarize after each file
- D) Reduce exploration scope

**Q76.** A subagent needs access to 3 specific tools. How do you configure this?
- A) Give all tools to all agents
- B) Scoped tool access with allowedTools
- C) Let agent choose
- D) One tool per subagent

**Q77.** Your agent must follow GDPR compliance rules. How do you enforce this?
- A) Detailed system prompt
- B) Programmatic hooks for data access/deletion
- C) Agent training
- D) User guidelines

**Q78.** An agent is making too many tool calls (20+ per task). What's the likely issue?
- A) Normal for complex tasks
- B) Poor task decomposition or unclear tool descriptions
- C) Need more tools
- D) Temperature too high

**Q79.** You need an agent to retry failed operations up to 3 times. Where should this logic be?
- A) In the system prompt
- B) In the agentic loop or hook logic
- C) In tool descriptions
- D) User handles retries

**Q80.** A coordinator spawns subagents but they all fail with "context not found". What's wrong?
- A) Subagents are broken
- B) Context not being explicitly passed
- C) Need more memory
- D) Wrong model

**Q81.** Your agent needs to process items in a specific order. How do you ensure this?
- A) Hope agent figures it out
- B) Explicit sequencing in prompt or workflow
- C) Use alphabetical order
- D) Random is fine

**Q82.** An agent is escalating 80% of requests when only 20% should escalate. What's wrong?
- A) Escalation rules too strict
- B) Escalation logic based on unreliable signals (sentiment, confidence)
- C) Need more training
- D) Temperature too low

**Q83.** You're implementing crash recovery for long-running agents. What should you save?
- A) Nothing, restart from scratch
- B) State manifest with completed steps and current position
- C) Full conversation history
- D) Only final results

**Q84.** A tool sometimes returns data in different formats. How should this be handled?
- A) Agent should figure it out
- B) PostToolUse hook for normalization
- C) Ignore format differences
- D) Return error

**Q85.** Your agent needs to coordinate 3 sequential tasks with dependencies. What's the best pattern?
- A) Single agent with all tools
- B) Coordinator with 3 specialized subagents
- C) 3 separate sessions
- D) Prompt chaining

**Q86.** An agent is using arbitrary iteration cap (10) as primary stop mechanism. What's wrong?
- A) Nothing, this is correct
- B) Should use stop_reason, cap is safety only
- C) Cap too low
- D) Cap too high

**Q87.** You need to track which tools are being used most frequently. Where should this be implemented?
- A) System prompt
- B) PostToolUse hook with logging
- C) Tool descriptions
- D) Manual tracking

**Q88.** A subagent needs to make 50 API calls. What's the best approach?
- A) 50 individual tool calls
- B) Batch processing tool or pagination
- C) Multiple subagents
- D) Increase timeout

**Q89.** Your agent must validate all inputs before processing. Where should validation occur?
- A) In system prompt
- B) PreToolUse hook or tool input validation
- C) After processing
- D) User validates

**Q90.** An agent is returning "I'm done!" but stop_reason is "tool_use". What should you do?
- A) Trust the text response
- B) Continue loop, check stop_reason
- C) End immediately
- D) Ask user

---

## Advanced Level (45 Questions)

**Q91.** You're building a multi-agent research system. The coordinator delegates to a search subagent, which finds 100 articles. The analysis subagent can only handle 10 at a time. How should you architect this?
- A) Pass all 100, let analyzer handle it
- B) Coordinator filters to top 10 before delegating
- C) Search subagent spawns 10 analysis subagents in parallel
- D) Coordinator spawns 10 analysis subagents with 10 articles each

**Q92.** Your agent system has a coordinator that spawns subagents, which themselves spawn sub-subagents. What's the primary concern?
- A) Cost
- B) Coordination complexity and error propagation depth
- C) Speed
- D) Context limits

**Q93.** You need to implement a policy that blocks refunds over $500 unless approved by a manager. The approval process involves calling an external API. How should this be architected?
- A) System prompt with approval instructions
- B) PostToolUse hook that calls approval API and blocks if not approved
- C) Separate approval agent
- D) User handles approvals

**Q94.** Your agent loop is checking stop_reason correctly, but still runs indefinitely. Tool results are being appended. What's the most likely subtle issue?
- A) stop_reason not updating
- B) Tool results appended to wrong message role
- C) Network issues
- D) Model error

**Q95.** You're building a code review system with a generator agent and reviewer agent. The reviewer finds issues 90% of the time, even for good code. What's the likely cause?
- A) Reviewer is too strict
- B) Same-session review retaining generator's reasoning context
- C) Generator is bad
- D) Need different model

**Q96.** A coordinator needs to synthesize results from 5 subagents, but 2 return conflicting information with equal confidence. How should the coordinator resolve this?
- A) Choose first result
- B) Spawn additional verification subagent or escalate with conflict annotation
- C) Average the results
- D) Ignore conflicting data

**Q97.** Your multi-agent system processes 1000 tasks/day. You notice 30% of tasks fail due to subagent timeouts. What's the best optimization?
- A) Increase all timeouts
- B) Implement retry logic with exponential backoff and circuit breakers
- C) Add more subagents
- D) Reduce task complexity

**Q98.** You're implementing a hook that must enforce data retention policies (delete data after 90 days). Where should the date checking logic be?
- A) In the system prompt
- B) In PostToolUse hook for data access tools
- C) In tool descriptions
- D) Scheduled external job

**Q99.** An agent needs to process a 500-page document. Context window is 200K tokens. The document is 300K tokens. What's the best approach?
- A) Increase context window
- B) Chunk document, process with subagents, coordinator synthesizes
- C) Summarize document first
- D) Skip some pages

**Q100.** Your agent system must maintain audit logs showing exact decision paths. How should this be implemented?
- A) Agent self-reports decisions
- B) Hooks log all tool calls with context and results
- C) User manually logs
- D) Database triggers

**Q101.** You're building a trading agent that must never execute trades over $10K without human approval. The approval process takes 5-30 minutes. How do you handle this?
- A) Block in PostToolUse hook, poll approval API
- B) Async workflow with callback
- C) System prompt instruction
- D) Separate approval agent

**Q102.** A coordinator spawns 10 subagents in parallel. 3 fail immediately, 5 succeed, 2 timeout. How should partial results be structured?
- A) Return only successful results
- B) Structured response with completed/failed/timeout arrays and error context
- C) Retry all failures
- D) Return error

**Q103.** Your agent needs to maintain state across 100 sequential operations. Context window limits prevent keeping all history. What's the best approach?
- A) Increase context window
- B) Scratchpad file with state checkpoints, periodic context reset
- C) Database storage
- D) Reduce operations

**Q104.** You're implementing a multi-pass review system. Pass 1 analyzes per-file. Pass 2 does cross-file integration. How should context be managed?
- A) Same session for both passes
- B) Fresh session for Pass 2 with Pass 1 findings as input
- C) Two separate agents
- D) Parallel execution

**Q105.** An agent must enforce complex business rules with 20 conditions. Some conditions require external API calls. How should this be architected?
- A) All in system prompt
- B) Programmatic rule engine in hooks with async API calls
- C) Separate rules agent
- D) User validates

**Q106.** Your coordinator uses dynamic task decomposition. It sometimes creates overly narrow subtasks, causing coverage gaps. How do you fix this?
- A) Switch to prompt chaining
- B) Improve coordinator prompt with decomposition guidelines and examples
- C) Add more subagents
- D) Use higher temperature

**Q107.** You need to implement rate limiting (max 100 tool calls/hour) across all agents. Where should this be enforced?
- A) System prompts
- B) Global hook or middleware tracking all tool calls
- C) Individual tool limits
- D) User monitoring

**Q108.** A subagent needs to explore 50 solution paths but only return the best 3. What's the most efficient approach?
- A) Sequential exploration in one session
- B) Fork 50 sessions, compare results, return top 3
- C) Coordinator spawns 50 parallel subagents
- D) Reduce to 3 paths upfront

**Q109.** Your agent system has 5 layers of delegation (coordinator → subagent → sub-subagent → ...). Errors are getting lost. How do you fix error propagation?
- A) Flatten architecture
- B) Structured error propagation with full context chain at each level
- C) Only report final errors
- D) Add more logging

**Q110.** You're building a compliance agent that must block certain operations based on user role. Roles are fetched from external API. How should this be implemented?
- A) System prompt with role instructions
- B) PreToolUse hook that fetches role and blocks unauthorized operations
- C) Separate authorization agent
- D) User handles authorization

**Q111.** An agent needs to coordinate 3 subagents with complex dependencies (B depends on A, C depends on A and B). How should the coordinator manage this?
- A) Parallel execution
- B) Explicit dependency graph with sequential execution
- C) Let subagents figure it out
- D) Prompt chaining

**Q112.** Your agent system processes sensitive healthcare data. You must ensure PHI is never logged. How do you enforce this?
- A) Trust system prompts
- B) Hooks that detect and redact PHI before logging
- C) Manual review
- D) Encryption only

**Q113.** A coordinator needs to make a decision based on 10 subagent results, but results arrive asynchronously over 30 seconds. How should this be handled?
- A) Wait for all results
- B) Async coordination with timeout and partial result handling
- C) Use fastest result only
- D) Synchronous execution

**Q114.** You're implementing a self-healing agent that detects and recovers from errors. What's the key challenge?
- A) Error detection
- B) Distinguishing recoverable vs non-recoverable errors
- C) Speed
- D) Cost

**Q115.** Your agent must maintain consistency across 5 distributed databases. How should transaction management be handled?
- A) Agent handles transactions
- B) Programmatic transaction coordinator with rollback capability
- C) Hope for the best
- D) Manual intervention

**Q116.** A coordinator spawns subagents that each take 30-60 seconds. You need to optimize for latency. What's the best approach?
- A) Faster model
- B) Parallel execution + streaming responses
- C) Reduce subagent count
- D) Caching

**Q117.** You're building an agent that must comply with GDPR right-to-deletion. How do you ensure complete data removal?
- A) System prompt instruction
- B) Programmatic deletion hooks with verification across all systems
- C) Manual deletion
- D) Database cascading deletes

**Q118.** Your agent system has variable latency (1-30 seconds per operation). How do you handle user experience?
- A) Make everything faster
- B) Streaming responses + progress indicators + async patterns
- C) Increase timeout
- D) Reduce functionality

**Q119.** A coordinator needs to optimize subagent allocation based on current load. How should this be implemented?
- A) Fixed allocation
- B) Dynamic load balancing with health checks and circuit breakers
- C) Random allocation
- D) User chooses

**Q120.** You're implementing a hook that must call 3 external APIs sequentially. One API is slow (5 seconds). How do you handle this?
- A) Synchronous calls, accept latency
- B) Async calls with timeout and fallback
- C) Skip slow API
- D) Cache results

**Q121.** Your agent must enforce a complex approval workflow (L1 → L2 → L3 approval based on amount). How should this be architected?
- A) System prompt with workflow
- B) State machine in hooks with external approval API integration
- C) Separate approval agent
- D) User manages workflow

**Q122.** A coordinator needs to aggregate results from 100 subagents. Context window can't hold all results. What's the solution?
- A) Increase context window
- B) Hierarchical aggregation (10 groups of 10, then final aggregation)
- C) Sample results
- D) External storage

**Q123.** You're building an agent that must maintain ACID properties across operations. How do you ensure atomicity?
- A) Agent handles it
- B) Transaction wrapper with commit/rollback in hooks
- C) Database handles it
- D) Not possible

**Q124.** Your agent system must support rollback of the last N operations. How should this be implemented?
- A) Agent remembers
- B) Operation log with inverse operations in hooks
- C) Database transactions
- D) Manual undo

**Q125.** A coordinator spawns subagents that modify shared state. How do you prevent race conditions?
- A) Sequential execution only
- B) Locking mechanism or optimistic concurrency control
- C) Hope for the best
- D) Separate state per subagent

**Q126.** You're implementing an agent that must maintain eventual consistency across 3 systems. How do you handle temporary inconsistencies?
- A) Prevent them
- B) Reconciliation process with conflict resolution
- C) Ignore them
- D) Manual fixes

**Q127.** Your agent must enforce SLA (95% of operations complete in <5 seconds). How do you monitor and enforce this?
- A) System prompt
- B) Instrumentation + timeout enforcement + fallback paths
- C) Hope for the best
- D) User monitors

**Q128.** A coordinator needs to implement circuit breaker pattern for failing subagents. What's the key logic?
- A) Retry forever
- B) Track failure rate, open circuit after threshold, close after cooldown
- C) Fail immediately
- D) User intervention

**Q129.** You're building an agent that must support multi-tenancy with strict data isolation. How do you enforce this?
- A) System prompt per tenant
- B) Hooks that validate tenant context on every operation
- C) Separate agents per tenant
- D) Database row-level security

**Q130.** Your agent system must support graceful degradation when external services fail. How should this be architected?
- A) Fail completely
- B) Fallback chains with reduced functionality
- C) Retry indefinitely
- D) User handles failures

**Q131.** A coordinator needs to implement saga pattern for distributed transactions. What's the key requirement?
- A) All-or-nothing execution
- B) Compensating transactions for each step
- C) Synchronous execution
- D) Single database

**Q132.** You're implementing an agent that must maintain causal consistency. How do you track causality?
- A) Timestamps
- B) Vector clocks or causal graphs
- C) Sequential IDs
- D) Not needed

**Q133.** Your agent must support idempotent operations (safe to retry). How do you implement this?
- A) Never retry
- B) Idempotency keys + deduplication logic
- C) Hope for the best
- D) User tracks

**Q134.** A coordinator needs to implement backpressure when subagents are overloaded. What's the mechanism?
- A) Ignore overload
- B) Queue management with flow control
- C) Add more subagents
- D) Fail requests

**Q135.** You're building an agent that must support distributed tracing across all operations. How should this be implemented?
- A) Manual logging
- B) Trace context propagation through all tool calls and subagents
- C) Database logs
- D) Not needed

---

**Next:** [Domain 2 Questions →](./Domain-2-Questions.md)
