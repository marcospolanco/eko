# EKO Impact on Agentic Engineering Design Process

**TL;DR:** EKO transforms enterprise knowledge from "retrieved content" into "governed instruction." This document explains how that shift changes how you design, build, test, deploy, and operate agentic systems.

---

## Overview

EKO (Executable Knowledge Objects) is not an add-on to agentic architecture—it fundamentally reshapes how we treat the *knowledge layer* in AI systems. Where conventional practice treats knowledge as "retrieval/content" that feeds the agent, EKO insists: **knowledge is the program**. It requires the same governance, testing, versioning, and deployment discipline we apply to application code.

This document details how EKO impacts each phase of the agentic design process, which industry metrics it transforms, and where it belongs in the AI system design flow.

> **Status Note:** EKO is a **proposed architecture and discipline**. The schemas are draft v0.1.0 and do not yet constitute a complete interpreter specification or production runtime. The design patterns below represent intended capabilities, not validated guarantees.

---

## Who is this for?

- **System architects** evaluating whether EKO fits their use case
- **Engineering managers** assessing rollout and operational implications  
- **Product leaders** understanding what metrics EKO transforms
- **Practitioners** who already understand agentic architecture patterns

If you're new to EKO, start with [`eko-learning-path.md`](eko-learning-path.md).

---

## When to Use EKO

EKO is not always the right answer. It imposes authoring, registry, and operational costs that must be justified by the problem.

**Use EKO when:**
- Knowledge governs high-consequence behavior (financial, regulatory, safety-critical decisions)
- Knowledge changes frequently and requires traceable deployment
- Multiple jurisdictions or authorities overlap with clear precedence rules
- Auditability and reproducible decision traces are non-negotiable
- The cost of incorrect knowledge application exceeds governance overhead

**Use plain RAG when:**
- Knowledge is informational (help center, reference material, evidence display)
- Source attribution matters but behavioral enforcement does not
- Latency constraints preclude resolution and binding overhead
- The use case is experimental or rapidly evolving

**Use deterministic workflows or policy engines when:**
- Rules are already stable, executable, and don't require LLM interpretation
- A rules engine, BPM system, or code can express the logic directly
- The knowledge rarely changes and doesn't require semantic reasoning

**Adopt EKO only when reliability, auditability, and change-management benefits exceed authoring, registry, and operational cost.**

---

## Where EKO Belongs in the AI System Design Flow

EKO operates at a **critical intersection** in the seven-layer production chain:

```
┌─────────────────────────────────────────────────────────────────┐
│                        AI SYSTEM DESIGN FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  1. API Layer              ──>  Authentication, rate limits      │
│  2. Orchestration          ──>  Intent classification, routing   │
│  3. Retrieval / Tools      ──>  ┌──────────────────────────┐    │
│                                 │  EKO LIVES HERE         │    │
│                                 │  (and influences above) │    │
│  4. Prompt Construction    ──>  └──────────────────────────┘    │
│                                 │                                │
│                                 │  Knowledge is NOT content    │
│                                 │  Knowledge is instruction    │
│                                 │  Knowledge = governed release │
│                                 │                                │
│  5. LLM Inference           ──>  Executes IR-bound behavior   │
│  6. Post-Processing         ──>  Validates against EKO guardrails
│  7. Response / UX           ──>  Presents with EKO citations    │
└─────────────────────────────────────────────────────────────────┘
```

**EKO transforms the Retrieval/Tools layer from a "fetch and hope" operation into a "load and verify" operation.**

- **Traditional RAG:** Retrieve chunks → inject into prompt → hope model interprets correctly
- **EKO-powered:** Load governed release → validate schema → bind facts → execute declared Rule IR → trace every decision

---

## Phase-by-Phase Impact on the Six-Phase Architecture Process

### Phase 1: Clarify — Enhanced Problem Framing

**Before EKO:** Questions focus on user tasks, latency, cost, accuracy.

**With EKO:** Additional mandatory questions:

| Question | Why It Matters |
|----------|----------------|
| What knowledge sources are *normative* (policy) vs *empirical* (claims)? | Policies require authority chains; claims require evidence. |
| What are the *consequences* of incorrect knowledge application? | Determines profile rigor: policy vs procedure vs claim. |
| Who *owns* each piece of knowledge and who can *approve* changes? | EKO requires attestations and governed releases. |
| What is the *temporal validity* of this knowledge? | EKO captures freshness requirements and stale-handling. |
| What *jurisdictional scopes* apply? | Resolution profiles govern overlapping authority. |

**Impact:** The clarify phase becomes longer but produces a **knowledge inventory** that prevents later surprises.

---

### Phase 2: Choose the Architecture — Profile Selection Drives Pattern

**Before EKO:** Decision tree: workflow → reflex agent → reasoning agent → RAG → multi-agent.

**With EKO:** Decision tree includes knowledge profile:

| Profile | Architecture Implication |
|---------|--------------------------|
| `claim` | RAG for evidence display; no Rule IR needed |
| `policy` | Requires Rule IR state machine; HITL at action gates |
| `procedure` | Requires multi-step orchestration; compensation paths |
| `action_contract` | Requires capability broker; middleware IAM integration |
| `composite` | Requires release registry; dependency resolution |

**New Anti-Pattern:** "One agent reads everything from a vector DB and figures it out."

**EKO Forces:** Distinct execution paths for distinct knowledge profiles. A refund *policy* is not retrieved like a help-center *claim*.

---

### Phase 3: Define Coordination — Typed Contracts Extend to Knowledge

**Before EKO:** Agent contracts specify input/output schemas, tools, permissions.

**With EKO:** Knowledge contracts specify:

- **Applicability:** Static scope + dynamic predicates
- **Fact bindings:** What runtime facts are required, and `on_unknown` behavior
- **Action references:** Which capabilities the knowledge may invoke (not grant)
- **Behavioral conduct:** Required explanations, prohibited conduct, escalation paths
- **Resolution priority:** When multiple policies apply, which wins

**Impact:** The shared task state schema expands:

```json
{
  "goal": "...",
  "plan": [...],
  "applicable_ekos": ["policy@1.0.0", "procedure@2.1.0"],
  "resolution_winner": "policy@1.0.0",
  "fact_bindings_satisfied": {...},
  "escalation_path": "human_review"
}
```

**New Coordination Responsibility:** The orchestrator must run the **resolution profile** before any EKO executes.

---

### Phase 4: Harden Reliability — EKO as a Failure Boundary

**Before EKO:** Reliability patterns: retries, idempotency, circuit breakers, HITL.

**With EKO:** Additional failure modes become explicit:

| Failure Mode | EKO Mitigation |
|--------------|----------------|
| Stale knowledge | Freshness thresholds in claim metadata |
| Conflicting policies | Resolution profile with precedence rules |
| Missing facts | `on_unknown: abstain | refuse | escalate | ask` |
| Unknown context | Explicit `refuse_unknown_status` terminal state |
| Invalid action request | Action contract validation before capability grant |
| Knowledge version mismatch | Interpreter pinning in release envelope |

**Impact:** EKO turns "the model was confused" into specific, debuggable failure states with declared recovery paths.

---

### Phase 5: Scale Deliberately — Release Model Changes Cost Structure

**Before EKO:** Scaling concerns: coordination cost, state contention, tool rate limits, model tiering.

**With EKO:** New scaling dimension: **knowledge release management**

| Scale Factor | EKO Implication |
|--------------|-----------------|
| Multiple jurisdictions | Resolution profiles per domain/jurisdiction |
| Rapid policy changes | EKO release velocity vs. deployment velocity |
| Cross-tenant policy variation | Tenant-scoped EKO registries |
| Knowledge dependencies | Composite release digests and compatibility matrices |

**New Bottleneck:** The EKO registry and resolution layer. This scales differently from LLM inference—pattern is closer to package registry + dependency resolution.

**Cost Impact:** By declaring fact bindings and `on_unknown` behavior, EKO can reduce expensive model loops that would otherwise attempt to "figure out" missing information through repeated retrieval and re-prompting. The savings depend on the frequency of unknown-fact scenarios; for well-scoped domains with stable fact availability, the reduction is modest. For complex, frequently-changing knowledge where ambiguity is common, the reduction is more significant.

---

### Phase 6: State Tradeoffs — EKO Adds Evaluation Dimension

**Before EKO:** Tradeoffs: latency vs. quality, cost vs. accuracy, simplicity vs. capability.

**With EKO:** Additional tradeoff axis:

| Tradeoff | Question |
|----------|----------|
| Knowledge formality | " prose → IR" authoring cost vs. execution reliability |
| Release granularity | One large EKO vs. many small components (atomicity vs. approval velocity) |
| Resolution strictness | Strict conflict detection vs. graceful degradation |

**Impact:** The "state tradeoffs" artifact now includes a **knowledge governance section** documenting who approves what, how often, and what the rollback policy is.

---

## Industry Metrics Impacted by EKO

EKO transforms measurement in three categories: **Compliance**, **Quality**, and **Operational Efficiency**. Each is presented as a metric tree with North Star, Metric Equation, Product Levers, and Counter-Metrics.

---

### Compliance: Correct Policy Decisions Without Unnecessary Escalation

**North Star:** Correct policy decisions without unnecessary escalation

**Metric Equation:**
```
correct_decisions = eligible_cases × correct_resolution_rate × (1 - unnecessary_escalation_rate) × accepted_resolution_rate
```

**Product Levers:**
| Lever | How EKO Helps |
|-------|---------------|
| Eligible case identification | Static scope + dynamic predicates in applicability strategy |
| Correct resolution accuracy | Rule IR traceability; branch coverage testing |
| Accepted resolution rate | Declared escalation paths reduce gray-area escalations |

**Counter-Metrics:**
| Counter-Metric | Why It Matters |
|----------------|----------------|
| False escalations | `on_unknown=escalate` when human review could have been avoided |
| Decision latency | Resolution and binding overhead vs. plain retrieval |
| Coverage gaps | Over-narrow applicability filters miss edge cases |

**Before EKO:** Policy violation rate measured post-hoc from incidents; audit trails incomplete.

**After EKO:** Every policy decision traces to a release version, fact bindings, and interpreter—enabling measurement from execution traces rather than incident retrospection.

---

### Quality: Grounded Policy Execution with Trajectory Correctness

**North Star:** Grounded policy answers with inspectable reasoning paths

**Metric Equation:**
```
quality_outcome = eligible_queries × (grounded_rate × trajectory_correctness × answer_relevance)
```

**Product Levers:**
| Lever | How EKO Helps |
|-------|---------------|
| Grounded rate | Policy answers only from validated EKO releases; refusal on missing evidence |
| Trajectory correctness | Rule IR makes every branch inspectable; grade path, not just answer |
| Answer relevance | Evidence linkage in claim profile; source traceable to release digest |

**Counter-Metrics:**
| Counter-Metric | Why It Matters |
|----------------|----------------|
| False refusals | Over-aggressive `on_unknown=refuse` when answer was available |
| Stale answers | Ingestion errors or incorrect freshness thresholds |
| Resolver defects | Resolution profile bugs selecting wrong policy |

**Before EKO:** "Model made something up" was a hallucination; refusal correctness was untestable.

**After EKO:** Refusal correctness testable via `unknown_fact` fixtures; `on_unknown` behavior declared and tested. However, **hallucination is not reduced to zero**—ingestion errors, incorrect facts, resolver defects, IR/interpreter bugs, and mis-scoped applicability remain as failure modes.

---

### Operational Efficiency: Cost per Accepted Outcome

**North Star:** CAPPO (Cost per Accepted Outcome) — total cost divided by accepted, correct resolutions

**Metric Equation:**
```
CAPPO = total_cost / accepted_outcomes
total_cost = inference_cost + retrieval_cost + resolution_overhead + escalation_cost
```

**Product Levers:**
| Lever | How EKO Helps |
|-------|---------------|
| Inference cost | `on_unknown=abstain` prevents expensive clarification loops |
| Retrieval cost | Release registry lookup vs. per-query vector search |
| Resolution overhead | Declarative applicability vs. open-ended reasoning |
| Escalation cost | Declared escalation paths reduce gray-area escalations |

**Counter-Metrics:**
| Counter-Metric | Why It Matters |
|----------------|----------------|
| Resolution latency | Binding and resolution vs. direct retrieval |
| Registry operational cost | Release management overhead vs. document updates |
| False positive cost | Incorrect resolution accepted by user |

**Before EKO:** MTTR included "what does policy say?" research time; knowledge update latency unclear.

**After EKO:** MTTR reduced by declarative applicability; EKO release separates document ingestion from system behavior change. However, **resolution and binding add latency**—the tradeoff is deliberate overhead for traceability.

---

## The EKO-Architecture Integration Map

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EKO × AGENTIC ARCHITECTURE                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  AGENTIC CONCEPT          │         EKO COUNTERPART                 │
│  ────────────────────────│         ─────────────────────            │
│                              │                                        │
│  Agent = (Prompt + Tools +  │  Knowledge = governed release         │
│           Memory) × LLM     │  = (profile + evidence + IR) × runtime│
│                              │                                        │
│  Shared task state          │  Composite release envelope           │
│  - goal, plan, step_status  │  - component refs, digests, resolution │
│                              │                                        │
│  Typed contracts            │  Profile-specific schemas              │
│  - input, output, tools     │  - claim vs policy vs procedure        │
│                              │                                        │
│  Orchestrator               │  Deterministic resolver               │
│  - routes to agents         │  - selects applicable EKO release     │
│                              │                                        │
│  Tool layer                 │  Action contract layer                │
│  - capability invocation    │  - declared capabilities, IAM grants  │
│                              │                                        │
│  HITL gates                 │  Approval gates in Rule IR            │
│  - human approval           │  - human | automated | hybrid         │
│                              │                                        │
│  Termination conditions     │  Terminal states in Rule IR            │
│  - budget, max steps        │  - success, refuse, escalate, error   │
│                              │                                        │
│  Golden sets                │  EKO test fixtures                     │
│  - representative tasks     │  - fixture types: happy_path, edge_case│
│                              │                                        │
│  Trace ID / Conversation ID │  EKO release digest + interpreter pin  │
│  - observability            │  - reproducibility across releases    │
│                              │                                        │
│  Seven-layer chain          │  EKO sits at Retrieval/Tools           │
│  - failure localization    │  - transforms knowledge into code     │
│                              │                                        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## New Anti-Patterns EKO Eliminates

| Anti-Pattern | EKO Elimination |
|--------------|-----------------|
| "The model knows our policy" | Policy is in governed EKO release, not model weights |
| "We retrieved the right document, why did the answer fail?" | EKO separates retrieval (claim) from execution (policy IR) |
| "Which version of the policy was that?" | Release versioning and content digests make this unambiguous |
| "Who approved this behavior?" | Attestation field in EKO envelope names authority |
| "What happens when two policies conflict?" | Resolution profile explicitly declares precedence and conflict handling |
| "The agent escalated because it wasn't sure" | `on_unknown` behavior makes escalation deterministic and traceable |
| "We can't audit why that decision was made" | Rule IR execution trace shows every state transition |
| "The knowledge changed, when did the system update?" | Release deployment is separate from document ingestion |

---

## Risk-Calibrated UX

EKO enables different interaction patterns based on consequence and recoverability. Not all knowledge-derived outputs should be treated alike.

| Stakes | Example | UX Pattern | EKO Role |
|--------|---------|------------|----------|
| Low | Help-center lookup, reference information | Low friction; quick undo or edit acceptable | Claim profile; citation sufficient |
| Medium | Policy advice, procedural guidance | Preview before act; editable draft; clear accept/edit/cancel | Procedure profile with behavioral conduct |
| High | Customer refunds, account changes, regulated actions | Explicit confirmation, citations, policy checks, human review | Policy profile with HITL at action gates |
| Critical | Medical diagnosis, safety-critical decisions | AI as decision support only; human co-sign required | Policy profile; escalation paths; terminal states |

**Required trust patterns for EKO-powered systems:**

- **Visible AI involvement:** Never hide that an EKO release produced or transformed the output
- **Draft, not done:** Generative output from EKO is a suggestion until policy gate accepts it
- **Verifiable evidence:** Cite source EKO release, article/section, and fact bindings
- **Confidence and alternatives:** Expose `on_unknown` outcomes where they change user behavior
- **Graceful fallback:** EKO refusal (unknown/indeterminate) produces a useful next step, not a dead end

---

## Implementation Implications

### For the Orchestrator

The orchestrator gains a new **pre-execution phase**:

1. **Resolution Phase:** Run the resolution profile to select applicable EKO releases
2. **Fact Acquisition:** Bind required facts from declared sources
3. **Validation:** Check that all `on_unknown` conditions are satisfied
4. **Execution:** Only then instantiate the Rule IR state machine

### For the Tool Layer

Tools are no longer "whatever the model decides to call." Tools must be:

- Declared in an `action_contract` EKO (permits intended capability)
- Bound to specific EKO releases via capability references
- Granted via IAM at link-time, not prompt-time
- Validated against preconditions and postconditions

**Critical distinction:** An `action_contract` **declares** permitted intent—it does not itself grant or enforce authority. A capability broker, IAM system, network controls, and runtime policy enforcement are required to actually grant and enforce the capability. EKO says "a refund may be issued"; IAM decides whether this specific agent, for this specific user, in this specific context, is allowed to issue one.

### For Evaluation

Golden sets expand to include:

- EKO release version under test
- Fact binding scenarios (including unknown/stale/missing)
- Resolution profile conflicts
- Rule IR branch coverage

### For Observability

Trace spans gain new attributes:

```json
{
  "trace_id": "...",
  "eko_release": "policy@1.0.0",
  "eko_digest": "sha256:...",
  "interpreter_version": "eko-runtime@2.1.0",
  "resolution_profile": "flra-precedence@1.0.0",
  "fact_bindings_satisfied": {...},
  "rule_ir_path": ["check_eligibility", "calculate_rate", "approve_2x"]
}
```

---

## Rollout and Evaluation Discipline

EKO introduces new rollout risks and requires dedicated evaluation practices beyond standard agent testing.

### Rollout Strategy

```
Internal → Shadow → Canary (by tenant/jurisdiction) → Gradual Rollout → Full Launch
```

| Stage | What Can Go Wrong | Required Control |
|-------|-------------------|------------------|
| Internal | EKO release conflicts, resolver bugs | Fast iteration; test fixture validation |
| Shadow | Silent resolution failures | Logging, offline scoring, human review |
| Canary | Wrong policy selected in production | Kill switch, rollback by release digest |
| Gradual rollout | Tenant-specific resolution failures | Cohort metrics, budget caps, alerting |
| Full launch | System-wide resolution failure | SLOs, incident process, ongoing evals |

### Evaluation Requirements

**Golden sets must include:**
- Versioned EKO release under test
- Fact binding scenarios (unknown, stale, missing, conflicting)
- Resolution profile conflicts and precedence cases
- Rule IR branch coverage (all states and transitions)
- `on_unknown` behavior verification

**Test fixture types:**
- `happy_path` — Expected state transitions and outcomes
- `edge_case` — Boundary conditions (timeouts, thresholds)
- `unknown_fact` — Missing information triggers correct `on_unknown` path
- `conflict` — Overlapping policies resolve per resolution profile
- `stale_evidence` — Freshness thresholds enforced correctly

### Monitoring

**Track these signals in production:**
- False escalations (escalated when answer was available)
- False refusals (refused when policy should have applied)
- Decision defects (wrong policy selected or applied incorrectly)
- Resolver latency (resolution overhead vs. direct retrieval)
- Release adoption rate (which EKO versions are active)

**Rollback by release digest:** If an EKO release is found defective, rollback means re-deploying the previous pinned digest—not reverting document ingestion.

---

## The Bottom Line

**EKO is the missing "knowledge discipline" layer in agentic architecture.**

Where conventional practice treats knowledge as an input to be retrieved, EKO treats knowledge as a governed, testable, releasable artifact that deserves the same engineering rigor as application code.

**Key impact statement:**

> **Before EKO:** "We built an agent that retrieves policies and applies them. Sometimes it works, sometimes we're not sure which policy version it used, and escalations happen when the model gets confused."
>
> **After EKO:** "We built an agent that loads governed EKO releases, validates against resolution profiles, binds facts declaratively, and executes Rule IR under a pinned interpreter. Every policy decision is traceable to a specific release version, attested authority, and execution path."

**The shift is from "knowledge as content" to "knowledge as instruction"—and that shift changes everything about how we design, build, test, deploy, and operate agentic systems.**
