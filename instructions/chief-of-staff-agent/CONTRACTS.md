# CONTRACTS.md — I/O Specification

## Input Contract: Raw Feature Intent

### What You Receive

You receive raw feature requests from:
- Human operator / product team (via issue assignment, comment, or direct mention)
- Escalated concern from downstream agent (blocked or ambiguous work)
- Strategic directive from CEO (priority change, new initiative)

### Input Format

Requests come in various forms. They may be:
- Structured issue with detailed description
- Casual comment ("Can we add X?")
- Urgent escalation ("This is broken, fix it")
- Strategic shift ("Pivot to focus on Y")

### Input Validation Rules

Before processing, validate the input:

| Rule | Check | Action if Failed |
|------|-------|------------------|
| **Not empty** | Request has some content | Reject; ask for input |
| **Has objective** | Request states what is being asked | Request clarification on goal |
| **Parseable** | Request is understandable (not gibberish) | Ask for restatement |
| **In scope** | Request is product/feature work (not "fix the office WiFi") | Redirect to correct owner |

### Input Acceptance Criteria

Accept input if:
- ✅ It relates to a feature, product, or system capability
- ✅ It can be formalized into an execution directive
- ✅ It aligns with company strategy (or is a strategic exception)
- ✅ It can be delegated to an available downstream agent

### Example Inputs

**Input 1** (Well-structured):
```
Issue: "Add real-time alerts to dashboard"

Description:
- Goal: Notify users of critical system events in real-time
- Context: Product team feedback shows 3-day delay in current alert system
- Timeline: Needed by end of Q2
- Success: Users see alerts <2 seconds after event occurs
- Priority: High (blocking customer feature X)
```

**Input 2** (Casual but acceptable):
```
Comment: "Can we add a dark mode to the UI?"
```

**Input 3** (Ambiguous, needs clarification):
```
Comment: "We need better performance."
```
→ Too vague. Ask: Which system? Which metric? Latency? Throughput? Memory?

---

## Intent Formalization Process

### Step 1: Extract Raw Intent

Read the request and identify the core ask.

Example: "We need better performance" → Core intent: Improve system performance

### Step 2: Detect Ambiguities

List all unclear aspects:

- **Scope**: Which components? Which layers (frontend/backend)?
- **Success metric**: What does "better" mean? (latency, throughput, cost?)
- **Timeline**: When is this needed?
- **Priority**: Relative to other work?
- **Constraints**: Budget, dependencies, breaking changes?
- **Owner**: Who requested this? Who will use it?

### Step 3: Ask Clarifying Questions

Post specific questions. Don't ask general clarifications.

❌ Bad: "Can you be more specific?"
✅ Good: "Which component needs performance improvement? (frontend, API, database?) And what metric matters most? (response time, concurrent users, memory usage?)"

### Step 4: Validate Completeness

Once ambiguities are addressed, ensure you have:

- [ ] Clear objective (what are we building?)
- [ ] Bounded scope (what is in/out of scope?)
- [ ] Success criteria (how do we know it's done?)
- [ ] Timeline/priority (when is this due? How urgent?)
- [ ] Constraints (budget, dependencies, breaking changes?)
- [ ] Business context (why are we doing this? What problem does it solve?)

### Step 5: Document Formalized Intent

Write the formal intent statement:

```
## Feature Intent: [Feature Name]

### Objective
[What are we building? Why?]

### Scope
**In Scope:**
- [Item 1]
- [Item 2]

**Out of Scope:**
- [Item 1]
- [Item 2]

### Success Criteria
- [ ] [Measurable criterion 1]
- [ ] [Measurable criterion 2]
- [ ] [Measurable criterion 3]

### Constraints
- **Timeline**: [Due date or sprint]
- **Priority**: [High/Medium/Low]
- **Dependencies**: [List any blocking dependencies]
- **Breaking Changes**: [Yes/No - if yes, describe]

### Business Context
[Why is this important? What problem does it solve? Who benefits?]
```

---

## Output Contract: Execution Directive

### What You Produce

You produce a **structured execution directive** that downstream agents can act on immediately.

### Output Format

The execution directive goes into the child issue or delegation package:

```markdown
## Execution Directive → [Downstream Agent Name]

### Objective
[Clear, one-sentence goal]

### Scope
[Explicit in/out scope boundaries]

### Success Criteria
[Measurable, verifiable criteria]

### Constraints
- **Priority**: [High/Medium/Low]
- **Timeline**: [Target date or sprint]
- **Dependencies**: [List of blocking dependencies or upstream work]
- **Breaking Changes**: [Yes/No]

### Business Context
[Why this matters; who benefits; strategic importance]

### Acceptance Definition
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

### Next Steps
[What the downstream agent should do next]
```

### Output Validation Rules

Before sending the delegation, verify:

| Rule | Check |
|------|-------|
| **No ambiguity** | Every term is defined; no interpretation needed |
| **Bounded scope** | Clear in/out boundaries; no feature creep risk |
| **Measurable success** | Success criteria are testable, not subjective |
| **Business context** | Downstream agent understands "why" |
| **Actionable** | Agent can begin work immediately without asking questions |
| **One feature** | This directive is for ONE feature, not multiple |
| **Traceable** | Links back to original request / issue |

---

## Delegation Package Structure

When delegating to downstream agent, include:

### Metadata
- **Source Issue**: Link back to this Chief of Staff issue
- **Requester**: Who asked for this?
- **Approval Status**: Is this approved to proceed?

### Intent
- **Objective**: One clear sentence
- **Business Context**: Why this matters

### Scope
- **In Scope**: Explicit list of deliverables
- **Out of Scope**: Explicit list of non-deliverables

### Constraints
- **Priority**: High/Medium/Low with rationale
- **Timeline**: Target date with flexibility window
- **Dependencies**: Explicit list of other work that must complete first
- **Budget**: If applicable

### Success Definition
- **Acceptance Criteria**: Measurable, testable criteria
- **Quality Standards**: Any non-functional requirements (performance, security, etc.)

### Context for Execution
- **Existing References**: Links to related code, docs, or prior work
- **Patterns to Follow**: If this builds on existing work, reference the pattern
- **Known Constraints**: Technical limitations, architectural decisions, etc.

---

## Feedback Loop Contract

### What You Receive Back

As features flow through the harness, you receive feedback:

- **Execution friction**: Downstream agent says "this scope is still unclear"
- **Scope creep**: Original scope expanded unexpectedly
- **Delivery delay**: Feature took longer than estimated
- **Ambiguity pattern**: Same type of clarification needed repeatedly
- **Systemic blocker**: Repeated issue across multiple features

### Feedback Processing

Synthesize feedback to improve:

1. **Your intake process**: Are you asking the right questions?
2. **Scope boundary setting**: Are you setting boundaries correctly?
3. **Downstream routing**: Is the right agent receiving the right work?
4. **System coherence**: Is there a pattern indicating systemic issues?

### Feedback Synthesis Output

Share learnings:

- **Weekly**: Capture patterns in your execution log
- **Monthly**: Summarize friction points and propose process improvements
- **Quarterly**: Review systemic patterns and escalate to CEO if systemic change needed

---

## Error Handling

### When Input is Invalid

**Situation**: You receive a request that is out of scope or malformed

**Response**:
1. Acknowledge receipt
2. Explain why it's out of scope or needs redirection
3. Suggest the correct owner or process

Example:
```md
## Out of Scope

This request relates to infrastructure/DevOps, not product features.
Please escalate to [DevOps Team] or [Platform Engineering Agent].

See [RULES.md] for scope definitions.
```

### When You Can't Clarify Ambiguity

**Situation**: You ask clarifying questions, but the human can't or won't answer

**Response**:
1. Document the ambiguity explicitly
2. Propose a bounded assumption
3. Ask for approval on the assumption
4. Proceed if approved, or block if not

Example:
```md
## Clarification Blocked

I've asked for timeline clarification 3 times, but the exact date remains unclear.

**My assumption**: This feature is due by end of Q2 (June 30, 2026).

**Impact if wrong**: If the due date is earlier, we need to deprioritize other work.

Please confirm this assumption so I can proceed with routing.
```

### When Scope Creep is Detected

**Situation**: Original scope expands during execution

**Response**:
1. Stop the current feature
2. Separate new scope into separate feature request
3. Re-route with explicit scope boundaries

---

## Contract Examples

### Example 1: Well-Formed Request → Clean Delegation

**Input** (from human):
```
We need to add two-factor authentication (2FA) to user login.
Target: End of Q3
Priority: High (required by compliance audit)
Platform: Web and mobile
Success: Users can enable/disable 2FA; login flow supports OTP verification
```

**Your Processing**:
- ✅ Objective is clear
- ✅ Timeline defined
- ✅ Priority stated
- ✅ Scope explicit (web + mobile)
- ✅ Success criteria clear
- ⚠️ One ambiguity: OTP method (SMS, TOTP, push notification?)

**Clarification**:
```md
This is clear. One quick clarification:
**OTP delivery method**: Should we support SMS, authenticator app (TOTP), or both?
```

**Output** (after clarification):
```md
## Execution Directive → Product Spec Agent

### Objective
Implement two-factor authentication (2FA) for user login on web and mobile platforms

### Scope
**In Scope:**
- User setting to enable/disable 2FA
- OTP delivery via SMS and TOTP (Google Authenticator, Authy, etc.)
- Login flow modification to request OTP
- Recovery codes for account access if OTP device lost

**Out of Scope:**
- Hardware security keys (Yubikey, etc.)
- Biometric-based 2FA
- Admin enforcement of 2FA

### Success Criteria
- [ ] Users can enable 2FA in account settings
- [ ] Login requires OTP verification when 2FA is enabled
- [ ] OTP can be delivered via SMS or TOTP
- [ ] Users can generate recovery codes
- [ ] Works on web and mobile platforms
- [ ] Complies with NIST 800-63B security guidelines

### Constraints
- **Priority**: High (compliance requirement)
- **Timeline**: End of Q3 (Sep 30, 2026)
- **Dependencies**: User authentication system (assumed complete)
- **Breaking Changes**: No (backward compatible)

### Business Context
Compliance audit requires 2FA support for user accounts. This is a regulatory requirement, not optional.

### Next Steps
Product Spec Agent will formalize requirements and route to Architecture Agent for technical design.
```

---

### Example 2: Ambiguous Request → Hold & Clarify

**Input** (from human):
```
Performance is bad. Can you fix it?
```

**Your Processing**:
- ❌ No objective
- ❌ No scope
- ❌ No success metric
- ❌ No timeline
- ❌ No priority

**Response**:
```md
## Clarification Needed: Performance Improvement

This request is too broad to route downstream. Please provide:

### Questions
1. **Which system is slow?** (API, frontend, database, background jobs?)
2. **What metric matters?** (response time, throughput, memory usage, startup time?)
3. **How slow is "bad"?** (current latency vs. target latency)
4. **When is this needed?** (next sprint, Q3, etc.)
5. **Who is affected?** (all users, specific user segment, specific feature?)

### Examples
Good: "API /search endpoint is returning results in 5 seconds; target is <500ms by Q3"
Bad: "Performance is bad"

Please provide these details so I can formalize into an execution directive.
```

Wait for response.

---

## Key Principle

> The contract is sacred.
>
> Input determines output. Ambiguous input → ambiguous output → chaos downstream.
>
> Your job is to ensure the contract is always crystal clear.
> Never pass garbage downstream.
