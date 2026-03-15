# Domain 2: Tool Design & MCP Integration (90 Questions)

**Weight: 18% of exam**

This domain covers tool design principles, MCP configuration, error handling, and built-in tools.

---

## Beginner Level (30 Questions)

**Q136.** What are the three essential parts of a tool definition?
- A) Name, price, speed
- B) Name, description, parameters ✓
- C) Input, output, error
- D) Type, format, schema

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q137.** What should a tool name describe?
- A) The developer who created it
- B) What the tool does ✓
- C) The technology used
- D) The version number

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q138.** Which is a good tool name?
- A) tool1
- B) helper
- C) search_customer ✓
- D) do_thing

**Answer: C** | **Explanation:** Clear, descriptive name indicating function.

**Q139.** What should a tool description include?
- A) Only the tool name
- B) What it does, what it needs, what it returns ✓
- C) Just the parameters
- D) The code implementation

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q140.** What does "required" mean for a parameter?
- A) Nice to have
- B) Must be provided for tool to work ✓
- C) Optional
- D) Deprecated

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q141.** Which parameter type is used for text?
- A) number
- B) string ✓
- C) boolean
- D) array

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q142.** What is a boolean parameter?
- A) Text value
- B) Number value
- C) True/false value ✓
- D) List of values

**Answer: C** | **Explanation:** Boolean represents binary true/false.

**Q143.** How many tools per agent is optimal?
- A) 1-2
- B) 4-5 ✓
- C) 18+
- D) As many as possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q144.** What happens when an agent has 18+ tools?
- A) Better performance
- B) Tool selection quality degrades ✓
- C) Faster execution
- D) Lower costs

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q145.** What is an MCP server?
- A) A database
- B) A standardized way to provide tools and resources ✓
- C) A web server
- D) A file system

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q146.** What are the three MCP configuration scopes?
- A) Small, medium, large
- B) Local, project, user ✓
- C) Dev, test, prod
- D) Public, private, shared

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q147.** Where is project-scope MCP configuration stored?
- A) ~/.claude.json
- B) .mcp.json in project root ✓
- C) /etc/mcp.json
- D) C:\mcp\config.json

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q148.** Where is user-scope MCP configuration stored?
- A) .mcp.json
- B) ~/.claude.json ✓
- C) /var/mcp.json
- D) project.json

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q149.** What is the MCP scope hierarchy?
- A) Local > Project > User
- B) Managed > Project > User > Local ✓
- C) User > Project > Local
- D) Project > User > Managed

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q150.** What does ${VAR} do in MCP configuration?
- A) Creates a variable
- B) Expands to environment variable value ✓
- C) Deletes a variable
- D) Comments out the line

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q151.** What does ${VAR:-default} mean?
- A) VAR or default if VAR not set ✓
- B) VAR minus default
- C) VAR divided by default
- D) Error if VAR not set

**Answer: A** | **Explanation:** VAR or default if VAR not set.

**Q152.** Which built-in tool reads files?
- A) Write
- B) Read ✓
- C) Edit
- D) Bash

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q153.** Which built-in tool modifies existing files?
- A) Read
- B) Write ✓
- C) Edit
- D) Grep

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q154.** Which built-in tool searches file contents?
- A) Read
- B) Glob ✓
- C) Grep
- D) Bash

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q155.** Which built-in tool finds files by pattern?
- A) Read
- B) Grep ✓
- C) Glob
- D) Edit

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q156.** When should you use Edit instead of Write?
- A) Creating new files
- B) Modifying existing files ✓
- C) Deleting files
- D) Reading files

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q157.** What should a tool return when it encounters an error?
- A) Nothing
- B) Structured error with details ✓
- C) Generic "error" message
- D) Crash

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q158.** What does "isRetryable" indicate in an error response?
- A) Error severity
- B) Whether operation can be retried ✓
- C) Error category
- D) User permission

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q159.** Which error should have isRetryable: true?
- A) Authentication failed
- B) Rate limit exceeded ✓
- C) Resource not found
- D) Invalid input

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q160.** Which error should have isRetryable: false?
- A) Network timeout
- B) Service temporarily unavailable
- C) Invalid email format ✓
- D) Rate limit exceeded

**Answer: C** | **Explanation:** Validation errors are not retryable.

**Q161.** What is tool_choice: "auto"?
- A) Force tool use
- B) Let Claude decide whether to use tools ✓
- C) Use specific tool
- D) Disable tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q162.** What is tool_choice: "any"?
- A) Use all tools
- B) Force tool use, Claude picks which ✓
- C) Random tool
- D) No tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q163.** What is tool_choice: {"type": "tool", "name": "search"}?
- A) Suggest search tool
- B) Force use of search tool ✓
- C) Disable search tool
- D) Optional search

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q164.** When should you use tool_choice: "any"?
- A) Never
- B) When you need guaranteed tool use (structured output) ✓
- C) Always
- D) For debugging only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q165.** What is a common tool design mistake?
- A) Clear descriptions
- B) Vague descriptions like "does stuff" ✓
- C) Required parameters
- D) Error handling

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

## Intermediate Level (30 Questions)

**Q166.** You're designing a search_customer tool. It needs to search by email, phone, or customer ID. How should parameters be structured?
- A) Three separate tools
- B) One tool with search_type and search_value parameters ✓
- C) One parameter that accepts any format
- D) Email only, other tools for phone/ID

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q167.** A tool sometimes returns data in JSON, sometimes in XML. How should this be handled?
- A) Agent figures it out
- B) Normalize to consistent format in tool or hook ✓
- C) Return both formats
- D) Error on XML

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q168.** Your tool needs 15 parameters. What's the issue?
- A) Nothing wrong
- B) Too many parameters, should group related ones ✓
- C) Need more parameters
- D) Should be 15 separate tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q169.** A tool description says "Gets data". What's wrong?
- A) Perfect
- B) Too vague, needs specifics about what data, how, and what's returned ✓
- C) Too long
- D) Missing version

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q170.** You're configuring MCP for a team project. Which scope should you use?
- A) Local
- B) Project (.mcp.json in repo) ✓
- C) User
- D) Global

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q171.** You need to store sensitive API keys for MCP. Which approach is best?
- A) Hardcode in .mcp.json
- B) Environment variables with ${API_KEY} ✓
- C) Commit to git
- D) Plain text file

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q172.** An MCP server needs different configuration for dev vs prod. How should this be handled?
- A) Two separate .mcp.json files
- B) Environment variables with defaults: ${ENV:-dev} ✓
- C) Hardcode both
- D) Manual switching

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q173.** You have a project .mcp.json and user ~/.claude.json with same server name. Which takes precedence?
- A) User config
- B) Project config ✓
- C) Merge both
- D) Error

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q174.** A tool returns 10MB of data. What should you do?
- A) Return all data
- B) Paginate or summarize in tool ✓
- C) Increase limits
- D) Error

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q175.** You're designing a send_email tool. What parameters are required?
- A) Only "to"
- B) to, subject, body (cc/bcc optional) ✓
- C) Just body
- D) All fields required

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q176.** A tool needs to call an external API that's sometimes slow (10+ seconds). How should this be handled?
- A) No timeout
- B) Timeout with error handling and retry logic ✓
- C) Increase all timeouts
- D) Synchronous wait

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q177.** You have 20 related tools (search_by_email, search_by_phone, etc.). What's better?
- A) Keep all 20 separate
- B) Combine into flexible search tool with type parameter ✓
- C) Remove most tools
- D) Add more tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q178.** A tool must validate input before execution. Where should validation occur?
- A) After execution
- B) In tool implementation before processing ✓
- C) Agent handles it
- D) User validates

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q179.** You're designing a tool that creates resources. What should it return?
- A) Nothing
- B) Created resource ID and confirmation ✓
- C) Just "success"
- D) Full resource details

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q180.** A tool fails 30% of the time due to external API issues. How should errors be structured?
- A) Generic "failed"
- B) Specific error category, isRetryable: true, retry guidance ✓
- C) Silent failure
- D) Crash

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q181.** You need a tool to process batches of 100 items. What's the best design?
- A) 100 separate tool calls
- B) Single tool with array parameter ✓
- C) 100 separate tools
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q182.** A tool returns empty results. Should this be an error?
- A) Yes, always error
- B) No, valid result (no data found) ✓
- C) Depends on user preference
- D) Retry until data found

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q183.** You're adding a new tool to an agent that already has 5 tools. What should you consider?
- A) Just add it
- B) Evaluate if it's necessary or can combine with existing ✓
- C) Remove an old tool first
- D) Create new agent

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q184.** A tool needs to support both sync and async execution. How should this be designed?
- A) Two separate tools
- B) Parameter to specify sync/async mode ✓
- C) Always async
- D) Always sync

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q185.** You're designing error responses. What should errorCategory be?
- A) Generic "error"
- B) Specific category (auth_failed, not_found, rate_limit, etc.) ✓
- C) Error code number
- D) Full stack trace

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q186.** A tool must enforce business rules (e.g., max quantity 100). Where should this be?
- A) System prompt
- B) Tool implementation validation ✓
- C) Agent decides
- D) User enforces

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q187.** You need to add examples to a tool description. Where should they go?
- A) Separate documentation
- B) In the description field ✓
- C) Not needed
- D) Code comments

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q188.** A tool needs to access multiple databases. How should this be configured?
- A) Hardcode connections
- B) MCP configuration with environment variables ✓
- C) User provides each time
- D) Random selection

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q189.** You're designing a tool that modifies data. What should it return?
- A) Nothing
- B) Success confirmation and what was modified ✓
- C) Just "done"
- D) Full new state

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q190.** A tool has optional parameters with defaults. How should defaults be documented?
- A) Not documented
- B) In parameter description with default value ✓
- C) Separate file
- D) Code only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q191.** You need to deprecate a tool. What's the best approach?
- A) Delete immediately
- B) Mark deprecated in description, provide alternative, sunset timeline ✓
- C) Just remove
- D) Keep forever

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q192.** A tool returns different data based on user permissions. How should this be handled?
- A) Return all data
- B) Filter in tool based on user context ✓
- C) Agent filters
- D) User filters

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q193.** You're designing a tool for a multi-tenant system. What must be included?
- A) Nothing special
- B) Tenant context parameter for isolation ✓
- C) Separate tools per tenant
- D) Shared data

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q194.** A tool needs to log all operations for audit. Where should logging occur?
- A) Agent logs
- B) Tool implementation or PostToolUse hook ✓
- C) User logs
- D) Database triggers

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q195.** You have tools that are only valid in certain contexts. How do you handle this?
- A) Always available
- B) Conditional tool availability based on context ✓
- C) Agent figures it out
- D) User manages

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

## Advanced Level (30 Questions)

**Q196.** You're designing a tool ecosystem for a complex application with 50+ potential operations. How should you structure tools to stay within the 4-5 tool limit per agent?
- A) Create 50 tools, use all
- B) Hierarchical tool design with context-specific tool sets per agent ✓
- C) One mega-tool
- D) Limit functionality

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q197.** A tool must maintain idempotency (safe to retry with same inputs). How should this be implemented?
- A) Hope for the best
- B) Idempotency key parameter + deduplication logic ✓
- C) Never retry
- D) User tracks

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q198.** You're designing an MCP server that must support multiple authentication methods (OAuth, API key, JWT). How should this be structured?
- A) Separate servers per auth method
- B) Single server with auth method configuration ✓
- C) No authentication
- D) Hardcode one method

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q199.** A tool needs to support pagination for large result sets. What's the best parameter design?
- A) Return all results
- B) page_number and page_size parameters with continuation token ✓
- C) Limit to first 10
- D) Multiple tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q200.** You're implementing a tool that must support transactions (commit/rollback). How should this be designed?
- A) Auto-commit
- B) Transaction ID parameter with explicit commit/rollback tools ✓
- C) No transactions
- D) Database handles it

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q201.** A tool must integrate with 5 different external APIs with different rate limits. How should rate limiting be handled?
- A) No rate limiting
- B) Per-API rate limit tracking with backoff and queuing ✓
- C) Single global limit
- D) User manages

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q202.** You're designing a tool that must support both real-time and batch processing. How should this be architected?
- A) Two separate tools
- B) Mode parameter with different execution paths ✓
- C) Always real-time
- D) Always batch

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q203.** A tool needs to support complex filtering with 20+ possible filter criteria. How should parameters be designed?
- A) 20 separate parameters
- B) Filter object parameter with schema ✓
- C) Limit to 5 filters
- D) No filtering

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q204.** You're implementing a tool that must support versioning (v1, v2 APIs). How should this be handled?
- A) Separate tools per version
- B) Version parameter with backward compatibility ✓
- C) Latest version only
- D) User specifies in description

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q205.** A tool must support partial updates (PATCH semantics). How should parameters be designed?
- A) All fields required
- B) All fields optional, only provided fields updated ✓
- C) Separate tool per field
- D) Full replacement only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q206.** You're designing an MCP server that must support dynamic tool discovery (tools change at runtime). How should this be implemented?
- A) Static tool list
- B) Tool registry with refresh mechanism ✓
- C) Restart server for changes
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q207.** A tool must support conditional execution (only execute if condition met). How should this be designed?
- A) Agent checks condition
- B) Condition parameter with pre-execution validation ✓
- C) Always execute
- D) Separate validation tool

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q208.** You're implementing a tool that must support distributed tracing. What must be included?
- A) Nothing
- B) Trace context parameters (trace_id, span_id) ✓
- C) Logging only
- D) User tracks

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q209.** A tool needs to support both optimistic and pessimistic locking. How should this be parameterized?
- A) Always optimistic
- B) Lock mode parameter with version/lock token ✓
- C) Always pessimistic
- D) No locking

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q210.** You're designing a tool that must support circuit breaker pattern. Where should circuit breaker logic be?
- A) Agent handles it
- B) Tool implementation with state tracking ✓
- C) External service
- D) Not needed

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q211.** A tool must support request deduplication across multiple agents. How should this be implemented?
- A) Each agent tracks separately
- B) Global deduplication with request fingerprinting ✓
- C) No deduplication
- D) User manages

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q212.** You're implementing a tool that must support saga pattern for distributed transactions. What parameters are needed?
- A) None
- B) Saga ID and compensation data ✓
- C) Just transaction ID
- D) Not supported

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q213.** A tool needs to support both synchronous and asynchronous callbacks. How should this be designed?
- A) Sync only
- B) Callback URL parameter with webhook support ✓
- C) Async only
- D) Polling only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q214.** You're designing a tool for multi-tenancy with strict isolation. What's required?
- A) Nothing special
- B) Tenant ID parameter with validation and scoping ✓
- C) Separate tools per tenant
- D) Shared access

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q215.** A tool must support audit logging with tamper-proof records. How should this be implemented?
- A) Simple logging
- B) Cryptographic signing of log entries ✓
- C) Database logs
- D) Not needed

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q216.** You're implementing a tool that must support graceful degradation when dependencies fail. How should this be designed?
- A) Fail completely
- B) Fallback chain with reduced functionality ✓
- C) Retry forever
- D) No degradation

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q217.** A tool needs to support both streaming and batch responses. How should this be parameterized?
- A) Always batch
- B) Response mode parameter with streaming support ✓
- C) Always streaming
- D) Two tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q218.** You're designing a tool that must support request prioritization. What parameters are needed?
- A) None
- B) Priority level with queue management ✓
- C) FIFO only
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q219.** A tool must support both synchronous and event-driven execution. How should this be architected?
- A) Sync only
- B) Execution mode parameter with event subscription ✓
- C) Events only
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q220.** You're implementing a tool that must support distributed caching. What's required?
- A) No caching
- B) Cache key strategy and invalidation parameters ✓
- C) Local cache only
- D) Database caching

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q221.** A tool needs to support both CRUD and search operations. Should this be one tool or multiple?
- A) One mega-tool
- B) Separate tools per operation type for clarity ✓
- C) Search only
- D) CRUD only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q222.** You're designing a tool that must support schema evolution (backward/forward compatibility). How should this be handled?
- A) Break compatibility
- B) Version negotiation and schema migration ✓
- C) Latest schema only
- D) Never change schema

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q223.** A tool must support both pull and push data models. How should this be designed?
- A) Pull only
- B) Mode parameter with webhook registration for push ✓
- C) Push only
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q224.** You're implementing a tool that must support request batching with partial failures. How should responses be structured?
- A) All or nothing
- B) Per-item status with success/failure arrays ✓
- C) Fail entire batch
- D) No batching

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q225.** A tool needs to support both optimistic and pessimistic concurrency control. How should this be parameterized?
- A) Optimistic only
- B) Concurrency mode with version tokens or locks ✓
- C) Pessimistic only
- D) No concurrency control

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

**Next:** [Domain 3 Questions →](./Domain-3-Questions.md)
