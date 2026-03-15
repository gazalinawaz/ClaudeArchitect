# Domain 4: Prompt Engineering & Structured Output - Concept Guide

**Study this guide BEFORE attempting Domain 4 questions**

This guide explains prompt engineering techniques, few-shot prompting, structured output, and validation strategies.

---

## 📚 Table of Contents

1. [Explicit vs Vague Prompts](#explicit-vs-vague-prompts)
2. [Few-Shot Prompting](#few-shot-prompting)
3. [Structured Output with JSON Schema](#structured-output-with-json-schema)
4. [Validation-Retry Loops](#validation-retry-loops)
5. [Temperature Settings](#temperature-settings)
6. [Prompt Chaining](#prompt-chaining)
7. [Multi-Pass Review](#multi-pass-review)

---

## 1. Explicit vs Vague Prompts

### The Difference

**Vague prompts** → Unpredictable results
**Explicit prompts** → Consistent, accurate results

### Examples

#### ❌ Vague Prompts

```
"Make it better"
"Improve the code"
"Fix the issues"
"Optimize this"
```

**Problem:** Claude doesn't know WHAT to improve or HOW.

#### ✓ Explicit Prompts

```
"Reduce API response time from 500ms to <200ms by:
1. Adding database indexes on user_id and created_at
2. Implementing Redis caching for frequently accessed data
3. Using connection pooling"
```

```
"Fix the login bug where users with special characters in passwords 
cannot authenticate. The issue is in auth.js line 45 where password 
is not being properly escaped before SQL query."
```

**Why better:** Specific requirements, clear success criteria, actionable details.

### Making Prompts Explicit

**Template:**
```
[Action] by [Method] to achieve [Outcome]

Constraints:
- [Constraint 1]
- [Constraint 2]

Success criteria:
- [Criterion 1]
- [Criterion 2]
```

**Example:**
```
Refactor the user authentication system by extracting auth logic 
into a separate AuthService class to improve testability.

Constraints:
- Maintain backward compatibility with existing API
- No changes to database schema
- Keep existing error messages

Success criteria:
- All existing tests pass
- New AuthService has 100% test coverage
- Authentication flow unchanged from user perspective
```

---

## 2. Few-Shot Prompting

### What is Few-Shot Prompting?

Providing **examples** to guide Claude's output format.

### Why Use It?

Shows Claude EXACTLY what you want instead of describing it.

### How Many Examples?

**Optimal: 2-4 examples**

- 1 example: Might be seen as unique case
- 2-4 examples: Shows pattern clearly
- 10+ examples: Diminishing returns, wastes tokens

### Example: Invoice Data Extraction

**Without few-shot (vague):**
```
"Extract invoice data from this document"
```

**With few-shot (explicit):**
```
Extract invoice data in this format:

Example 1:
Input: "Invoice #1234, Date: 2024-01-15, Total: $500.00"
Output: {"invoice_id": "1234", "date": "2024-01-15", "total": 500.00}

Example 2:
Input: "INV-5678 | 2024-02-20 | Amount: $1,250.50"
Output: {"invoice_id": "5678", "date": "2024-02-20", "total": 1250.50}

Example 3:
Input: "Invoice Number: 9012, Dated 03/15/2024, Total Due: $75.00"
Output: {"invoice_id": "9012", "date": "2024-03-15", "total": 75.00}

Now extract from: [your document]
```

### Cover Edge Cases

Include examples for:
- Normal cases
- Edge cases
- Different formats
- Missing data

```
Example 1: Standard format
Input: "Invoice #1234, Total: $500"
Output: {"invoice_id": "1234", "total": 500.00}

Example 2: Missing invoice number
Input: "Total: $500"
Output: {"invoice_id": null, "total": 500.00}

Example 3: Different currency
Input: "Invoice #5678, Total: €300"
Output: {"invoice_id": "5678", "total": 300.00, "currency": "EUR"}
```

---

## 3. Structured Output with JSON Schema

### What is a JSON Schema?

A **blueprint** that defines the structure of JSON output.

### What Schema Guarantees

✓ **Structure** matches (field names, types)
✓ **Required fields** are present
✓ **Data types** are correct

❌ **Does NOT guarantee:**
- Semantic correctness (values might be wrong)
- Data accuracy
- Business logic validity

### Example Schema

```json
{
  "type": "object",
  "required": ["invoice_id", "date", "total"],
  "properties": {
    "invoice_id": {
      "type": "string",
      "description": "Invoice identifier"
    },
    "date": {
      "type": "string",
      "format": "date",
      "description": "Invoice date in YYYY-MM-DD format"
    },
    "total": {
      "type": "number",
      "minimum": 0,
      "description": "Total amount"
    },
    "currency": {
      "type": "string",
      "enum": ["USD", "EUR", "GBP", "other"],
      "default": "USD"
    }
  }
}
```

### Using tool_choice for Guaranteed Output

```python
response = claude.message(
    prompt="Extract invoice data from this document",
    tools=[{
        "name": "extract_invoice",
        "input_schema": invoice_schema
    }],
    tool_choice="any"  # Forces tool use = guaranteed structure
)
```

### Enum with "other"

**Why include "other"?**

```json
{
  "currency": {
    "type": "string",
    "enum": ["USD", "EUR", "GBP", "other"]
  }
}
```

**Without "other":**
- Document has "CAD" → Validation fails

**With "other":**
- Document has "CAD" → Returns "other" (graceful handling)

---

## 4. Validation-Retry Loops

### What is a Validation-Retry Loop?

1. Get output from Claude
2. Validate output
3. If invalid → Retry with specific error feedback
4. Repeat until valid or max retries

### Basic Pattern

```python
max_retries = 3
for attempt in range(max_retries):
    response = claude.extract_data(document)
    
    errors = validate(response)
    
    if not errors:
        return response  # Success!
    
    # Retry with specific errors
    prompt = f"Previous attempt had errors: {errors}. Please fix and retry."
```

### Specific Error Feedback

❌ **BAD: Generic retry**
```
"That was wrong, try again"
```

✓ **GOOD: Specific errors**
```
"Validation errors:
1. Field 'date' is '15-01-2024' but must be 'YYYY-MM-DD' format (2024-01-15)
2. Field 'total' is missing but is required
3. Field 'invoice_id' is '12-34' but should be numeric only (1234)

Please correct these specific issues and retry."
```

### When Validation-Retry Fails

**Ineffective when:**
- Requirements are ambiguous
- Source data is missing
- Task is impossible

**Example:**
```
Prompt: "Extract the customer's favorite color from this invoice"
Document: [Standard invoice with no color information]

→ No amount of retries will work (data doesn't exist)
```

### Max Retries

**Recommended: 3-5 retries**

```python
max_retries = 3

if attempts >= max_retries:
    return {
        "success": false,
        "error": "Max retries exceeded",
        "last_errors": errors,
        "suggestion": "Review requirements or source data"
    }
```

---

## 5. Temperature Settings

### What is Temperature?

Controls randomness/creativity in responses.

**Range:** 0.0 to 1.0

### Temperature Guide

| Temperature | Use Case | Example |
|-------------|----------|---------|
| **0.0 - 0.2** | Factual, consistent | Data extraction, code generation |
| **0.3 - 0.5** | Balanced | General tasks, documentation |
| **0.6 - 0.8** | Creative | Brainstorming, content writing |
| **0.9 - 1.0** | Very creative | Creative writing, varied ideas |

### Examples

**Temperature 0.0 (Consistent):**
```
Prompt: "What is 2+2?"
Response: "4"
Response: "4"
Response: "4"
(Always the same)
```

**Temperature 1.0 (Creative):**
```
Prompt: "Describe a sunset"
Response 1: "The sky blazed with orange and pink hues..."
Response 2: "Golden light painted the horizon..."
Response 3: "Crimson waves washed across the evening sky..."
(Different each time)
```

### When to Use Each

**Low Temperature (0.0-0.2):**
- Data extraction
- Code generation
- Factual Q&A
- Structured output
- Consistency critical

**High Temperature (0.7-1.0):**
- Brainstorming
- Creative writing
- Generating alternatives
- Variety desired

---

## 6. Prompt Chaining

### What is Prompt Chaining?

Sequence of prompts where each builds on previous results.

### Basic Pattern

```
Prompt 1: "Analyze this codebase structure"
→ Result 1: Structure analysis

Prompt 2: "Based on this structure [Result 1], identify security issues"
→ Result 2: Security issues

Prompt 3: "Based on these issues [Result 2], create remediation plan"
→ Result 3: Remediation plan
```

### When to Use Chaining

✓ **Good for:**
- Clear sequential steps
- Each step builds on previous
- Intermediate results needed
- Complex workflows

❌ **Not good for:**
- Simple single-step tasks
- Exploratory work (use dynamic decomposition)

### Context Management in Chains

**Problem:** Context grows with each step

```
Step 1: 5K tokens
Step 2: 5K + 10K = 15K tokens
Step 3: 15K + 15K = 30K tokens
Step 4: 30K + 20K = 50K tokens
```

**Solution:** Summarize between steps

```python
def chain_with_summarization(steps):
    context = ""
    
    for step in steps:
        response = claude.message(step + context)
        
        # Summarize to keep context manageable
        if len(context) > 10000:
            context = summarize(context)
        
        context += response
    
    return context
```

---

## 7. Multi-Pass Review

### Why Multi-Pass?

**Problem:** Same-session review has confirmation bias

**Solution:** Fresh session for each pass

### Pass 1: Generation

```
Session 1:
Prompt: "Create user authentication system"
→ Code generated
```

### Pass 2: Review (Fresh Session)

```
Session 2 (NEW SESSION):
Prompt: "Review this authentication code for security issues:
[paste code from Pass 1]"
→ Unbiased review (no memory of generation)
```

### Why Fresh Session Matters

**Same session:**
```
Session 1:
- Generate code with reasoning X
- Review same code
- Still has reasoning X in context
- Confirmation bias: "This looks good because I built it this way"
```

**Fresh session:**
```
Session 1: Generate code
Session 2 (new): Review code
- No memory of generation
- No bias toward original reasoning
- Objective review
```

### Two-Pass Pattern

**Pass 1: Per-File Analysis**
- Code quality
- Style compliance  
- Individual file issues
- Local concerns

**Pass 2: Cross-File Integration (Fresh Session)**
- Architecture
- Integration issues
- Cross-file dependencies
- System-level concerns

### Example

**Pass 1:**
```
Session 1:
"Review each file in src/api/ for code quality"
→ Per-file findings
```

**Pass 2:**
```
Session 2 (fresh):
"Given these per-file findings [Pass 1 results], 
analyze cross-file integration and architecture"
→ System-level findings
```

---

## 🎯 Key Takeaways

### Critical Concepts

1. **Explicit > Vague** - Specific requirements get better results
2. **Few-shot: 2-4 examples** - Shows pattern clearly
3. **Schema guarantees structure** - NOT semantic correctness
4. **Validation-retry with specific errors** - Generic retries don't help
5. **Temperature 0.0-0.2 for consistency** - 0.7-1.0 for creativity
6. **Fresh session for review** - Avoids confirmation bias
7. **tool_choice="any"** - Guarantees structured output

### Common Anti-Patterns

❌ Vague prompts ("make it better")
❌ No examples (when format matters)
❌ Assuming schema validates semantics
❌ Generic retry messages
❌ Same-session self-review
❌ Wrong temperature for task
❌ Unbounded prompt chains (manage context)

### Prompt Engineering Checklist

- [ ] Prompt is explicit with clear criteria
- [ ] Examples provided (2-4 for format tasks)
- [ ] JSON schema defined for structured output
- [ ] Validation logic implemented
- [ ] Retry prompts include specific errors
- [ ] Temperature appropriate for task
- [ ] Fresh session for unbiased review
- [ ] Context managed in long chains

---

## 📖 Study Flow

**Now that you understand these concepts:**

1. ✅ Read this concept guide
2. ✅ Review the examples
3. ✅ Understand prompt patterns
4. ➡️ **Now attempt Domain 4 Questions** → [Domain-4-Questions.md](./Domain-4-Questions.md)

---

**Previous:** [Domain 3 Concepts](./Domain-3-Concepts.md) | **Next:** [Domain 5 Concepts →](./Domain-5-Concepts.md)
