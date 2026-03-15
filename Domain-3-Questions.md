# Domain 3: Claude Code Configuration & Workflows (100 Questions)

**Weight: 20% of exam**

This domain covers CLAUDE.md configuration, skills, commands, plan mode, and CI/CD integration.

---

## Beginner Level (35 Questions)

**Q226.** What is the main configuration file for Claude Code projects?
- A) config.json
- B) CLAUDE.md
- C) settings.ini
- D) project.yaml

**Q227.** Where should the project CLAUDE.md file be located?
- A) In your home directory
- B) In .claude/ folder at project root
- C) Anywhere in the project
- D) In /etc/claude/

**Q228.** What is the recommended maximum length for CLAUDE.md?
- A) 50 lines
- B) 200 lines
- C) 1000 lines
- D) No limit

**Q229.** Why should CLAUDE.md be kept under 200 lines?
- A) File size limits
- B) Every low-value instruction degrades high-value ones
- C) Faster loading
- D) Git limitations

**Q230.** What is the CLAUDE.md hierarchy?
- A) User > Project > Directory
- B) Directory-specific > Project > User
- C) Project > User > Directory
- D) All equal priority

**Q231.** Where is the user-level CLAUDE.md located?
- A) .claude/CLAUDE.md
- B) ~/.claude/CLAUDE.md
- C) /etc/CLAUDE.md
- D) C:\CLAUDE.md

**Q232.** What does @import do in CLAUDE.md?
- A) Imports code
- B) Includes another configuration file
- C) Imports dependencies
- D) Downloads files

**Q233.** Where should topic-specific rules be stored?
- A) All in main CLAUDE.md
- B) .claude/rules/ directory
- C) Separate repository
- D) Database

**Q234.** What are slash commands used for?
- A) Code comments
- B) Simple, single-purpose actions
- C) File paths
- D) Mathematical operations

**Q235.** Where are custom slash commands stored?
- A) .claude/commands/
- B) .claude/skills/
- C) ~/commands/
- D) /usr/local/commands/

**Q236.** What are skills in Claude Code?
- A) User abilities
- B) Complex, multi-step workflows
- C) Programming languages
- D) Tool configurations

**Q237.** Where are skills stored?
- A) .claude/commands/
- B) .claude/skills/
- C) ~/skills/
- D) /etc/skills/

**Q238.** What is the difference between commands and skills?
- A) No difference
- B) Commands are simple actions, skills are complex workflows
- C) Commands are faster
- D) Skills are deprecated

**Q239.** What does the frontmatter in SKILL.md contain?
- A) Code
- B) Configuration (context, allowed-tools, etc.)
- C) Documentation
- D) Examples

**Q240.** What does `context: fork` mean in SKILL.md?
- A) Use Git fork
- B) Run skill in isolated session
- C) Create new branch
- D) Duplicate files

**Q241.** What is the `allowed-tools` frontmatter option?
- A) Tools the user can access
- B) Restrict which tools the skill can use
- C) Recommended tools
- D) Deprecated tools

**Q242.** What is `argument-hint` in SKILL.md?
- A) Required arguments
- B) Help text for skill arguments
- C) Default values
- D) Type hints

**Q243.** When should you use plan mode?
- A) Always
- B) Complex refactoring, high-risk changes, unfamiliar code
- C) Never
- D) Only for new projects

**Q244.** When should you use direct execution?
- A) Never
- B) Well-understood tasks, low-risk changes, quick fixes
- C) Always
- D) Only for testing

**Q245.** How do you activate plan mode?
- A) --plan or -p flag
- B) --mode plan
- C) -m flag
- D) plan: true in config

**Q246.** What is the benefit of plan mode?
- A) Faster execution
- B) Review proposed changes before execution
- C) Lower costs
- D) Better accuracy

**Q247.** What flag enables non-interactive mode in CI/CD?
- A) --silent
- B) -p (prompt flag)
- C) --no-input
- D) --batch

**Q248.** What does --output-format json do?
- A) Formats code as JSON
- B) Returns structured JSON output
- C) Validates JSON
- D) Converts files to JSON

**Q249.** What is --json-schema used for?
- A) Creating schemas
- B) Validating output against schema
- C) Converting to JSON
- D) Schema documentation

**Q250.** What is the Message Batches API used for?
- A) Sending emails
- B) Processing multiple requests with cost savings
- C) Batch file operations
- D) Database batching

**Q251.** What cost savings does the Batch API provide?
- A) 25%
- B) 50%
- C) 75%
- D) 90%

**Q252.** What is the processing window for Batch API?
- A) 1 hour
- B) 24 hours
- C) 1 week
- D) Instant

**Q253.** What is custom_id used for in Batch API?
- A) User identification
- B) Tracking individual batch requests
- C) API authentication
- D) Version control

**Q254.** What is iterative refinement?
- A) Code optimization
- B) Progressively improving output through multiple prompts
- C) Performance tuning
- D) Bug fixing

**Q255.** What is the TDD iteration pattern?
- A) Write code, then tests
- B) Write failing test, implement code, refactor
- C) Test everything
- D) Debug-driven development

**Q256.** What is the interview pattern in iterative refinement?
- A) Job interviews
- B) Ask Claude to propose approach, review, iterate
- C) User interviews
- D) Code reviews

**Q257.** Why use fresh session for code review?
- A) Faster execution
- B) Avoid confirmation bias from generation session
- C) Lower costs
- D) Better formatting

**Q258.** What is multi-pass review?
- A) Multiple reviewers
- B) Per-file analysis, then cross-file integration in fresh session
- C) Reviewing multiple times
- D) Automated testing

**Q259.** What should Pass 1 of multi-pass review focus on?
- A) Everything
- B) Per-file local analysis
- C) Integration
- D) Performance

**Q260.** What should Pass 2 of multi-pass review focus on?
- A) Same as Pass 1
- B) Cross-file integration and architecture
- C) Documentation
- D) Testing

---

## Intermediate Level (35 Questions)

**Q261.** You're configuring Claude Code for a React project. Where should you specify that all components must use TypeScript strict mode?
- A) In every file
- B) In .claude/CLAUDE.md
- C) In package.json
- D) In README

**Q262.** Your CLAUDE.md is 500 lines long with many low-priority suggestions. What's the issue?
- A) Nothing wrong
- B) Too long, low-value instructions degrade high-value ones
- C) Should be longer
- D) Wrong format

**Q263.** You need different rules for API files vs component files. How should you organize this?
- A) One CLAUDE.md with all rules
- B) Path-specific rules with YAML frontmatter
- C) Separate projects
- D) Multiple agents

**Q264.** A skill needs to run in isolation without affecting the main session. What frontmatter should you use?
- A) context: main
- B) context: fork
- C) isolated: true
- D) session: new

**Q265.** You're creating a skill that should only use Read and Edit tools. How do you restrict this?
- A) System prompt
- B) allowed-tools: ["Read", "Edit"] in frontmatter
- C) Can't restrict
- D) Remove other tools

**Q266.** Your skill requires a file path argument. How do you provide a hint to users?
- A) In documentation
- B) argument-hint in frontmatter
- C) In skill name
- D) Not possible

**Q267.** You're refactoring authentication across 20 files. Should you use plan mode or direct execution?
- A) Direct execution for speed
- B) Plan mode to review changes before execution
- C) Doesn't matter
- D) Neither

**Q268.** A quick bug fix in a well-understood file. Plan mode or direct execution?
- A) Plan mode for safety
- B) Direct execution for speed
- C) Always plan mode
- D) Ask user

**Q269.** You're integrating Claude Code into CI/CD. What flag ensures non-interactive execution?
- A) --interactive=false
- B) -p (prompt flag for non-interactive)
- C) --ci
- D) --auto

**Q270.** Your CI/CD pipeline needs structured JSON output for parsing. What flag do you use?
- A) --json
- B) --output-format json
- C) --format=json
- D) -j

**Q271.** You need to validate Claude's output against a specific JSON schema in CI. What do you use?
- A) Manual validation
- B) --json-schema schema.json
- C) --validate
- D) Not possible

**Q272.** You're processing 1000 code reviews. What's the most cost-effective approach?
- A) 1000 individual API calls
- B) Message Batches API (50% savings)
- C) Parallel processing
- D) Sequential processing

**Q273.** In Batch API, how do you track which response belongs to which request?
- A) Order in array
- B) custom_id field
- C) Timestamps
- D) Request hash

**Q274.** You need results within 1 hour. Should you use Batch API?
- A) Yes, it's faster
- B) No, Batch API has 24-hour window
- C) Yes, with priority flag
- D) Doesn't matter

**Q275.** You're building a component and want to iterate: basic structure → add features → add tests. What pattern is this?
- A) Waterfall
- B) Iterative refinement with concrete examples
- C) Agile
- D) Random

**Q276.** You want Claude to propose an approach before implementing. What pattern should you use?
- A) Direct implementation
- B) Interview pattern (propose, review, iterate)
- C) TDD
- D) Waterfall

**Q277.** You're using TDD with Claude. What's the correct order?
- A) Write code, write tests, refactor
- B) Write failing test, implement code to pass, refactor
- C) Write all tests first
- D) Write all code first

**Q278.** A generator agent creates code. Should the same agent review it?
- A) Yes, for efficiency
- B) No, use fresh session to avoid bias
- C) Yes, with higher temperature
- D) Doesn't matter

**Q279.** You're implementing multi-pass review. Pass 1 analyzes each file. What should Pass 2 do?
- A) Re-analyze each file
- B) Fresh session analyzing cross-file integration
- C) Same as Pass 1
- D) Skip Pass 2

**Q280.** Why is same-session self-review problematic?
- A) Too slow
- B) Retains reasoning context, confirmation bias
- C) Too expensive
- D) Not problematic

**Q281.** You have API-specific rules and component-specific rules. How should you structure CLAUDE.md?
- A) All in one file
- B) @import .claude/rules/api.md and .claude/rules/components.md
- C) Separate projects
- D) Multiple CLAUDE.md files

**Q282.** Path-specific rules use YAML frontmatter. What does the `paths` field contain?
- A) File paths
- B) Glob patterns matching files
- C) Directory names
- D) File extensions

**Q283.** You want rules to apply only to `src/api/**/*.ts` files. Where do you specify this?
- A) In CLAUDE.md directly
- B) In YAML frontmatter with paths glob pattern
- C) In .gitignore
- D) In tsconfig.json

**Q284.** A skill is making too many tool calls. What's likely wrong?
- A) Normal behavior
- B) allowed-tools not restricted, or unclear skill description
- C) Need more tools
- D) Bug in Claude

**Q285.** You're creating a refactoring skill. Should it run in the main session or forked?
- A) Main session
- B) Forked session to avoid affecting main work
- C) Doesn't matter
- D) New agent

**Q286.** Your CI pipeline generates code that must follow a specific JSON schema. How do you enforce this?
- A) Manual validation
- B) --json-schema flag with schema file
- C) Hope for the best
- D) Post-processing

**Q287.** You're running Claude Code in CI for code generation. Should you use plan mode?
- A) No, use -p for direct execution
- B) Yes, always review
- C) Doesn't matter
- D) CI doesn't support plan mode

**Q288.** Batch API requests are processed within 24 hours. What if you need real-time results?
- A) Use Batch API with priority
- B) Use synchronous API calls instead
- C) Wait 24 hours
- D) Not possible

**Q289.** You're implementing session context isolation for generator vs reviewer. Why?
- A) Performance
- B) Prevent reviewer from inheriting generator's reasoning bias
- C) Cost savings
- D) Required by API

**Q290.** A skill needs to read files, edit them, and run tests. What tools should be in allowed-tools?
- A) All tools
- B) ["Read", "Edit", "Bash"]
- C) Just "Edit"
- D) No restriction

**Q291.** You have 10 topic-specific rule files. Where should they be stored?
- A) All in CLAUDE.md
- B) .claude/rules/ directory
- C) Separate repositories
- D) Database

**Q292.** Your CLAUDE.md imports 5 rule files. What's the benefit?
- A) Faster loading
- B) Organization and maintainability
- C) Lower costs
- D) Better performance

**Q293.** You're creating a command for simple formatting. Should it be a command or skill?
- A) Skill (complex workflow)
- B) Command (simple action)
- C) Neither
- D) Both

**Q294.** You're creating a multi-step code review workflow. Should it be a command or skill?
- A) Command (simple)
- B) Skill (complex workflow)
- C) Neither
- D) Separate agent

**Q295.** In CI/CD, you need to pass prior review findings to the current review. How?
- A) Automatic
- B) Include findings in prompt/context
- C) Database
- D) Not possible

---

## Advanced Level (30 Questions)

**Q296.** You're building a monorepo with 10 services, each needing different Claude Code rules. How should you architect the configuration?
- A) One global CLAUDE.md
- B) Directory-specific CLAUDE.md in each service + shared project CLAUDE.md
- C) 10 separate projects
- D) No configuration

**Q297.** Your CLAUDE.md has 50 lines of critical security rules and 150 lines of style preferences. What's the issue?
- A) Perfect balance
- B) Style preferences dilute critical security rules
- C) Need more rules
- D) Too short

**Q298.** You need to enforce that API endpoints always have rate limiting, but this is a critical security requirement. Where should this be?
- A) In CLAUDE.md as suggestion
- B) In CLAUDE.md as critical rule + programmatic validation
- C) Just in CLAUDE.md
- D) Documentation only

**Q299.** You're implementing path-specific rules for `src/api/**/*.ts`, `src/components/**/*.tsx`, and `src/utils/**/*.ts`. How should you organize this?
- A) All in main CLAUDE.md
- B) Three separate rule files in .claude/rules/ with path frontmatter
- C) Three separate projects
- D) One rule file

**Q300.** A skill needs to fork session, use specific tools, and accept file path arguments. What frontmatter is needed?
- A) Just context: fork
- B) context: fork, allowed-tools: [...], argument-hint: "path"
- C) Only allowed-tools
- D) No frontmatter needed

**Q301.** You're creating a skill that coordinates 3 sub-tasks. Should each sub-task be a separate skill or one complex skill?
- A) One complex skill
- B) Separate skills with coordinator skill
- C) Not possible
- D) Use commands

**Q302.** Your skill sometimes needs Read/Edit, sometimes needs Bash. How do you handle allowed-tools?
- A) Include all three
- B) Include all needed tools in allowed-tools
- C) Separate skills
- D) No restriction

**Q303.** You're implementing a CI/CD pipeline with 3 stages: generate, review, test. How should sessions be managed?
- A) One session for all
- B) Separate sessions: generate, fresh review session, test session
- C) Random
- D) No sessions

**Q304.** Your Batch API requests need to be processed in priority order. How do you handle this?
- A) Batch API doesn't support priority
- B) Use synchronous API for high-priority, batch for low-priority
- C) Submit in order
- D) Not possible

**Q305.** You're generating 1000 files with Claude in CI. Some fail validation. How should you handle partial failures?
- A) Fail entire job
- B) Track success/failure per file, retry failures
- C) Ignore failures
- D) Manual intervention

**Q306.** A multi-pass review finds 50 issues in Pass 1. Pass 2 should focus on what?
- A) Re-check same issues
- B) Cross-file integration and architectural issues
- C) Same as Pass 1
- D) Skip Pass 2

**Q307.** You're implementing iterative refinement with 5 iterations. Context is growing large. What should you do?
- A) Continue in same session
- B) Periodic context reset with summary
- C) Increase context limit
- D) Stop iterating

**Q308.** Your skill needs to handle errors gracefully and retry up to 3 times. Where should this logic be?
- A) In CLAUDE.md
- B) In skill implementation/description
- C) User handles it
- D) Not possible

**Q309.** You're building a code generation pipeline: spec → code → tests → docs. Should this be one skill or multiple?
- A) One mega-skill
- B) Four separate skills with orchestration
- C) Not possible
- D) One command

**Q310.** Your CI pipeline uses --json-schema for validation. The schema changes frequently. How do you manage this?
- A) Hardcode schema
- B) Version-controlled schema files
- C) No schema
- D) Manual updates

**Q311.** You need to enforce that all database queries use parameterized queries (SQL injection prevention). This is critical. How do you enforce?
- A) CLAUDE.md only
- B) CLAUDE.md + automated code analysis + CI validation
- C) Trust Claude
- D) Manual review

**Q312.** Your project has 3 teams with different coding standards. How should CLAUDE.md be structured?
- A) One standard for all
- B) Shared base + team-specific overrides via directory CLAUDE.md
- C) Separate projects
- D) No standards

**Q313.** A skill needs to access external APIs, databases, and file system. What tools are needed?
- A) Just Read/Write
- B) Custom MCP tools + built-in tools
- C) Built-in only
- D) Not possible

**Q314.** You're implementing a skill that must maintain state across multiple invocations. How?
- A) Skill remembers automatically
- B) External state storage (file, database)
- C) Not possible
- D) Global variables

**Q315.** Your Batch API job has 1000 requests. 100 fail. How do you handle retries?
- A) Resubmit entire batch
- B) Extract failed requests, retry separately
- C) Ignore failures
- D) Manual intervention

**Q316.** You're building a CI/CD pipeline that must generate code, review it, and only commit if review passes. How should you structure this?
- A) One step
- B) Generate (session 1) → Review (fresh session 2) → Conditional commit
- C) Manual review
- D) Trust generation

**Q317.** Your multi-pass review Pass 1 generates 200KB of findings. Pass 2 can't fit all in context. What do you do?
- A) Increase context
- B) Summarize Pass 1 findings, prioritize critical issues
- C) Skip Pass 2
- D) Reduce Pass 1

**Q318.** A skill needs to coordinate with external systems (Jira, GitHub, Slack). How should this be implemented?
- A) Built-in tools only
- B) Custom MCP servers for each integration
- C) Not possible
- D) Manual integration

**Q319.** You're implementing TDD iteration with Claude. Tests fail 50% of the time due to flaky tests. How do you handle this?
- A) Ignore flaky tests
- B) Retry logic + flaky test detection
- C) Disable tests
- D) Manual intervention

**Q320.** Your CI pipeline needs to generate code that passes linting, type checking, and tests. How should you structure the workflow?
- A) Generate and hope
- B) Generate → Validate → Fix issues → Re-validate loop
- C) Manual fixes
- D) Disable validation

**Q321.** You have 20 different skills. Users don't know which to use. How do you help?
- A) Documentation
- B) Clear naming + argument-hint + description
- C) Reduce to 5 skills
- D) Training

**Q322.** A skill needs different behavior in dev vs prod. How do you handle this?
- A) Two separate skills
- B) Environment variable in skill logic
- C) Not possible
- D) Manual switching

**Q323.** You're implementing a skill that must comply with SOC 2 requirements (audit logging, data retention). How?
- A) CLAUDE.md instructions
- B) Programmatic enforcement + audit logging in skill
- C) Trust Claude
- D) Manual compliance

**Q324.** Your Batch API usage is growing. You need to optimize costs. What strategies should you use?
- A) Use synchronous API
- B) Batch similar requests + prompt caching + efficient prompts
- C) Reduce usage
- D) No optimization needed

**Q325.** You're building a skill that must handle PII data. How do you ensure PII is never logged?
- A) CLAUDE.md instruction
- B) Programmatic PII detection + redaction in skill
- C) Trust Claude
- D) Manual review

---

**Next:** [Domain 4 Questions →](./Domain-4-Questions.md)
