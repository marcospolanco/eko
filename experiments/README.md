# Experiments

Protocol drafts for reproducible experiments that can support, narrow, or falsify EKO's claims.

## Status

**All experiments are design documents only.** No runner, fixture corpus, empirical result, or implementation has been created yet. These are protocols, not findings.

## Dependency structure

Experiments must be executed in order. Later studies depend on earlier decision gates:

```
001 (investment gate) → 002 → 003 → 004
                         └─────┴─────┘
                               └───→ (parallel)
```

- **001** is the Phase 4a canary and investment gate for the entire practical thesis
- **002–004** are conditional Phase 4b studies that proceed only if their prerequisites pass

If 001 fails to show meaningful improvement over strong baselines at acceptable cost, the broader practical claim must be narrowed or stopped before investing in 002–004.

## Experiment catalog

### [001: Retrieval sufficiency](001-retrieval-sufficiency/DESIGN.md) — *Phase 4a investment gate*

**Question:** Does EKO-style analysis detect operational knowledge defects that ordinary retrieval-relevance evaluation misses, at acceptable false-positive rate and review cost?

**Why it matters:** This is the practical adoption wedge. If shadow-EKO cannot identify missing scope, evidence, authority, behavior, or conflict handling that relevance metrics miss, the broader thesis cannot proceed.

**Design:** Shadow-mode completeness and consistency analysis on retrieved content, compared against relevance metrics, standard RAG evaluation, and a strong checklist audit baseline.

**Outcome:** Proceed, narrow, revise, or stop the broader practical claim.

**Key baselines:**
- B0: Retrieval relevance
- B1: Standard RAG evaluation
- B2: Strong checklist audit (fairness baseline)
- T1: Shadow-EKO analysis

---

### [002: Prose-to-IR fidelity](002-prose-to-ir-fidelity/DESIGN.md) — *Conditional Phase 4b*

**Question:** Can model-assisted authoring translate policy prose into reviewable EKO rule IR without silently inventing, omitting, or changing domain behavior?

**Why it matters:** The authoring boundary is where prose becomes executable rules. If constrained generation produces unsupported behavior or silent omissions, EKO cannot safely automate policy translation.

**Design:** Source-mapped, schema-constrained workflow vs. direct generation, measuring unsupported behavior, clause coverage, semantic fidelity, ambiguity handling, and reviewer correction effort.

**Prerequisite:** 001 decision supports continued investment

**Key conditions:**
- B0: Human formalization without model assistance
- B1: Direct generation
- B2: Direct generation with model self-review
- T1: Source-mapped constrained workflow

---

### [003: Policy resolution](003-policy-resolution/DESIGN.md) — *Conditional Phase 4b*

**Question:** Can a deterministic EKO resolver produce correct, stable, and explainable outcomes across overlapping releases, temporal changes, unknown facts, and governed precedence profiles?

**Why it matters:** Policy applicability and precedence must be resolved against the complete authoritative candidate set using pinned semantics—not by retrieval order or model judgment. A single unsafe false resolution is a blocker.

**Design:** Conformance suite with hand-authored, scenario-family, generated, and metamorphic cases testing determinism, order invariance, unknown preservation, authority safety, and trace completeness.

**Prerequisite:** 001 decision supports continued investment

**Key properties:**
- Correctness, determinism, order invariance
- Unknown preservation (not coerced to false)
- Authority safety and failure explicitness
- Replayable traces

---

### [004: Runtime boundaries](004-runtime-boundaries/DESIGN.md) — *Conditional Phase 4b*

**Question:** Does the EKO compile/link/runtime boundary prevent undeclared, unauthorized, or untraceable behavior under adversarial inputs, capability failures, and distributed-system failures?

**Why it matters:** An immutable declarative release, linked to scoped capabilities and executed through a reference-monitor runtime, should constrain behavior more reliably than unconstrained tool-using agents.

**Design:** Adversarial safety study with 14 attack/failure families including prompt injection, capability confusion, grant replay, artifact substitution, tool-contract drift, crashes, timeouts, and partial side effects.

**Prerequisite:** 001 decision supports continued investment

**Key security properties:**
- P1–P10: Declared-action confinement, capability enforcement, approval integrity, artifact integrity, unknown preservation, side-effect discipline, recovery discipline, trace completeness, model non-authority

**Bar:** Zero confirmed high-severity unauthorized side effects in the treatment runtime.

---

## What every experiment specifies

Each DESIGN.md follows a consistent template:

1. **Research question** — What is being tested?
2. **Claim under test** — What specific claim is supported or falsified?
3. **Hypotheses** — Primary and secondary, with null framing
4. **Unit of analysis** — What is counted as one case?
5. **Dataset or fixture design** — Domains, complexity, size
6. **Experimental conditions** — Baselines and treatments
7. **Gold labeling or reference construction** — How truth is established
8. **Procedure** — Step-by-step protocol with pilot and confirmatory phases
9. **Measures** — Primary, secondary, and how they're calculated
10. **Analysis plan** — Statistical methods, corrections, reporting
11. **Decision gate** — Proceed, narrow, revise, or stop logic
12. **Validity threats** — What could go wrong and mitigations
13. **Reproducibility artifacts** — What must be published to rerun
14. **Dependencies** — What must exist before the experiment can run

## Limitations

- No clinical, legal-advice, or live financial-decision tasks in the first studies
- Mock tools and isolated environments only (004 does not establish production security)
- Results apply to the tested domains, defect classes, and threat models
- Model drift, version changes, or prompt adaptations require new validation

## References

Experiments extend versioned example fixtures from:
- `examples/rag-shadow-audit/`
- `examples/minimal-claim/`
- `examples/refund-policy/`
- `examples/compile-link-trace/`
- `examples/policy-conflict/`
