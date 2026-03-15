# Domain 4: Prompt Engineering & Structured Output (100 Questions)

**Weight: 20% of exam**

This domain covers prompt engineering techniques, few-shot prompting, JSON schemas, validation-retry loops, and batch processing.

---

## Beginner Level (35 Questions)

**Q326.** What makes a prompt "explicit" rather than "vague"?
- A) Longer length
- B) Specific requirements and clear expectations
- C) Technical jargon
- D) Multiple questions

**Q327.** Which is a vague prompt?
- A) "Make it better"
- B) "Reduce API response time from 500ms to <200ms by adding indexes"
- C) "Fix the login bug where users can't reset passwords"
- D) "Add error handling for network timeouts"

**Q328.** What is few-shot prompting?
- A) Taking multiple attempts
- B) Providing examples to guide output format
- C) Using short prompts
- D) Rapid iteration

**Q329.** How many examples are typically optimal for few-shot prompting?
- A) 1
- B) 2-4
- C) 10+
- D) 100+

**Q330.** What does a JSON schema guarantee in tool use?
- A) Semantic correctness
- B) Structure matches schema (field names, types)
- C) Data accuracy
- D) Performance

**Q331.** What does a JSON schema NOT guarantee?
- A) Field types match
- B) Required fields present
- C) Semantic correctness of values
- D) Structure compliance

**Q332.** What is tool_choice: "auto"?
- A) Force tool use
- B) Let Claude decide whether to use tools
- C) Use all tools
- D) Disable tools

**Q333.** What is tool_choice: "any"?
- A) Random tool
- B) Force tool use, Claude picks which
- C) All tools
- D) No tools

**Q334.** What is tool_choice: {"type": "tool", "name": "extract"}?
- A) Suggest tool
- B) Force specific tool
- C) Optional tool
- D) Disable tool

**Q335.** When should you use tool_choice: "any"?
- A) Never
- B) When you need guaranteed structured output
- C) Always
- D) Random

**Q336.** What should "required" fields in a JSON schema contain?
- A) Optional data
- B) Mandatory fields that must be present
- C) Recommended fields
- D) All fields

**Q337.** What is an enum in JSON schema?
- A) Number type
- B) List of allowed values
- C) Boolean
- D) Array

**Q338.** Why include "other" in enum values?
- A) Not needed
- B) Flexibility for unexpected values
- C) Default value
- D) Error handling

**Q339.** What is a validation-retry loop?
- A) Infinite loop
- B) Validate output, retry with specific errors if invalid
- C) Performance optimization
- D) Error logging

**Q340.** How many retries are typically recommended in validation-retry loops?
- A) Unlimited
- B) 3-5 max
- C) 1
- D) 100

**Q341.** When are validation-retry loops ineffective?
- A) Always work
- B) Ambiguous requirements, missing source data
- C) Never ineffective
- D) With JSON schemas

**Q342.** What should you provide in retry prompts?
- A) Generic "try again"
- B) Specific validation errors and what to fix
- C) Same prompt
- D) Nothing

**Q343.** What is the detected_pattern field used for?
- A) Code patterns
- B) Tracking dismissal patterns in validation
- C) Design patterns
- D) Error patterns

**Q344.** What is batch processing in the context of Claude API?
- A) File batching
- B) Processing multiple requests together
- C) Database batching
- D) Code compilation

**Q345.** What cost savings does the Message Batches API provide?
- A) 25%
- B) 50%
- C) 75%
- D) 90%

**Q346.** What is the processing window for Batch API?
- A) Instant
- B) 24 hours
- C) 1 week
- D) 1 hour

**Q347.** When should you use synchronous API instead of batch?
- A) Always
- B) When you need immediate results
- C) Never
- D) For cost savings

**Q348.** What is self-review in the context of Claude?
- A) User reviews
- B) Agent reviews its own output in same session
- C) Peer review
- D) Automated testing

**Q349.** What is the main limitation of same-session self-review?
- A) Too slow
- B) Retains reasoning context, confirmation bias
- C) Too expensive
- D) Not accurate

**Q350.** What is multi-pass review?
- A) Multiple reviewers
- B) Multiple review passes, fresh session for each
- C) Reviewing multiple times
- D) Automated testing

**Q351.** Why use a fresh session for Pass 2 in multi-pass review?
- A) Faster
- B) Avoid confirmation bias from Pass 1
- C) Cheaper
- D) Required

**Q352.** What should temperature be set to for factual, consistent responses?
- A) 1.0
- B) 0.0-0.3 (low)
- C) 0.7-1.0 (high)
- D) 2.0

**Q353.** What should temperature be set to for creative, varied responses?
- A) 0.0
- B) 0.7-1.0 (high)
- C) 0.1
- D) -1.0

**Q354.** What is the purpose of system prompts?
- A) User messages
- B) Set agent behavior and context
- C) Error messages
- D) Logging

**Q355.** Can system prompts enforce critical business rules reliably?
- A) Yes, always
- B) No, use programmatic enforcement for critical rules
- C) Yes, with high temperature
- D) Yes, with examples

**Q356.** What is structured output?
- A) Formatted code
- B) Output conforming to defined schema (JSON, XML)
- C) Pretty printing
- D) Documentation

**Q357.** How do you guarantee structured output?
- A) Ask nicely
- B) Use tool_use with JSON schema
- C) High temperature
- D) Multiple retries

**Q358.** What is the benefit of explicit criteria in prompts?
- A) Longer prompts
- B) Reduces false positives, clearer expectations
- C) More creative
- D) Faster

**Q359.** What happens when you provide vague instructions?
- A) Better results
- B) Unpredictable, inconsistent results
- C) Faster execution
- D) Lower costs

**Q360.** What is prompt chaining?
- A) Blockchain
- B) Sequence of prompts building on each other
- C) Multiple agents
- D) Error handling

---

## Intermediate Level (35 Questions)

**Q361.** You need Claude to extract invoice data with consistent format. Which approach is best?
- A) Ask in natural language
- B) Few-shot prompting with 2-4 examples
- C) One example
- D) No examples

**Q362.** Your few-shot examples should cover what?
- A) Only happy path
- B) Edge cases and format variations
- C) Just one scenario
- D) Errors only

**Q363.** You're designing a JSON schema for invoice extraction. Should all fields be required?
- A) Yes, all required
- B) No, only truly mandatory fields
- C) No required fields
- D) Random

**Q364.** A field might have unexpected values. How should you handle this in schema?
- A) Strict enum only
- B) Enum with "other" option
- C) No validation
- D) String only

**Q365.** Your validation-retry loop has run 5 times without success. What should you do?
- A) Continue retrying
- B) Escalate or return error after max retries
- C) Retry 100 times
- D) Give up immediately

**Q366.** In a retry prompt, you should provide:
- A) Same original prompt
- B) Specific validation errors with guidance
- C) Generic "failed"
- D) Nothing

**Q367.** You're extracting data from 1000 documents. What's the most cost-effective approach?
- A) 1000 synchronous calls
- B) Message Batches API (50% savings)
- C) Sequential processing
- D) Manual extraction

**Q368.** Batch API has 24-hour processing window. When should you NOT use it?
- A) Always use it
- B) When you need results within minutes
- C) For cost savings
- D) For large volumes

**Q369.** You're implementing self-review. The agent reviews its own code in the same session. What's the issue?
- A) Too slow
- B) Confirmation bias, retains reasoning context
- C) Too expensive
- D) No issue

**Q370.** How do you avoid confirmation bias in code review?
- A) Higher temperature
- B) Fresh session for review
- C) More examples
- D) Longer prompts

**Q371.** Multi-pass review: Pass 1 checks each file. What should Pass 2 focus on?
- A) Same as Pass 1
- B) Cross-file integration in fresh session
- C) Skip Pass 2
- D) Documentation

**Q372.** You need consistent, factual responses. What temperature should you use?
- A) 1.0
- B) 0.0-0.2
- C) 0.8
- D) Random

**Q373.** You need creative brainstorming with variety. What temperature should you use?
- A) 0.0
- B) 0.7-1.0
- C) 0.1
- D) 2.0

**Q374.** You want to enforce that all API responses include error codes. Where should this be?
- A) System prompt only
- B) JSON schema + validation
- C) Hope for best
- D) Manual check

**Q375.** A prompt says "improve the code". What's wrong?
- A) Perfect
- B) Too vague, needs specific criteria
- C) Too long
- D) Too technical

**Q376.** Better version: "Improve code by: reducing time complexity from O(n²) to O(n log n), adding error handling, adding unit tests". What's better about this?
- A) Longer
- B) Explicit criteria and measurable outcomes
- C) More words
- D) Technical terms

**Q377.** You're using tool_use for structured output. The structure is correct but values are wrong. What's the issue?
- A) Schema is wrong
- B) Schema guarantees structure, not semantic correctness
- C) Need more examples
- D) Bug in Claude

**Q378.** How do you improve semantic correctness in structured output?
- A) Better schema
- B) Validation-retry with specific error feedback
- C) Higher temperature
- D) More retries

**Q379.** You need to extract data in a specific format. What's the best approach?
- A) Natural language description
- B) Few-shot examples + JSON schema + validation
- C) Hope for best
- D) Manual extraction

**Q380.** Your schema has 20 optional fields. What's the issue?
- A) Perfect
- B) Too permissive, consider which are truly optional
- C) Need more fields
- D) All should be required

**Q381.** A field can be null or have a value. How do you represent this in schema?
- A) Don't include field
- B) Use nullable: true
- C) Make it required
- D) Use string

**Q382.** You're chaining 5 prompts together. Context is growing large. What should you do?
- A) Continue
- B) Summarize between prompts
- C) Increase limit
- D) Start over

**Q383.** Prompt chaining vs single complex prompt. When should you use chaining?
- A) Always
- B) When task has clear sequential steps
- C) Never
- D) Random

**Q384.** You need Claude to follow a strict 10-step process. What's the best approach?
- A) One prompt with all steps
- B) Prompt chaining with explicit steps
- C) Hope Claude figures it out
- D) Multiple agents

**Q385.** Your batch job has 1000 requests. 100 fail validation. How do you handle this?
- A) Resubmit all 1000
- B) Extract failed, retry with error feedback
- C) Ignore failures
- D) Manual fixes

**Q386.** You're using custom_id in Batch API. What's it for?
- A) Authentication
- B) Tracking which response belongs to which request
- C) Priority
- D) Versioning

**Q387.** A validation-retry loop keeps failing on the same issue. What should you do?
- A) Retry more
- B) Check if requirements are ambiguous or data is missing
- C) Increase retries to 100
- D) Give up

**Q388.** You're extracting structured data. Schema compliance is 100% but accuracy is 60%. What's the issue?
- A) Schema is wrong
- B) Schema ensures structure, not semantic correctness
- C) Need more retries
- D) Bug

**Q389.** How do you improve accuracy in structured extraction?
- A) Better schema
- B) Few-shot examples + validation-retry with specific feedback
- C) More fields
- D) Higher temperature

**Q390.** You need to extract data from invoices with varying formats. What's the best approach?
- A) One example
- B) Few-shot with examples covering format variations
- C) No examples
- D) Hope for best

**Q391.** Your JSON schema uses enum: ["USD", "EUR", "GBP"]. A document has "CAD". What happens?
- A) Validation passes
- B) Validation fails
- C) Converts to USD
- D) Ignores field

**Q392.** Better schema: enum: ["USD", "EUR", "GBP", "other"]. Why is this better?
- A) More options
- B) Handles unexpected values gracefully
- C) Longer
- D) No difference

**Q393.** You're implementing multi-pass review. How should you pass Pass 1 findings to Pass 2?
- A) Automatic
- B) Include findings in Pass 2 prompt
- C) Database
- D) Don't pass

**Q394.** Pass 2 review finds issues that Pass 1 missed. Why?
- A) Pass 1 was bad
- B) Fresh session provides new perspective
- C) Random
- D) Bug

**Q395.** You need guaranteed JSON output. What's the most reliable approach?
- A) Ask nicely
- B) tool_use with JSON schema and tool_choice: "any"
- C) High temperature
- D) Multiple retries

---

## Advanced Level (30 Questions)

**Q396.** You're building a data extraction pipeline processing 10,000 documents/day with varying formats. How should you architect prompting strategy?
- A) One generic prompt
- B) Document type classification + type-specific few-shot prompts + validation
- C) Manual processing
- D) Random

**Q397.** Your few-shot examples work for 80% of cases but fail on edge cases. How do you improve?
- A) More examples
- B) Add edge case examples + validation-retry for outliers
- C) Give up on edge cases
- D) Manual handling

**Q398.** You're designing a schema for complex nested data (invoices with line items, taxes, discounts). How should you structure it?
- A) Flat structure
- B) Nested objects with clear hierarchy
- C) Multiple schemas
- D) No structure

**Q399.** Your validation-retry loop has 3 retries. 20% of requests still fail. What should you do?
- A) Increase retries to 10
- B) Analyze failure patterns, improve prompts/schema, escalate persistent failures
- C) Ignore failures
- D) Manual intervention

**Q400.** You're using Batch API for 10,000 requests. You need to track success rate per document type. How?
- A) Manual tracking
- B) custom_id with document type encoding + post-processing analysis
- C) Not possible
- D) Database

**Q401.** Multi-pass review: Pass 1 generates 500 findings. Pass 2 can't fit all in context. What do you do?
- A) Increase context
- B) Prioritize critical findings, summarize rest
- C) Skip Pass 2
- D) Reduce Pass 1

**Q402.** You're implementing prompt chaining with 10 steps. Step 5 fails. How should you handle this?
- A) Start over
- B) Error handling with retry or alternative path
- C) Skip step 5
- D) Manual intervention

**Q403.** Your schema has additionalProperties: false. A document has an unexpected field. What happens?
- A) Field is included
- B) Validation fails
- C) Field is ignored
- D) Error

**Q404.** When should you use additionalProperties: false?
- A) Always
- B) When you need strict validation
- C) Never
- D) Random

**Q405.** You're extracting data with confidence scores. Should you use self-reported confidence for decision-making?
- A) Yes, reliable
- B) No, self-reported confidence is unreliable
- C) Yes, with validation
- D) Sometimes

**Q406.** How do you measure actual accuracy instead of self-reported confidence?
- A) Trust confidence scores
- B) Validation against ground truth + stratified sampling
- C) Ask Claude
- D) Not possible

**Q407.** You're building a prompt that must work across 5 languages. How should you structure it?
- A) English only
- B) Language-agnostic examples + explicit language handling
- C) 5 separate prompts
- D) Hope for best

**Q408.** Your few-shot examples are 1000 tokens each. You need 4 examples. What's the issue?
- A) Perfect
- B) Too large, consuming context; use concise examples
- C) Need more examples
- D) Too small

**Q409.** You're implementing validation-retry with external API validation (slow, 5 seconds). How do you optimize?
- A) Synchronous validation
- B) Async validation + timeout + fallback
- C) Skip validation
- D) Manual validation

**Q410.** Your Batch API job processes 10,000 requests. You need to reprocess failures. How do you identify them?
- A) Manual review
- B) Parse batch results, extract failures by custom_id
- C) Reprocess all
- D) Not possible

**Q411.** You're using temperature=0 for consistency but getting varied outputs. What's likely wrong?
- A) Temperature is broken
- B) Prompt is ambiguous, allowing multiple valid interpretations
- C) Need higher temperature
- D) Bug

**Q412.** How do you achieve true consistency with ambiguous requirements?
- A) Lower temperature
- B) Make requirements explicit and unambiguous
- C) More examples
- D) Not possible

**Q413.** You're chaining prompts with state that must persist. How do you maintain state?
- A) Claude remembers
- B) Explicit state passing in prompts or external storage
- C) Global variables
- D) Not possible

**Q414.** Your schema validation passes but business logic validation fails (e.g., line items don't sum to total). Where should business logic validation be?
- A) In schema
- B) Separate validation layer after schema validation
- C) Not needed
- D) Manual

**Q415.** You're implementing a validation-retry loop with 3 validators (schema, business logic, external API). How should you structure this?
- A) Sequential validation
- B) Layered validation with specific error feedback per layer
- C) Random order
- D) One validator

**Q416.** Your few-shot examples have inconsistent formatting. What's the issue?
- A) No issue
- B) Inconsistency confuses model, use consistent format
- C) Good variety
- D) Need more examples

**Q417.** You're using tool_choice: "any" but sometimes don't get tool use. What's wrong?
- A) Bug
- B) Check if tools are actually available and relevant
- C) Use "auto"
- D) Increase temperature

**Q418.** Your batch processing has variable latency (1-24 hours). How do you handle time-sensitive requests?
- A) All in batch
- B) Synchronous for time-sensitive, batch for latency-tolerant
- C) Wait
- D) Not possible

**Q419.** You're implementing multi-pass review with 3 passes. When should you stop adding passes?
- A) Always 3
- B) When additional passes show diminishing returns
- C) Never stop
- D) Random

**Q420.** Your schema has deeply nested objects (5+ levels). What's the issue?
- A) Perfect
- B) Complexity increases errors, consider flattening
- C) Need more nesting
- D) No issue

**Q421.** You're using few-shot prompting with examples from different domains. What's the issue?
- A) Good variety
- B) Domain mismatch confuses model, use relevant examples
- C) Perfect
- D) Need more domains

**Q422.** Your validation-retry loop retries with the same prompt each time. What's wrong?
- A) Correct approach
- B) Should provide specific error feedback in retry
- C) Need more retries
- D) No issue

**Q423.** You're extracting data with required fields. 30% of documents don't have all required fields. How do you handle this?
- A) Fail all
- B) Partial extraction with missing field indicators
- C) Make all optional
- D) Manual extraction

**Q424.** Your Batch API usage is growing. You need to optimize costs beyond the 50% savings. What else can you do?
- A) Nothing more
- B) Prompt caching + efficient prompts + deduplication
- C) Reduce usage
- D) Switch to sync

**Q425.** You're implementing prompt chaining with conditional branches (if X then Y else Z). How do you handle this?
- A) Linear chain only
- B) Dynamic chaining with conditional logic
- C) Not possible
- D) Multiple chains

---

**Next:** [Domain 5 Questions →](./Domain-5-Questions.md)
