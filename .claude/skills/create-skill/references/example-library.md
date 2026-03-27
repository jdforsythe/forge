# Example Library — BAD vs GOOD Pairs Across Skill Dimensions

This reference contains fully worked examples for every section of a skill. Load this
at Step 9 (Author Examples) or anytime you need structural templates for any section.

---

## Why Examples Matter

LLMs pattern-match against examples more reliably than they follow complex verbal rules.
2-3 well-chosen BAD vs GOOD pairs communicate implicit quality expectations that
paragraphs of instructions cannot convey. Every pair below is annotated with the
scientific mechanism that makes the GOOD version work.

**Rules for examples in skills:**
- Always include both BAD and GOOD (contrastive pairs)
- Cover diverse cases: at least one simple, one hard
- Place the most representative example LAST (recency bias — the model weights it most heavily)
- Annotate WHY the GOOD version works (enables generalization)
- 2-3 pairs in SKILL.md; more can go in a references/ file

---

## 1. Description (Triggering Surface)

<example type="BAD">
---
name: api-review
description: Reviews APIs for quality and best practices.
---
</example>

<example type="GOOD">
---
name: api-review
description: |
  Reviews REST and gRPC API designs for resource modeling, backward compatibility,
  error handling (RFC 7807), and versioning strategy. Use this skill when someone
  says "review my API," "is this endpoint design right," "check my proto file,"
  "help me design an API," or is building any HTTP or gRPC interface — even if they
  just say "I need to expose this data" or "how should clients talk to this service."
  Do NOT use for internal function signatures, database schema review, or frontend
  component APIs.
---
</example>

**Mechanism:** The BAD version is single-register (only generic terms), under 15 words,
and has no trigger phrases, user scenarios, or exclusions. It will undertrigger on
casual queries and produce generic output when it does fire. The GOOD version is
dual-register (RFC 7807, resource modeling + "is this endpoint design right," "I need
to expose this data"), ~100 words, with explicit exclusions and edge-case triggers.

---

## 2. Vocabulary Payload

<example type="BAD">
## Vocabulary
- API design
- Error handling
- Security
- Performance
- Documentation
</example>

<example type="GOOD">
## Expert Vocabulary Payload

**Resource Design:**
- RESTful resource modeling, sub-resource vs independent resource
- idempotent operations (GET, PUT, DELETE), safe methods (RFC 7231)
- HATEOAS (Richardson Maturity Model level 3), link relations (RFC 8288)

**Error Handling & Resilience:**
- RFC 7807 Problem Details, structured error responses with `type` URI
- idempotency keys (Stripe pattern) for retry safety
- circuit breaker (Michael Nygard, Release It!), bulkhead isolation

**Evolution & Compatibility:**
- additive-only changes, Postel's law / robustness principle
- API deprecation lifecycle, sunset header (RFC 8594)
- consumer-driven contract testing (Pact), provider verification
</example>

**Mechanism:** The BAD version uses terms so generic they could describe any software.
"API design" routes to introductory REST tutorials. The GOOD version uses precise terms
with RFC numbers and originators: "RFC 7807 Problem Details" routes to the actual
specification; "circuit breaker (Michael Nygard)" routes to Release It! content.
Sub-domain clustering creates local coherence. Zero consultant-speak.

---

## 3. Anti-Pattern Entry

<example type="BAD">
### Avoid bad error handling
- Don't return generic errors
- Make sure errors are useful
- Handle edge cases properly
</example>

<example type="GOOD">
### Primitive Obsession in Error Responses (Martin Fowler)
- **Detection:** API returns plain strings or integer codes for errors; no structured
  error body; clients must parse error messages with string matching; different
  endpoints return errors in different formats
- **Why it fails:** Unstructured errors force client-side string parsing, which is
  fragile across locales and API versions. Inconsistent formats multiply client
  error-handling code.
- **Resolution:** Adopt RFC 7807 Problem Details format: every error returns
  `{type, title, status, detail, instance}`. Define an error type registry.
  Use `type` URI for machine-readable classification, `detail` for human context.
- **Prevention:** Error response format is defined once in API standards and enforced
  by middleware — individual endpoints do not choose their error format.
</example>

**Mechanism:** The BAD version uses vague language that applies to anything ("avoid bad,"
"make sure useful"). The GOOD version has a named pattern with originator (routing
signal), specific detection signals (observable indicators, not intentions), a concrete
resolution with a named standard (RFC 7807), and a prevention principle that generalizes
(middleware enforcement). The "Why it fails" sentence enables the model to apply the
principle to related error-handling situations.

---

## 4. Behavioral Instruction

<example type="BAD">
When reviewing an API, you should look at the endpoints and think about whether they
follow good patterns. Consider the naming, the HTTP methods, the response codes, and
the error handling. If something looks wrong, mention it. Try to be helpful and provide
suggestions for improvement where possible.
</example>

<example type="GOOD">
1. SCAN the API definition for resource naming violations.
   Check: plural nouns for collections, singular for singletons, no verbs in URIs.
   IF violations found: List each with the corrected URI.
   OUTPUT: Resource naming assessment (pass/violations list).

2. VERIFY HTTP method semantics.
   For each endpoint, confirm: GET is safe (no side effects), PUT is idempotent
   (same result on retry), DELETE is idempotent, POST is used only for non-idempotent
   creation or actions.
   IF method semantics are wrong: Identify the specific violation and the correct method.
   OUTPUT: Method semantics assessment (pass/violations list).

3. EVALUATE error response consistency.
   Check: all endpoints return RFC 7807 Problem Details format; error types use URI
   identifiers; status codes match HTTP semantics (4xx for client errors, 5xx for server).
   IF inconsistent: Flag the inconsistency and provide the standardized format.
   OUTPUT: Error response assessment (pass/violations list).
</example>

**Mechanism:** The BAD version is prose, passive ("you should look at"), vague ("good
patterns," "something looks wrong"), and lacks sequencing or output requirements. There
are multiple valid interpretations. The GOOD version uses imperative verbs (SCAN,
VERIFY, EVALUATE), explicit conditions (IF violations found), specific checks
(plural nouns, RFC 7807), and required outputs per step. There is exactly one way
to execute these instructions.

---

## 5. Output Format

<example type="BAD">
## Output
Provide a helpful review of the API with suggestions for improvement.
</example>

<example type="GOOD">
## Output Format

### API Review Report

```
## Resource Model Assessment
[For each resource: name, URI pattern, verdict (PASS/ISSUE), fix if needed]

## Method Semantics
[For each endpoint: method, idempotency status, verdict, fix if needed]

## Error Handling
[Format compliance: RFC 7807 (YES/NO), consistency score, specific gaps]

## Backward Compatibility
[Breaking changes detected: list with severity and migration path]

## Summary
- Critical issues: [count] — must fix before deployment
- Recommendations: [count] — improve quality but not blocking
- Strengths: [count] — things done well, reinforce these patterns
```
</example>

**Mechanism:** The BAD version gives no structure — "helpful review" means different
things to different people. The GOOD version provides a concrete template with sections,
field names, and expected content types. The model and the user both know exactly what
the output looks like. The template is specific enough to guide (named sections,
severity levels) but flexible enough to adapt (bracketed placeholders for content).

---

## 6. Questions This Skill Answers

<example type="BAD">
## Questions
- How do I design an API?
- What are API best practices?
- How do I handle errors in APIs?
</example>

<example type="GOOD">
## Questions This Skill Answers

- "Review my API endpoints"
- "Is this REST design right?"
- "Check if my API is backward compatible"
- "How should I handle errors in my API?"
- "I need to expose this data to clients — what's the best approach?"
- "Does this proto file follow good gRPC patterns?"
- "My API keeps breaking clients when I deploy — help"
- "Should this be a sub-resource or its own endpoint?"
- "What HTTP status code should I use for [situation]?"
- "Help me version my API without breaking existing clients"
- "Is it okay to use verbs in REST URLs?"
- "Review my OpenAPI spec for problems"
</example>

**Mechanism:** The BAD version has only 3 questions, all formal, all starting with "How
do I." The GOOD version has 12 questions mixing registers: formal ("Is this REST design
right?"), casual ("My API keeps breaking clients — help"), imperative ("Review my
endpoints"), question-form ("Should this be a sub-resource?"), and scenario-based
("I need to expose this data"). The variety covers the vocabulary gap — different users
ask the same thing in different ways. These function as retrieval anchors, self-
documentation, and implicit test cases for trigger reliability.
