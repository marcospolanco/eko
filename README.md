# EKO: Executable Knowledge Objects

EKO is a proposal for treating enterprise knowledge used by AI systems as a **governed, testable, executable artifact**, not a document library or a retrieved chunk.

> **Status:** Experimental reference design. This repository holds draft schemas, examples, and explanatory media. It does **not** yet include a validated reference runtime or empirical results.

---

## The core insight

**The model is the interpreter; the knowledge is the program.**

For seventy years, engineering discipline followed the instruction side of the von Neumann line: versioning, review, tests, deploy, rollback. Generative AI moved the danger. In LLM-based systems, what the agent *does* is determined less by application source code than by the policies, procedures, and organizational knowledge it retrieves and reasons over at runtime.

If knowledge is the program, it needs the discipline of a program.

---

## What is knowledge? (EKO's answer)

> **Knowledge is governed, context-sensitive information that connects evidence about the world to correct behavior within a defined scope.**

That means ownership and versioning, declared applicability, evidence and provenance, testable behavior, and explicit boundaries (ask, abstain, escalate). A fact alone is not enough. Truth and authority are different accountabilities, which is why EKO uses typed profiles rather than one mushy “knowledge object.”

## Review this take-home in 15 minutes

This repository answers two questions: **what is knowledge?** and **what schema makes that answer operational?** Follow this path:

1. **Two minutes:** Read the axiom above and the [execution boundary](#execution-boundary).
2. **Five minutes:** Read the [three schema files](#three-schema-files), starting with [`schemas/README.md`](schemas/README.md) for the contract they do and do not express.
3. **Eight minutes:** Follow the [Seaway CBA example](examples/seaway_cba/README.md): inspect the [`overtime policy`](examples/seaway_cba/overtime-rules.policy.json), its [`time-and-a-half fixture`](examples/seaway_cba/tests/time-and-half.fixture.json), and the [`composite release`](examples/seaway_cba/seaway-cba.composite.json) that pins the family together.

### Submission boundary

This submission specifies the governed artifact and its validation contract. It does **not** claim a production runtime, real-world approval, or demonstrated business impact. The fixtures are executable specifications for a future pinned interpreter, not evidence that an interpreter already exists.

---

## Explore further

### 1. Read the learning path

**[`eko-learning-path.md`](eko-learning-path.md)** is the progressive tutorial for this repository.

It walks from the premise above through the axiom, five profiles, the three schema files, releases, Rule IR, fact bindings, capabilities, resolution, and the model-authority boundary, then grounds it in the Seaway CBA example. Use it to learn the architecture or to explain it to someone else.

### 2. See the concrete example

[`examples/seaway_cba/`](examples/seaway_cba/): a Collective Bargaining Agreement expressed as claim, policy, procedure, action contract, resolution profile, and composite release, with test fixtures.

### 3. Watch the optional orientation

[![Watch the EKO walkthrough](https://img.youtube.com/vi/QaH7BSplEnY/maxresdefault.jpg)](https://youtu.be/QaH7BSplEnY)

[Watch the walkthrough on YouTube](https://youtu.be/QaH7BSplEnY) · [Media notes](media/README.md)

---

## The idea in one diagram

[![The evolution of enterprise knowledge from documents to executable behavior](media/Evolution_of_Enterprise_Knowledge.png)](media/Evolution_of_Enterprise_Knowledge.png)

*Concept map of the EKO thesis. Click for full resolution.*

### Execution boundary

```text
governed EKO release
  + verified runtime facts
  + externally granted capabilities
  + pinned interpreter
  → compile / link / instantiate
  → bounded execution
  → durable trace
```

The language model may help at authoring and presentation boundaries. It does **not** become the source of policy authority, capability grants, or release approval.

### Five profiles (graded rigor)

| Profile | Role |
|---|---|
| `claim` | Empirical assertion with evidence |
| `policy` | Normative rule enacted by authority |
| `procedure` | Declared sequence toward an outcome |
| `action_contract` | Capability / tool binding (declared, never self-granted) |
| `composite` | Release envelope that pins compatible components |

### Three schema files

| File | Job |
|---|---|
| [`schemas/eko.schema.json`](schemas/eko.schema.json) | What is released (typed knowledge + envelope) |
| [`schemas/rule-ir.schema.json`](schemas/rule-ir.schema.json) | How domain control flow is declared (no eval) |
| [`schemas/resolution-profile.schema.json`](schemas/resolution-profile.schema.json) | Which release wins when many apply |

Details and rationale: [`eko-learning-path.md`](eko-learning-path.md) · schema index: [`schemas/README.md`](schemas/README.md)

---

## Repository map

| Path | What it contains |
|---|---|
| [`eko-learning-path.md`](eko-learning-path.md) | Progressive architecture tutorial (start here for “why”) |
| [`schemas/`](schemas/) | Draft JSON Schemas (v0.1.0) |
| [`examples/`](examples/) | Progressive examples, including Seaway CBA |
| [`media/`](media/) | Video and related explanatory artifacts |

Working drafts and source material live under [`wip/`](wip/); treat them as quarry, not as the public surface.

---

## What remains to be proven

- A pinned interpreter and formal predicate language are needed before the behavioral fixtures can be executed as runtime tests.
- Real-world release approvals and cryptographic attestations are intentionally outside this illustrative conversion.
- The practical advantage over existing RAG and policy-as-code approaches, and the organizational adoption cost, require empirical evidence.

The project is designed to support **proceed**, **narrow**, **revise**, or **stop** as evidence accumulates.
