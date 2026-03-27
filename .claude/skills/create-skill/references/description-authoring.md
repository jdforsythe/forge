# Description Authoring — Writing Dual-Register, Pushy Descriptions

This reference contains the complete method for writing skill descriptions. Load this
at Step 10 (Author Description) of the skill creation workflow.

---

## Why the Description Is the Most Important ~100 Words

The YAML frontmatter description is the triggering surface — it's what the system
matches against user queries to decide whether to load the skill. If the description
doesn't match, the skill never fires, and everything else is irrelevant. Current
models tend to undertrigger — they err on the side of NOT using skills. The description
must actively fight this tendency.

These ~100 words have more impact on skill effectiveness than any other section.

---

## The ~100 Word Target

**Why ~100 words:** Long enough to include both formal terms and colloquial triggers.
Short enough to keep the always-in-context metadata footprint small. Anthropic's
skill-creator documentation uses this as the practical guideline.

**Acceptable range:** 80-120 words. Under 80 risks trigger anemia. Over 120 wastes
the always-in-context attention budget.

---

## Dual-Register Construction Method

### Step 1: Write the Formal Version First

Start with what the skill does using expert terminology:
> "Reviews REST and gRPC API designs for resource modeling, backward compatibility,
> error handling (RFC 7807), and versioning strategy."

This establishes the expert routing signal — when the skill fires, these terms
prime the model to draw from expert knowledge clusters.

### Step 2: Add Colloquial Trigger Phrases

For each formal concept, write 2-3 ways a user might ask for it in plain language:

| Formal Term | Colloquial Equivalents |
|-------------|----------------------|
| "resource modeling" | "is this endpoint design right," "how should I structure my API" |
| "backward compatibility" | "my API keeps breaking clients," "how do I change my API safely" |
| "error handling (RFC 7807)" | "how should I handle errors," "my error responses are a mess" |
| "versioning strategy" | "how do I version my API," "should I put v1 in the URL" |

### Step 3: Add User Scenarios

Describe what the person is trying to accomplish, not just what the skill does:
> "Use this skill when someone says 'review my API,' 'is this endpoint right,' or
> is building any HTTP or gRPC interface — even if they just say 'I need to expose
> this data' or 'how should clients talk to this service.'"

The phrase "even if they just say..." is powerful — it explicitly covers edge cases
where the user doesn't know the formal term for what they need.

### Step 4: Add Explicit Exclusions

State what the skill does NOT handle:
> "Do NOT use for internal function signatures, database schema review, or frontend
> component APIs."

Exclusions prevent mis-triggers and help delineate boundaries with adjacent skills.

---

## Trigger Phrase Generation

Before finalizing, list 10-15 ways a user might invoke this skill without knowing
its name. Organize by type:

**Direct requests:** "review my API," "check my endpoints"
**Questions:** "is this REST design right?", "what status code should I use?"
**Problems:** "my API keeps breaking clients," "errors are confusing"
**Vague requests:** "I need to expose this data," "how should clients talk to this?"
**Adjacent concepts:** "check my proto file," "review my OpenAPI spec"

Verify that the description contains enough matching terms to trigger on each.

---

## The Vocabulary Gap Problem

The vocabulary gap occurs when user query terms differ from description terms.
If the user says "check my endpoints" and the description only contains "API resource
modeling evaluation," the skill won't trigger.

**Solutions:**
1. Include both registers in the description (formal + colloquial)
2. Include explicit trigger phrases with user vocabulary
3. Include the "Questions This Skill Answers" section with diverse phrasing
4. Include synonyms: if the domain uses "API," also mention "endpoints," "interface,"
   "service," "routes"

---

## Worked Examples: Weak to Strong

### Example A: Data Modeling Skill

**WEAK (single-register, terse):**
> "Designs database schemas following normalization principles."

**MEDIUM (formal terms added, but no colloquial):**
> "Designs relational database schemas using BCNF normalization, temporal modeling
> (SCD types), strategic denormalization, and index optimization for OLTP/OLAP."

**STRONG (dual-register, pushy, with scenarios and exclusions):**
> "Designs relational database schemas using normalization analysis (BCNF), temporal
> modeling (slowly changing dimensions), strategic denormalization, and index
> optimization for OLTP and OLAP workloads. Use when someone says 'design my database,'
> 'review my schema,' 'is this table structure right,' 'how should I model this data,'
> or needs to make data modeling decisions — even if they just say 'where should I
> store this' or 'my queries are slow because of the schema.' Do NOT use for query
> tuning without schema changes, application-level caching, or ORM configuration."

### Example B: Code Review Skill

**WEAK:**
> "Reviews code for quality."

**MEDIUM:**
> "Reviews code for cyclomatic complexity, coupling metrics, SOLID violations,
> and test coverage gaps using static analysis principles."

**STRONG:**
> "Reviews code for structural quality — cyclomatic and cognitive complexity, coupling
> metrics (Ca/Ce), SOLID principle violations, code smell detection (Fowler taxonomy),
> and test coverage analysis. Use when someone says 'review this code,' 'is this
> clean,' 'what's wrong with this PR,' 'check for code smells,' or is about to merge
> code — even if they just say 'take a look at this' or 'does this seem okay.' Do NOT
> use for feature design decisions, architecture review, or security-focused auditing."

### Example C: Deployment Skill

**WEAK:**
> "Helps with deployments."

**MEDIUM:**
> "Manages deployment pipelines with blue-green deployments, canary releases,
> and rollback strategies for containerized services."

**STRONG:**
> "Manages deployment pipelines using blue-green deployments, canary releases with
> traffic splitting, rolling updates, and automated rollback strategies for
> containerized services (ECS Fargate, Kubernetes). Use when someone says 'deploy this,'
> 'set up CI/CD,' 'how do I do zero-downtime deployments,' 'my deploy failed,' or is
> preparing any production release — even if they just say 'how do I get this to prod'
> or 'is this safe to ship.' Do NOT use for local development environment setup,
> Docker image building without deployment context, or infrastructure provisioning."

---

## Testing the Description

After writing the description, verify trigger reliability:

1. Write 5 colloquial test queries that use NONE of the formal vocabulary
2. For each query, ask: "Would this description plausibly match this query?"
3. If any query would NOT match: identify the vocabulary gap and add trigger phrases

Example test queries for an API review skill:
- "take a look at my endpoints" → Does the description mention "endpoints"?
- "is this the right way to structure my backend?" → Does it cover vague backend questions?
- "my clients keep breaking when I update the server" → Does it cover problem-state queries?
- "check my proto file" → Does it mention gRPC/proto alongside REST?
- "I need to expose some data from this service" → Does it cover "expose data"?
