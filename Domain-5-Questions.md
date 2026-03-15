# Domain 5: Context Management & Reliability (75 Questions)

**Weight: 15% of exam**

This domain covers context management, escalation patterns, error propagation, provenance tracking, and reliability strategies.

---

## Beginner Level (25 Questions)

**Q426.** What is progressive summarization?
- A) Improving summaries
- B) Compressing context through repeated summarization ✓
- C) Progress tracking
- D) Summary generation

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q427.** What is the main risk of progressive summarization?
- A) Too slow
- B) Information loss through compression ✓
- C) Too expensive
- D) Too fast

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q428.** What is the "lost in the middle" effect?
- A) Getting lost
- B) Middle content in long context gets deprioritized ✓
- C) Missing files
- D) Errors

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q429.** What is a "case facts" block?
- A) Legal documents
- B) Structured, persistent key information ✓
- C) Test cases
- D) Documentation

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q430.** Why trim verbose tool outputs?
- A) Save money
- B) Reduce context bloat, keep only relevant info ✓
- C) Faster execution
- D) Better formatting

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q431.** What is position-aware ordering?
- A) Sorting alphabetically
- B) Placing important info at start/end of context ✓
- C) Random order
- D) Chronological order

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q432.** When should you escalate to a human?
- A) Always
- B) Policy gaps, high-value transactions, legal concerns ✓
- C) Never
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q433.** Should sentiment analysis be used for escalation decisions?
- A) Yes, always
- B) No, sentiment ≠ complexity ✓
- C) Yes, it's reliable
- D) Sometimes

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q434.** Should self-reported confidence scores be used for escalation?
- A) Yes, reliable
- B) No, unreliable for decisions ✓
- C) Yes, always
- D) Sometimes

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q435.** What should trigger escalation?
- A) Customer sentiment
- B) Explicit business rules (policy gaps, value thresholds, legal) ✓
- C) Agent confidence
- D) Response length

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q436.** What is structured error propagation?
- A) Error codes
- B) Errors with full context and categorization ✓
- C) Generic errors
- D) Silent errors

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q437.** What should an error response include?
- A) Just "error"
- B) Category, context, isRetryable, suggested action ✓
- C) Stack trace only
- D) Nothing

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q438.** What is the difference between access failure and empty results?
- A) No difference
- B) Access failure is error, empty results is valid (no data found) ✓
- C) Both are errors
- D) Both are valid

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q439.** When should an agent attempt local recovery?
- A) Never
- B) Before escalating to coordinator ✓
- C) After escalation
- D) Always escalate immediately

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q440.** What should be included in partial results?
- A) Only successes
- B) Completed tasks + failed tasks with error context ✓
- C) Only failures
- D) Nothing

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q441.** What is context degradation?
- A) Code quality decline
- B) Loss of focus/accuracy in extended sessions ✓
- C) Performance issues
- D) Bugs

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q442.** What is a scratchpad file used for?
- A) Notes
- B) Persistent state outside context window ✓
- C) Temporary data
- D) Logs

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q443.** What does /compact do?
- A) Compresses files
- B) Summarizes and reduces context ✓
- C) Optimizes code
- D) Deletes data

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q444.** When should you use subagent delegation for verbose exploration?
- A) Never
- B) When exploration generates too much context ✓
- C) Always
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q445.** What is a crash recovery manifest?
- A) Error log
- B) Document of completed steps and current state ✓
- C) Backup file
- D) Configuration

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q446.** What is information provenance?
- A) Source location
- B) Tracking where information came from ✓
- C) Data validation
- D) Error handling

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q447.** What is a claim-source mapping?
- A) Legal claims
- B) Linking claims to their sources ✓
- C) Data mapping
- D) API mapping

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q448.** Why include temporal context in provenance?
- A) Timestamps
- B) Data may be outdated ✓
- C) Performance
- D) Logging

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q449.** What is conflict annotation?
- A) Code conflicts
- B) Noting when sources disagree ✓
- C) Git conflicts
- D) Errors

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q450.** In synthesis output, how should you handle contested claims?
- A) Ignore them
- B) Distinguish well-established vs contested ✓
- C) Choose one
- D) Remove them

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

## Intermediate Level (25 Questions)

**Q451.** Your agent session has accumulated 100,000 tokens and responses are becoming repetitive. What should you do?
- A) Continue
- B) Use /compact or start fresh session with summary ✓
- C) Increase context limit
- D) Add more tools

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q452.** You're using progressive summarization after every 10 messages. After 5 iterations, accuracy drops. Why?
- A) Bug
- B) Cumulative information loss through repeated compression ✓
- C) Need more iterations
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q453.** Where should critical information be placed in long context?
- A) Middle
- B) Start or end (avoid "lost in the middle") ✓
- C) Random
- D) Doesn't matter

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q454.** A customer is angry (high negative sentiment) but has a simple password reset request. Should you escalate?
- A) Yes, based on sentiment
- B) No, sentiment ≠ complexity, simple issue ✓
- C) Yes, always escalate anger
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q455.** An agent reports 95% confidence but the task is outside policy guidelines. Should you escalate?
- A) No, high confidence
- B) Yes, policy gap requires escalation regardless of confidence ✓
- C) Trust confidence
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q456.** A refund request is $450 (under $500 limit) but customer is threatening legal action. Should you escalate?
- A) No, under limit
- B) Yes, legal concern triggers escalation ✓
- C) Process normally
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q457.** A tool returns an empty array when searching for customer. How should this be communicated?
- A) As error
- B) As valid result (no customer found), not error ✓
- C) Retry
- D) Escalate

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q458.** A database connection fails. How should the error be structured?
- A) "Error"
- B) {isError: true, errorCategory: "database_connection_failed", isRetryable: true, context: {...}} ✓
- C) Silent failure
- D) Generic message

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q459.** A subagent completes 3 of 5 tasks. How should results be returned?
- A) Only completed
- B) Partial results with completed array + failed array with context ✓
- C) Only failures
- D) Error

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q460.** Before escalating to coordinator, what should a subagent try?
- A) Immediate escalation
- B) Local recovery attempts ✓
- C) Give up
- D) Retry indefinitely

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q461.** Your agent explores a large codebase, generating 200KB of notes. What should you do?
- A) Keep all in context
- B) Use scratchpad file or delegate to subagent ✓
- C) Summarize everything
- D) Delete notes

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q462.** An agent session crashes after 50 operations. How do you recover?
- A) Start from scratch
- B) Use crash recovery manifest with completed steps ✓
- C) Give up
- D) Manual recovery

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q463.** You're synthesizing research from 10 sources. 2 sources contradict each other. How should you handle this?
- A) Choose one
- B) Annotate conflict, present both perspectives ✓
- C) Ignore contradiction
- D) Average them

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q464.** A claim is from a 2020 study. Current year is 2024. How should you present this?
- A) As current
- B) Include temporal context (data from 2020, may be outdated) ✓
- C) Ignore date
- D) Remove claim

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q465.** You're tracking provenance for 100 claims. What's the minimum information needed per claim?
- A) Just the claim
- B) Claim + source + date + confidence level ✓
- C) Just source
- D) Nothing

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q466.** Human review of extracted data shows 95% accuracy overall but 60% for invoices. What's the issue?
- A) Overall accuracy is good
- B) Aggregate metrics mask per-document-type failures ✓
- C) Invoices don't matter
- D) No issue

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q467.** How should you measure accuracy by document type?
- A) Overall only
- B) Stratified sampling and per-type metrics ✓
- C) Random sampling
- D) Manual review

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q468.** A subagent returns "Operation failed" with no context. What's wrong?
- A) Sufficient
- B) Generic error hides context, should include what was attempted ✓
- C) Perfect
- D) Too detailed

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q469.** You're implementing escalation logic. What should NOT trigger escalation?
- A) Policy gaps
- B) Customer sentiment ✓
- C) High-value transactions
- D) Legal concerns

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q470.** An agent needs to maintain state across 50 operations. Context window is limited. What's the solution?
- A) Increase context
- B) Scratchpad file with periodic checkpoints ✓
- C) Memory
- D) Database only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q471.** You're using case facts blocks. What should they contain?
- A) Everything
- B) Critical, persistent information (customer ID, issue, key dates) ✓
- C) Random data
- D) Tool outputs

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q472.** Verbose tool output returns 50KB of data. What should you do before passing to next step?
- A) Pass all
- B) Trim to relevant information only ✓
- C) Delete all
- D) Summarize to 1 sentence

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q473.** You're implementing conflict resolution for contradictory sources. What's the best approach?
- A) Choose first source
- B) Present both with conflict annotation and context ✓
- C) Average
- D) Ignore conflict

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q474.** A synthesis must distinguish between well-established facts and contested claims. How?
- A) Treat all equally
- B) Explicit labeling with source characterization ✓
- C) Only include established
- D) Only include contested

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q475.** You're tracking information provenance. A claim appears in 5 sources. How should you represent this?
- A) List one source
- B) List all sources with dates and confidence ✓
- C) Count only
- D) Ignore duplicates

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

## Advanced Level (25 Questions)

**Q476.** You're building a system that must maintain context across 1000 sequential operations. Context window is 200K tokens. How should you architect this?
- A) Increase context window
- B) Hierarchical context management with checkpoints and scratchpad ✓
- C) Summarize everything
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q477.** Your progressive summarization strategy loses critical details after 3 iterations. How do you fix this?
- A) More iterations
- B) Case facts blocks for critical info + selective summarization ✓
- C) Stop summarizing
- D) Increase context

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q478.** You're implementing escalation for a multi-tier support system (L1 → L2 → L3). What determines escalation tier?
- A) Random
- B) Explicit business rules (complexity, value, expertise required) ✓
- C) Sentiment
- D) Confidence

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q479.** An agent must distinguish between "customer is frustrated but issue is simple" vs "customer is calm but issue is complex". What should drive escalation?
- A) Frustration level
- B) Issue complexity and business rules ✓
- C) Tone
- D) Response length

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q480.** You're building an error propagation system across 5 layers of agents. How do you prevent error context loss?
- A) Generic errors
- B) Structured errors with full context chain at each layer ✓
- C) Only final error
- D) Silent propagation

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q481.** A subagent fails with "database timeout". The coordinator should:
- A) Retry immediately
- B) Structured error with context, retry with backoff, or alternative approach ✓
- C) Give up
- D) Ignore

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q482.** You're implementing partial results for a coordinator with 10 subagents. 3 succeed, 5 fail, 2 timeout. How should results be structured?
- A) Only successes
- B) {completed: [...], failed: [...], timeout: [...], errors: {...}} ✓
- C) Only failures
- D) Error

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q483.** Your system must support crash recovery for multi-hour operations. What's the minimum state to save?
- A) Nothing
- B) Completed steps, current position, pending work, critical context ✓
- C) Everything
- D) Final results only

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q484.** Context degradation is occurring after 2 hours. You can't reduce session length. What strategies can you use?
- A) Accept degradation
- B) Periodic /compact + scratchpad + subagent delegation ✓
- C) Increase context
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q485.** You're synthesizing research from 50 sources with 20 conflicting claims. How should you structure the output?
- A) Choose majority
- B) Conflict graph with source characterization and evidence strength ✓
- C) Ignore conflicts
- D) List all

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q486.** Information provenance must be maintained for audit compliance. What's the minimum tracking required?
- A) Source only
- B) Source + timestamp + access method + user + confidence ✓
- C) Nothing
- D) Source + timestamp

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q487.** You're implementing stratified sampling for human review. You have 10 document types with varying accuracy. How do you sample?
- A) Random sampling
- B) Proportional sampling per type + oversample low-accuracy types ✓
- C) Equal sampling
- D) No sampling

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q488.** Field-level confidence tracking shows 95% accuracy for "name" but 60% for "total". What should you do?
- A) Overall is good
- B) Focus improvement on low-confidence fields ✓
- C) Ignore field-level
- D) Manual review all

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q489.** You're tracking accuracy by document type. Invoices: 60%, Receipts: 95%, Contracts: 80%. How should you optimize?
- A) Treat all equally
- B) Type-specific prompts and validation for low-accuracy types ✓
- C) Ignore invoices
- D) Manual processing

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q490.** A synthesis must preserve source characterizations (e.g., "preliminary study" vs "peer-reviewed meta-analysis"). Why?
- A) Not important
- B) Source credibility affects claim strength ✓
- C) Formatting
- D) Word count

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q491.** You're implementing temporal context tracking. A claim from 2020 contradicts one from 2024. How should you handle this?
- A) Use latest only
- B) Present both with temporal context and note evolution ✓
- C) Use oldest
- D) Ignore dates

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q492.** Your error propagation includes isRetryable flags. A database connection failure occurs. What should isRetryable be?
- A) false
- B) true (transient network issue) ✓
- C) null
- D) undefined

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q493.** An authentication failure occurs. What should isRetryable be?
- A) true
- B) false (credentials invalid, retry won't help) ✓
- C) null
- D) undefined

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q494.** You're implementing local recovery before escalation. A tool fails with rate limit. What should the agent do?
- A) Escalate immediately
- B) Wait/backoff and retry locally before escalating ✓
- C) Give up
- D) Ignore

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q495.** A subagent attempts local recovery 3 times without success. What should it do?
- A) Retry 100 times
- B) Escalate to coordinator with full context of attempts ✓
- C) Give up silently
- D) Random

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q496.** You're building a system that must maintain ACID properties across distributed operations. How do you ensure atomicity with partial failures?
- A) Hope for best
- B) Transaction coordinator with rollback capability ✓
- C) Ignore failures
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q497.** Context window is 200K tokens. Your operation generates 500K tokens of intermediate data. How do you handle this?
- A) Increase limit
- B) Hierarchical processing with summarization and scratchpad ✓
- C) Reduce scope
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q498.** You're implementing provenance tracking for a system that synthesizes from 1000 sources. How do you make this scalable?
- A) Track all details
- B) Hierarchical provenance with source grouping ✓
- C) Don't track
- D) Manual tracking

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q499.** Your escalation system has 5 tiers. An issue meets criteria for tier 3 but customer demands tier 5. What should you do?
- A) Grant tier 5
- B) Follow business rules (tier 3), explain to customer ✓
- C) Random tier
- D) Reject request

**Answer: B** | **Explanation:** See study guide for detailed explanation.

**Q500.** You're implementing a reliability system with SLA requirements (99.9% uptime, <5s response). How do you handle context management under these constraints?
- A) Ignore constraints
- B) Aggressive context management + fallbacks + circuit breakers ✓
- C) Reduce functionality
- D) Not possible

**Answer: B** | **Explanation:** See study guide for detailed explanation.

---

**Congratulations!** You've completed all 500 practice questions!

**Next:** [Answer Key with Explanations →](./Answer-Key.md)
