---
name: ears-requirements-engineering
description: Write, review, decompose, and validate clear natural-language system and software requirements using the Easy Approach to Requirements Syntax (EARS). Use when turning stakeholder needs into testable requirements, reviewing specifications, identifying missing states or triggers, or improving requirements quality.
---

# EARS Requirements Engineering

Use this skill to author and review requirements using the Easy Approach to Requirements Syntax (EARS). EARS gently constrains natural language so requirements are clear, concise, active-voice, unambiguous, and verifiable.

EARS is a way of thinking about requirements, not merely a fill-in-the-blank template. Understand the system, operating states, inputs, and intended responses before writing the requirement. If a requirement cannot be expressed clearly, do not force it into a pattern: identify the missing information or use a more suitable representation.

## When to use

Apply EARS when:
- Converting stakeholder needs or informal statements into system/software requirements.
- Refining or reviewing natural-language requirements.
- Decomposing high-level requirements into lower-level requirements.
- Checking normal-operation and unwanted-behaviour coverage.
- Preparing requirements for mixed technical and non-technical audiences.

Do not use EARS as a substitute for requirements elicitation, architecture, interface modelling, state-machine analysis, mathematical specification, or verification planning.

## Core EARS patterns

Choose the pattern that reflects when the requirement applies.

### 1. Ubiquitous

Always active; not conditional on a state, feature, or event.

```text
The <specific system name> shall <required response>.
```

Example:

```text
The order-management service shall assign a unique identifier to each accepted order.
```

Challenge every apparently ubiquitous requirement: confirm that it applies in every relevant operating state.

### 2. State-driven

Applies continuously while one or more preconditions hold.

```text
While <precondition(s)>, the <specific system name> shall <required response>.
```

Example:

```text
While an account is locked, the authentication service shall reject password-based sign-in attempts.
```

Use `While` for conditions that define the operating state, not for an event that causes a one-time response.

### 3. Event-driven

Applies when a desired event is detected at the system boundary.

```text
When <trigger>, the <specific system name> shall <required response>.
```

Example:

```text
When a user submits a valid payment request, the payment service shall create a payment transaction.
```

Use one trigger. Distinguish the trigger from any preconditions that must already hold.

### 4. Optional-feature

Applies only when an optional feature is included.

```text
Where <optional feature is included>, the <specific system name> shall <required response>.
```

Example:

```text
Where multi-factor authentication is enabled, the authentication service shall require a second authentication factor during sign-in.
```

`Where` identifies feature availability, not a general operating state.

### 5. Unwanted behaviour

Specifies mitigation or response to an undesirable event or condition.

```text
If <unwanted trigger>, then the <specific system name> shall <mitigation response>.
```

Example:

```text
If the payment gateway is unavailable, then the checkout service shall retain the pending payment request for retry.
```

Write unwanted-behaviour requirements in a deliberate second pass after normal-operation requirements. Look for missing preconditions, unavailable data or services, invalid inputs, boundary conditions, and undesirable user or system behaviour.

## Combining conditions

Complex requirements can combine the optional-feature, state, and trigger clauses. Keep them in this order:

```text
Where <optional feature>,
while <precondition(s)>,
when <desired trigger>,
the <specific system name> shall <response(s)>.
```

For unwanted behaviour:

```text
Where <optional feature>,
while <precondition(s)>,
if <unwanted trigger>,
then the <specific system name> shall <mitigation response(s)>.
```

Omit clauses that do not apply. Do not add empty or redundant clauses.

Example:

```text
While the account is active, when the user requests a password reset, the identity service shall issue a time-limited reset token.
```

Example with an optional feature:

```text
Where audit logging is enabled, while a user session is authenticated, when the user changes a permission, the administration service shall record the actor, target, timestamp, and change.
```

## Cardinality rules

Enforce these limits for each individual EARS requirement:

| Element | Cardinality |
|---|---:|
| Preconditions (`while`) | Zero to many |
| Triggers (`when` or `if`) | Zero or one |
| System name / responsible actor | Exactly one |
| System responses | One to many |

Interpretation:
- Preconditions may be multiple and must all be true before a trigger can cause the response.
- A requirement has at most one trigger. Do not combine independent events with `and` or `or` to hide multiple triggers.
- If there is no trigger, the response applies while all stated preconditions hold, regardless of the order in which those preconditions become true.
- Name exactly one system, component, service, or other responsible actor.
- One or more responses may follow the same conditions and trigger.

Although the syntax permits multiple preconditions and responses, prefer no more than two or three of either in one requirement. Split requirements when doing so improves clarity, traceability, or independent verification. Consider a table, state diagram, sequence diagram, decision table, or mathematical expression when the logic becomes cumbersome.

## Authoring workflow

Follow this sequence.

1. **Understand the need**
   - Identify the stakeholder goal, problem, context, and intended outcome.
   - Keep goals separate from requirements. A goal is an aspiration; a requirement is an agreed, feasible, system-specific obligation that can be verified.
   - Do not turn a goal into a `shall` statement without feasibility analysis and agreement.
   - Preserve the original goal and its rationale when refining it into requirements.

2. **Establish scope and ownership**
   - Identify the system or component that must provide the behaviour.
   - Use its specific name; avoid vague actors such as `the system`, `the application`, or `the service` when several could be meant.
   - Confirm that the requirement belongs at the selected system level.

3. **Identify conditions**
   - Determine whether the requirement is always active, state-dependent, event-triggered, feature-dependent, or concerned with unwanted behaviour.
   - Separate feature availability (`Where`), preconditions/state (`While`), and trigger (`When` or `If`).
   - Put clauses in EARS order: `Where` → `While` → `When`/`If...then` → named actor → `shall` → response.

4. **Write the response**
   - Use active voice and a single, explicit responsible actor.
   - State the required behaviour, not an implementation design, unless the design is itself mandated.
   - Prefer concise, concrete wording.
   - Include multiple response elements only when they are caused by the same conditions and trigger.

5. **Make it verifiable**
   - Ensure the response can be checked by inspection, analysis, demonstration, or test.
   - Replace vague terms such as `fast`, `user-friendly`, `robust`, `appropriate`, and `sufficient` with measurable criteria when known.
   - State units, tolerances, boundary conditions, and reference points explicitly when relevant.
   - If a value is not yet known, retain `TBD` or `TBC` rather than inventing precision. Record who will resolve it and by when or at which decision gate.

6. **Review normal operation**
   - Check expected states, events, features, and responses.
   - Check whether apparently ubiquitous requirements truly apply in every operating state.

7. **Review unwanted behaviour in a second pass**
   - For each normal requirement, consider invalid, missing, delayed, unavailable, conflicting, repeated, or out-of-range inputs.
   - Consider missing preconditions and transitions between states.
   - Add separate `If...then` requirements for mitigations.

8. **Check coverage and decomposition**
   - Look for states with no defined behaviour, triggers with no response, and responses whose required inputs or preconditions are unspecified.
   - Use state/activity/sequence diagrams or tables to check scenario coverage where useful.
   - EARS improves visibility of coverage issues but does not itself guarantee completeness or traceability.

9. **Review and refine**
   - Check each requirement independently against the quality checklist below.
   - Split compound requirements where separate parts can be independently triggered, implemented, traced, or verified.
   - Keep rationale, source, parent requirement, and verification method in associated metadata rather than crowding the requirement statement.

## Quality checklist

For every requirement, verify:

- [ ] **Correct:** It expresses an agreed system obligation, not an unexamined stakeholder wish.
- [ ] **Necessary:** It contributes to a stakeholder need, constraint, interface, safety obligation, or other justified objective.
- [ ] **Clear:** A reader can identify the conditions, responsible actor, and required response.
- [ ] **Unambiguous:** Terms have one agreed interpretation; pronouns and vague references are avoided.
- [ ] **Concise:** Extraneous context, rationale, and design commentary are removed from the requirement sentence.
- [ ] **Active voice:** The entity responsible for the action is the grammatical subject.
- [ ] **Well-formed EARS:** The correct keyword and clause order are used.
- [ ] **Cardinality-compliant:** One actor, zero or one trigger, and at least one response.
- [ ] **Feasible:** The system can reasonably satisfy it.
- [ ] **Verifiable:** Objective evidence can establish whether it is met.
- [ ] **Atomic enough:** It does not bundle unrelated behaviours or independently verifiable obligations.
- [ ] **Traceable:** Its source, rationale, parent/child relationships, and verification method can be recorded.
- [ ] **Consistent:** It does not conflict with other agreed requirements.
- [ ] **Bounded:** Numeric values include units, tolerances, and conditions where needed; unknown values are explicitly marked TBD/TBC.

## Common defects and how to respond

| Defect | Correction |
|---|---|
| Vague actor (`the system`) | Name the specific system/component responsible. |
| Passive voice (`shall be stored`) | Identify the actor (`the storage service shall store...`). |
| Goal disguised as a requirement | Record the goal separately; derive feasible, agreed requirements. |
| State written as a trigger | Use `While` for a continuing precondition and `When` for an event. |
| Multiple triggers in one requirement | Split into separate requirements, one trigger per requirement. |
| Missing precondition | Identify the state or condition that must hold before the trigger. |
| Unwanted condition omitted | Add a second-pass `If...then` mitigation requirement. |
| Several unrelated responses | Split into independently verifiable requirements. |
| False precision | Use `TBD`/`TBC` and assign resolution ownership. |
| Excessive conditions or long sentence | Use a table, diagram, decision table, or other suitable notation. |
| Requirement contains rationale or explanation | Move rationale to linked metadata. |
| Design prescribed without justification | State the required outcome unless a design constraint is intentional. |

## When not to use EARS

Do not force EARS when:
- The requirement is inherently mathematical and is clearer as a formula.
- The requirement is inherently graphical, such as an operating envelope or geometric constraint.
- The logic is too complex to express clearly in a short natural-language statement.
- There are more than about three preconditions or many response elements.
- A use case, table, decision table, state machine, activity diagram, sequence diagram, interface definition, or formal model is more appropriate for the audience and problem.

EARS can complement these representations. Use EARS for textual requirements and models or tables for structure that is better expressed graphically or formally.

## Review response format

When asked to review requirements, return:

1. **Overall assessment** — concise summary of quality and major risks.
2. **Requirement-by-requirement findings** — preserve original IDs and quote or reference the original text.
3. **Proposed EARS rewrite** — provide a corrected statement only when the source contains enough information.
4. **Open questions** — explicitly list missing actor, state, trigger, response, threshold, unit, or acceptance criterion. Do not invent answers.
5. **Coverage gaps** — identify likely missing normal or unwanted-behaviour cases.
6. **Verification suggestions** — propose observable test or analysis evidence without claiming that the requirement has already been validated.

For each finding, classify the issue where useful: `actor`, `condition`, `trigger`, `response`, `ambiguity`, `testability`, `goal-vs-requirement`, `cardinality`, `coverage`, or `scope`.

If information is insufficient to produce a valid rewrite, explain what is missing and ask the smallest useful clarification question. Do not silently make assumptions.

## Rewrite response format

When asked to convert informal statements:
- Preserve the source meaning and all stated constraints.
- Separate goals, context, rationale, assumptions, and actual system obligations.
- Produce one requirement per independently triggered behaviour where practical.
- Assign IDs only if requested or if the surrounding project uses an established ID convention.
- Include an `Open questions` section for unresolved facts.
- Never invent numeric thresholds, units, timing, actors, or failure policies.

## Examples

### Informal statement

```text
The app should be secure and users should be able to reset passwords.
```

### Analysis

- `Secure` is a broad goal or quality attribute, not a verifiable requirement as written.
- Password reset is a capability, but the actor, trigger, token policy, and expected response need clarification.

### EARS requirement, if the stated behaviour is confirmed

```text
When a user requests a password reset, the identity service shall initiate the password-reset process.
```

This is only a structural example. It does not define token expiry, identity proofing, rate limits, notification channels, or account-enumeration protections. Those need separate, sourced requirements.

### Unwanted behaviour example

```text
If the password-reset service cannot deliver a reset notification, then the identity service shall record the failed notification attempt.
```

Use this only if recording failed delivery is an agreed required behaviour.

## Operating principles

- Be rigorous about meaning, not rigid about formatting.
- Prefer simple, natural language with a consistent logical order.
- Use a specific system name and active voice.
- Separate goals from requirements.
- Write normal-operation requirements first, then perform a deliberate unwanted-behaviour pass.
- Use no more than one trigger per requirement.
- Do not confuse a precondition with a trigger.
- Do not manufacture missing facts or false precision.
- Use another representation when it communicates the requirement more clearly.
- EARS does not by itself guarantee completeness, correctness, traceability, or test coverage; review these explicitly.
