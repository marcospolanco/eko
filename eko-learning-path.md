# EKO Learning Path

A progressive tutorial for explaining the `schemas/` architecture and why it looks the way it does.

**Audience:** A first-time visitor who needs to understand EKO without getting lost in JSON Schema.  
**Hands-on companion:** [`examples/seaway_cba/`](examples/seaway_cba/), a real composite that validates against these schemas.

Status note: the schemas are **draft v0.1.0**. They encode the architecture; they are not yet a complete normative interpreter. Where draft JSON and the artifact diverge, this path says so.

---

## How to use this document

Read the lessons in order once. Each lesson answers one question that a skeptical listener is likely to ask. After Lesson 6, a reader can open `schemas/` and point at files with confidence. After Lesson 11, the whole system should be explainable in under five minutes.

Suggested talk tracks are marked **Talk track**.

---

## Map of the territory

Three schema files. One job each.

```text
schemas/
  eko.schema.json                 ← WHAT is released (typed knowledge + envelope)
  rule-ir.schema.json             ← HOW domain control flow is declared (no eval)
  resolution-profile.schema.json  ← WHICH release wins when many apply
```

They exist because the design kept hitting the same failure modes: documents that cannot be tested, policies that pretend to be facts, agents that invent control flow, knowledge that grants itself power, and multi-policy enterprises with no deterministic winner.

Everything else in this path is the story of those failure modes and the settlements that produced those three files.

---

## Lesson 1: Why knowledge had to be redefined

**Question this lesson answers:** Why isn't a better knowledge base or a better RAG chunking strategy enough?

For decades, knowledge systems assumed a human reader. Humans bring background context, tolerate ambiguity, and fill gaps with judgment. Libraries of documents were enough.

That consumer is changing. An AI system arrives with the opposite profile: huge bandwidth, zero innate organizational context, high sensitivity to ambiguity, and the ability to act at machine speed.

Ashu Roy's framing (which the design absorbed without parroting) names the deeper shift: von Neumann put *instruction* on one side of a line and *data* on the other. Engineering discipline followed the instruction side: versioning, review, tests, deploy, rollback. Generative AI moved the danger. In an LLM-based system, the model is a general-purpose interpreter; the corpus it retrieves at runtime is the program. What the system *does* is determined by knowledge, not by application source code.

**Talk track:** "We are not improving documentation. We are engineering the instruction layer for AI, with the same discipline we already give code."

If that sentence lands, the rest of the architecture is just the consequence.

---

## Lesson 2: The axiom (one sentence that loads the whole schema)

**Question:** What is the unit we are managing?

> **Knowledge is governed, context-sensitive information that connects evidence about the world to correct behavior within a defined scope.**

Every word becomes structure later:

| Word in the axiom | What it forces into the schema |
|---|---|
| Governed | owner, version, attestations, lifecycle, deployment, rollback |
| Context-sensitive | applicability / static scope + dynamic predicates |
| Information | evidence, sources, freshness |
| Connects evidence to behavior | rule IR + action contracts, not prose alone |
| Correct behavior | profile-appropriate tests (Corollary 2: unevaluable "knowledge" is opinion) |
| Defined scope | boundaries, abstain/escalate, conflict handling |

Three corollaries from the artifact drive profile design:

1. Knowledge is more than what is true (facts alone are not enough).
2. Knowledge that cannot be evaluated is not knowledge.
3. **Truth and authority are different accountabilities:** empirical claims answer to the world; policies answer to an enacting authority.

Corollary 3 is why EKO refuses a single mushy "knowledge object" type. That refusal becomes Lesson 4.

---

## Lesson 3: The executability ladder (why documents fail)

**Question:** Why not keep policies as articles and let the model interpret them?

Trace one example up the ladder:

| Level | Example | What it can do |
|---|---|---|
| Data | `73°F` | Nothing alone |
| Content | "The server room is 73°F." | Inform a human |
| Knowledge | "If the room exceeds 85°F, alert facilities, because equipment degrades." | Connect condition → consequence → reason |
| Instruction | Explicit steps, prohibitions, escalation, tool bindings | Govern agent behavior |

The progression is **executability**, not volume. A refund policy in production is simultaneously a number, customer-facing copy, a decision rule, a behavioral constraint, and an authorization to call a payments API. Documents force those apart. EKO holds them in one *governed release* while letting different authorities approve different pieces.

**Talk track:** "RAG retrieves content. EKO releases instruction. Retrieval can help discovery; it cannot be the authority."

---

## Lesson 4: Why five profiles exist

**Question:** Why `claim | policy | procedure | action_contract | composite` instead of one type?

Because treating everything as the same object dilutes accountability (the "conceptual dilution" critique accepted in the design conversation).

| Profile | What it is | Validated by | Conflicts resolved by |
|---|---|---|---|
| `claim` | Empirical assertion with sources | Verification, freshness, falsification | Claim adjudication; unresolved contradiction may be preserved |
| `policy` | Normative rule enacted by authority | Authority, scope, enactment provenance | Precedence (resolution profile) |
| `procedure` | Declared sequence toward an outcome | Behavioral tests, dry-run | Explicit supersession / release pinning |
| `action_contract` | Binding to a real-world capability | Contract tests, security review | Capability scoping |
| `composite` | Release that assembles the above | Release admission + integration tests | Manifest pinning |

In `eko.schema.json`, `profile` is an enum, and `allOf` conditionals force the matching `component` shape. A help-center fact is a `claim` and never needs a behavior test suite. A refund policy is typically a `composite` that pins a `policy`, presentation/evidence, and an `action_contract`.

**Talk track:** "One schema, graded rigor. We do not fact-check policies or enact observations."

A quick pass through the file should reveal:

- top-level required fields: `eko`, `version`, `profile`, `metadata`
- the `profile` enum
- `$defs/claim_component` vs `$defs/policy_component`

That is Lesson 4 made concrete.

---

## Lesson 5: Why there are three schema files (not one mega-schema)

**Question:** Why split Rule IR and Resolution Profile out of `eko.schema.json`?

Because they answer different questions and change on different cadences.

```text
                    ┌─────────────────────────────┐
   Author / review  │  eko.schema.json            │  "What am I releasing?"
                    │  identity, profile,         │
                    │  evidence, facts, tests,    │
                    │  attestations, deployment   │
                    └─────────────┬───────────────┘
                                  │ embeds / refs
                    ┌─────────────▼───────────────┐
   Domain logic     │  rule-ir.schema.json        │  "What control flow is allowed?"
                    │  states, transitions,       │
                    │  approvals, action refs     │
                    └─────────────┬───────────────┘
                                  │ selected under
                    ┌─────────────▼───────────────┐
   Multi-EKO world  │  resolution-profile.schema  │  "Which release governs this case?"
                    │  applicability, precedence, │
                    │  conflict, registry         │
                    └─────────────────────────────┘
```

- **EKO envelope** = the unit of deployment (like a signed release).
- **Rule IR** = the unit of domain control flow (like a constrained AST).
- **Resolution profile** = the unit of multi-policy combination (like XACML combining algorithms, scoped per domain/jurisdiction).

Policies *reference* a resolution profile; they do not redefine the resolver. That separation exists because "precedence policy is itself knowledge" would recurse forever without a trust root (Lesson 10).

### UML snapshot: how the three schema files relate

```mermaid
classDiagram
    class EKOEnvelope {
      +eko
      +version
      +profile
      +metadata
      +attestations
      +deployment
    }

    class ClaimComponent {
      +evidence[]
      +freshness
      +scope
    }

    class PolicyComponent {
      +rule_ir
      +fact_bindings[]
      +behavioral_conduct
    }

    class ProcedureComponent {
      +steps[]
      +compensation
      +behavioral_conduct
    }

    class ActionContract {
      +capabilities[]
      +preconditions[]
      +postconditions[]
      +reversibility_class
    }

    class CompositeRelease {
      +component_refs[]
      +digests[]
      +resolution_profile_ref
    }

    class RuleIR {
      +states[]
      +transitions[]
      +approval_gates[]
      +bounds
    }

    class ResolutionProfile {
      +applicability_strategy
      +precedence
      +conflict_resolution
      +registry_configuration
    }

    EKOEnvelope <|-- ClaimComponent
    EKOEnvelope <|-- PolicyComponent
    EKOEnvelope <|-- ProcedureComponent
    EKOEnvelope <|-- ActionContract
    EKOEnvelope <|-- CompositeRelease
    PolicyComponent --> RuleIR
    ProcedureComponent --> RuleIR
    CompositeRelease --> ResolutionProfile
```

This diagram is intentionally small: the goal is to show the accountability boundaries, not every field.

---

## Lesson 6: The release model (the git synthesis)

**Question:** Why not one big mutable knowledge object?

The critique accepted in the conversation: a naive monolithic object creates **false atomicity**. Legal changes the rule; communications changes the customer copy; security signs the tool binding. Those evolve at different rates and are approved by different people.

**Settlement:** the git model.

- Components are immutable, content-addressed, independently versionable.
- The EKO **release** is a signed manifest that pins specific component hashes (and the interpreter).
- Deployment, canary, rollback, and audit operate on releases, not on live mutation of a document.

In the draft schemas, you see this most clearly in the `composite` profile: component refs + digests + compatibility matrix (see [`examples/seaway_cba/seaway-cba.composite.json`](examples/seaway_cba/seaway-cba.composite.json)).

```text
composite release
  ├─ claim@1.0.0     sha256:…
  ├─ policy@1.0.0    sha256:…
  ├─ procedure@1.0.0 sha256:…
  ├─ action_contract@1.0.0 sha256:…
  └─ resolution profile pin
```

Draft vs artifact honesty: the full artifact also pins evidence snapshots, source-to-IR maps, behavior hashes, and an append-only deployment registry. The v0.1 schemas capture the envelope and digests; they do not yet encode every annex field from the normative write-up.

**Talk track:** "Atomicity of change lives at the release. Independence of approval lives at the component."

---

## Lesson 7: Rule IR: knowledge as code-as-data

**Question:** Why a state machine IR instead of "the LLM follows the policy prose"?

Because "code as data" implies a serialized program whose semantics exist only through a specific interpreter, not prose the model informally follows.

From the critique that reshaped the design:

```text
Knowledge Object (immutable, typed declarative program)
  + runtime facts and authorized capabilities
  + interpreter version
  → validated workflow instance
  → durable execution trace
```

`rule-ir.schema.json` is the deliberately small DSL:

- **States:** `decision | action | approval | terminal | error | escalation`
- **Transitions:** conditional edges (predicates)
- **Action references:** capability/procedure invocations, not implementations
- **Approval gates:** human / automated / hybrid
- **Bounds:** timeouts, cycle detection

What it deliberately refuses:

- arbitrary code / `eval`
- inventing new domain transitions at runtime
- owning retry/idempotency physics (those are runtime invariants)

**Domain vs runtime split** (load-bearing):

| Knowledge owns | Runtime owns |
|---|---|
| Applicability, branching, required steps | Condition evaluation, scheduling |
| Allowed actions, escalation paths | Authorization enforcement, approvals |
| Reversibility *class* and remediation *intent* | Idempotency, leases, timeouts, compensation *execution* |

**Talk track:** "The runtime must not decide that damaged items escalate. The knowledge must not implement its own retries."

A quick look at `rule-ir.schema.json` should reveal `state.type` and `action_reference`. A policy example's embedded `rule_ir` in Seaway overtime rules shows how that shape gets used.

---

## Lesson 8: Fact bindings and three-valued logic

**Question:** Why can't the IR just read "the order age" from context?

Because impeccably auditable wrong behavior is still wrong. Runtime inputs are not trusted by default.

Every predicate input needs a **fact binding**: name, source, type, and `on_unknown` behavior (`ask | abstain | refuse | escalate | …`).

Evaluation is three-valued: `true | false | unknown`. Unknown is never silently coerced into a branch.

Ordering matters (this was a real hole fixed late in the artifact):

1. Static scope against the trusted invocation envelope → candidates  
2. Acquire applicability facts using candidate bindings  
3. Evaluate dynamic applicability (unknown candidates can force `indeterminate`)  
4. Apply precedence → select release(s)  
5. Only then acquire execution-only facts for the selected release  

In `eko.schema.json`, look at `$defs/fact_binding`. In Seaway, look at overtime `fact_bindings` and the `unknown-fact-abstain` test fixture.

**Talk track:** "Missing facts are first-class. We escalate or abstain; we do not invent."

---

## Lesson 9: Action contracts: declare capabilities, never grant them

**Question:** Why isn't "authorization_required: true" enough?

Because knowledge that can authorize itself is a privilege-escalation surface.

Settlement:

- The EKO **declares** required capabilities (e.g. `payments:refund`, `hr.grievance.file`).
- Enterprise IAM **grants** concrete, scoped, short-lived capabilities at **link** time.
- Tool interfaces, pre/postconditions, and reversibility class live on the `action_contract` profile.

Reversibility vocabulary (domain knowledge, not runtime physics):

| Class | Meaning |
|---|---|
| reversible | automatic inverse restores prior state |
| compensable | automatic compensating action restores a business invariant |
| reconcilable | automation contains the defect; a human must finish repair |
| irreversible | no supported restoration path |

**Talk track:** "Knowledge may say a refund MAY be issued. It can never authorize itself to issue one."

Show `action_contract` in `eko.schema.json` and [`examples/seaway_cba/hr-capabilities.contract.json`](examples/seaway_cba/hr-capabilities.contract.json).

---

## Lesson 10: Resolution profiles: many EKOs, one winner (or honest failure)

**Question:** What happens when two policies both apply?

Typed edges (`supersedes`, `exception_to`) are not enough. Enterprises need a deterministic combining mechanism whose *policy* can vary by domain, but whose *security semantics* cannot be rewritten by the knowledge itself.

`resolution-profile.schema.json` encodes:

- **Applicability strategy:** static scope, dynamic predicate, or hybrid
- **Precedence:** priority, temporal, hierarchical, or composite
- **Conflict resolution:** precedence wins, merge, escalate, deny all, …
- **Fact resolution:** source priority, unknown propagation, stale handling
- **Registry configuration:** where the authoritative candidate set comes from

Invariant resolver outcomes: `resolved | not_applicable | indeterminate | conflict`.

Critical settlement from the conversation: **behavior of a selected release starts only after selection.** On `conflict` or resolution-level `indeterminate`, the **resolution profile** (constrained by a parent governance charter) refuses and escalates, not whichever release almost won. No silent tie-breaking. No global "lowest risk wins" (risk is domain-defined).

Trust root that breaks recursion:

```text
platform trust root
  → enterprise governance charter
    → jurisdiction/domain resolution profiles
      → EKO releases
```

**Talk track:** "Precedence is governed knowledge. The resolver is not. A program cannot rewrite its runtime's protections from inside."

Show [`examples/seaway_cba/resolution-profile.json`](examples/seaway_cba/resolution-profile.json): FLRA > CBA > agency guidance, temporal newest-wins, conflict detection.

---

## Lesson 11: The crux: the model at both borders, citizen of neither

**Question:** Where is the LLM in this architecture?

This is the core claim to hold onto when explaining EKO.

**Knowledge is code-as-data. The language model appears on both sides of it, and never above it.**

```text
authoring:  human prose  →  model proposes IR + source map  →  deterministic validate
            →  human reviews source-to-IR diff  →  attested release

runtime:    model may retrieve candidates, elicit facts, render language
            →  deterministic resolver + interpreter own authority
            →  model never authors control flow that can act
```

Pipeline:

```text
governed EKO release
  + verified runtime facts
  + externally granted capabilities
  + pinned interpreter
  → compile / link / instantiate
  → bounded execute
  → durable trace
```

The two historic failure modes this dissolves:

1. Hand-authored formalism nobody can maintain (classic rules/BPM)
2. Model-improvised behavior nobody can govern (naive agents)

**Punchline:** the model is the translator at both borders of knowledge, and the citizen of neither; authority belongs to the compiled, attested artifact alone.

### UML snapshot: authoring and runtime boundaries

```mermaid
sequenceDiagram
    participant Human as Human Author
    participant Model as LLM
    participant Schema as JSON Schema Validator
    participant Review as Human Reviewer
    participant Resolver as Deterministic Resolver
    participant Runtime as Runtime Interpreter

    Human->>Model: Draft prose + source material
    Model->>Schema: Propose IR / component JSON
    Schema-->>Model: Valid / invalid feedback
    Model-->>Human: Draft with structured diff
    Human->>Review: Approve or request changes
    Review-->>Human: Attested release
    Human->>Resolver: Submit release + context
    Resolver->>Runtime: Select applicable release
    Runtime->>Runtime: Evaluate facts and execute bounds
    Runtime-->>Human: Durable trace
```

The sequence shows where the model can assist and where it cannot own authority.

---

## Lesson 12: Walk the schemas with Seaway (hands-on)

Do this once with the files open. Timebox: 15 minutes.

1. Open [`schemas/README.md`](schemas/README.md): three files, ten concerns.
2. Open [`examples/seaway_cba/README.md`](examples/seaway_cba/README.md): five profiles in one family.
3. Open `unit-recognition.claim.json`: claim + evidence, no rule IR required.
4. Open `overtime-rules.policy.json`: policy + `fact_bindings` + embedded `rule_ir`.
5. Open `grievance-procedure.json`: procedure steps + capability refs.
6. Open `hr-capabilities.contract.json`: declared capability, not a grant.
7. Open `resolution-profile.json`: how overlaps resolve.
8. Open `seaway-cba.composite.json`: the release that pins digests together.
9. Skim `tests/unknown-fact-abstain.fixture.json` and `tests/overtime-eligible.fixture.json`: evaluation is part of the knowledge.

Optional validation:

```bash
# From repo root, with ajv installed
ajv validate -s schemas/eko.schema.json -d examples/seaway_cba/overtime-rules.policy.json
ajv validate -s schemas/resolution-profile.schema.json -d examples/seaway_cba/resolution-profile.json
```

You have now seen every major architectural commitment as concrete JSON.

---

## Lesson 13: Design decisions → schema fields (cheat sheet)

Use this when someone asks "why is this field here?"

| Design pressure (from critiques / artifact) | Where it landed |
|---|---|
| Truth ≠ authority | `profile` enum + per-profile `component` |
| False atomicity of one big object | `composite` + digests; release-level deploy |
| Knowledge deploys like code | `deployment`, attestations, version, tests |
| Runtime inputs untrusted | `fact_binding` + `on_unknown` |
| No self-granted authority | `action_contract` capabilities declared only |
| LLM must not invent control flow | `rule-ir.schema.json` constrained states |
| Multi-policy enterprises | `resolution-profile.schema.json` |
| Conflict ≠ IR bug | resolver `conflict` vs execution `indeterminate` (normative artifact; draft schemas approximate via conflict/escalation enums) |
| Behavior only after selection | resolution profile failure handling (artifact); selected release `behavioral_conduct` |
| Reproducible execution | `interpreter` pin on the EKO |
| Evaluation is constitutive | top-level `tests` + fixture types |
| Authoring burden | out of schema scope; humans approve IR diffs |

---

## Lesson 14: Five-minute explanation (script)

If only five minutes are available:

1. **Premise (30s):** The model is the interpreter; enterprise knowledge is the program. Discipline must move with the danger.
2. **Unit (30s):** An EKO is governed, scoped instruction that connects evidence to testable behavior.
3. **Shape (60s):** Five profiles so claims, policies, procedures, and tool contracts are accountable differently. Releases pin immutable components like git tags pin commits.
4. **Execution (60s):** Small Rule IR, no eval. Facts are contracted. Capabilities are declared, granted externally. Many policies resolve through a governed resolution profile.
5. **Model boundary (60s):** Model proposes IR at authoring time and helps at the edges at runtime. Authority stays in the attested artifact.
6. **Proof (60s):** Point at `schemas/` + Seaway example + one unknown-fact test.

Stop and return to Lessons 6–11 as needed.

---

## What the schemas do *not* fully encode yet

Be honest in explanations. Draft v0.1.0 is intentionally incomplete relative to the normative artifact:

- Predicate expressions are strings; formal expression AST / truth tables live in the artifact §4.4, not fully in JSON Schema.
- Attestation crypto format, capability URI grammar, and compensation execution semantics are placeholders or thin.
- Workflow-instance / trace schemas from artifact §4.6 are not separate JSON Schema files yet.
- Authoritative registry snapshot hashing and release-admission policy are described more richly in prose than in `eko.schema.json`.

That gap is fine for a learning path: the schemas are the portable skeleton; the artifact is the constitution.

---

## Suggested reading order after this path

1. [`README.md`](README.md): repo framing and what remains to be proven  
2. [`schemas/README.md`](schemas/README.md): machine-readable surface  
3. Artifact §§1–5 and §6: premise through resolution (skip annex-dense IR on first CEO pass)  
4. [`examples/seaway_cba/`](examples/seaway_cba/): concrete family  
5. [fbl-eko-convo.md](wip/fbl-eko-convo.md) and [fbl-executable-knowledge-artifact.md](wip/fbl-executable-knowledge-artifact.md): the critique-and-settlement trail behind each design choice  

---

## One-page concept map

```text
                    WHY (problem)
              von Neumann line moved
           knowledge is now the program
                        │
                        ▼
                 WHAT (axiom + profiles)
         governed, scoped, evidence → behavior
         claim | policy | procedure | contract | composite
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
   eko.schema     rule-ir.schema   resolution-profile
   release unit   domain control   multi-EKO winner
          │             │             │
          └─────────────┼─────────────┘
                        ▼
              HOW (runtime boundary)
     facts earned · capabilities linked · interpreter pinned
     model translates at borders · authority in attested artifact
                        ▼
              PROOF (examples + tests)
                 seaway_cba family
```

When someone asks why `schemas/` "all comes together," the answer is that sentence chain, not the JSON.

## UML appendix

If you want a more traditional UML view, this is the same architecture collapsed into a deployment-oriented picture:

```mermaid
flowchart LR
    Human[Human author] --> Model[LLM translator]
    Model --> Draft[Draft component JSON]
    Draft --> Validate[Schema validation]
    Validate --> Review[Human review]
    Review --> Release[Attested EKO release]
    Release --> Resolver[Deterministic resolver]
    Resolver --> Interpreter[Pinned interpreter]
    Interpreter --> Trace[Durable execution trace]
```

The flowchart is the shortest way to explain the lifecycle from prose to governed execution.
