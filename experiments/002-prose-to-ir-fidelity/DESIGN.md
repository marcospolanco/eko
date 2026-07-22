# Experiment 002: Prose-to-IR fidelity

Status: planned; protocol draft; no results

Role in project: conditional Phase 4b study

## 1. Research question

Can model-assisted authoring translate controlled policy or procedure prose into reviewable EKO rule IR without silently inventing, omitting, or changing domain behavior?

This experiment evaluates the model as a proposal generator at the authoring boundary. It does not authorize generated IR, test runtime safety, or assume that natural language has one unambiguous formalization.

## 2. Claim under test

A source-mapped, schema-constrained authoring workflow that explicitly reports ambiguity and uncovered clauses should produce safer and more reviewable IR proposals than direct prose-to-IR generation, while reducing correction effort compared with unaided formalization.

## 3. Hypotheses

### Primary hypotheses

- `H1`: The constrained, source-mapped workflow has a lower unsupported-behavior rate than direct generation using the same model family and budget.
- `H2`: The constrained workflow has higher normative clause coverage without lowering scenario-level semantic agreement.

### Secondary hypotheses

- `H3`: The constrained workflow identifies intentionally ambiguous clauses more reliably than direct generation.
- `H4`: Reviewers correct constrained proposals faster and with fewer missed defects than direct proposals.
- `H5`: Deterministic schema and static validation catch a meaningful subset of defects that model self-review misses.
- `H6`: Benefits remain after controlling for document length, domain, branching complexity, and model family.

### Null framing

The constrained workflow does not materially improve fidelity or review efficiency over a simpler direct-generation prompt, or any gain is offset by complexity and review burden.

## 4. Unit of analysis

The primary unit is a **source package**:

- Versioned controlled-natural-language policy or procedure.
- Normative clause segmentation.
- Declared terms and input types.
- Expert-authored reference interpretation or admissible interpretation set.
- Scenario suite with expected outcomes.
- Known ambiguities and intentionally underspecified clauses.
- Gold source-to-IR coverage map.

The same source package is translated under every automated condition. Split by policy family so paraphrases or revisions cannot cross train/development/test boundaries.

## 5. Corpus design

### Domains

Use at least four domains with different rule structures:

1. Retail refunds and exceptions.
2. Employee leave or approval policy.
3. SaaS access, billing, or account recovery.
4. Operations and incident escalation.

Avoid source material whose publication or transformation rights are uncertain.

### Complexity strata

Balance packages across:

- Simple threshold or eligibility rules.
- Nested exceptions.
- Multi-step procedures.
- Approval gates.
- Temporal or jurisdictional conditions.
- Collection predicates such as any/all line items.
- Explicit unknown and escalation paths.
- Conflicting or genuinely ambiguous prose.

### Size

- Pilot: approximately 12 source packages.
- Confirmatory target: at least 60 packages with 20–40 scenario cases per package.
- Final sample size will be based on pilot variance for paired unsupported-behavior and correction-time outcomes.

The held-out corpus, prompts, thresholds, and stopping rule must be frozen before confirmatory generation begins.

## 6. Experimental conditions

Use the same underlying model versions, decoding controls, and maximum resource budget for automated conditions where possible. Record every configuration.

### B0: Human formalization without model assistance

A qualified author produces IR from the source using the schema and documentation. This estimates unaided effort and establishes a human—not infallible—reference condition.

### B1: Direct generation

The model receives the source, schema, and a concise instruction to produce valid IR. It is not required to return clause mappings, ambiguities, or uncovered text.

### B2: Direct generation with model self-review

The model produces IR and then critiques or repairs its own output. This controls for extra inference budget without EKO's source-mapping workflow.

### T1: Source-mapped constrained workflow

The model must produce:

- Proposed typed IR.
- A bidirectional map from every normative source clause to IR elements.
- A list of uncovered clauses.
- A list of IR elements without source support.
- Declared ambiguity candidates with alternative interpretations.
- Type and term assumptions.

Deterministic schema and static validation run after generation. Validation failures are reported, not silently repaired.

### T2: Source-mapped workflow with separate review model

Optional confirmatory condition in which a second model checks mappings and unsupported IR without seeing the first model's hidden reasoning. Use only if budget and preregistration support it; do not add it after viewing held-out results.

## 7. Reference construction

Each source package is independently formalized by at least two qualified annotators. They must be allowed to conclude that the prose has multiple admissible interpretations or cannot be faithfully compiled without clarification.

Reference construction includes:

- Normative versus explanatory clause labels.
- Typed inputs and controlled terms.
- Expected and prohibited transitions.
- Source-to-IR mappings.
- Ambiguity and omission labels.
- Scenario outcomes, including unknown and escalation.

Disagreements are adjudicated before condition outputs are scored. The reference IR must not be authored by the same person who implements T1's prompts or validator rules without independent review.

## 8. Fidelity definitions

### Unsupported behavior

Any condition, transition, action, approval requirement, exception, or outcome present in the generated IR that lacks support in the source or an explicitly approved interpretation.

### Omission

A normative source clause with no behaviorally equivalent representation and no declared uncovered-clause warning.

### Semantic mutation

A represented clause whose operational meaning differs from the source, including reversed conditions, changed thresholds, widened scope, or collapsed unknown behavior.

### Ambiguity handling

An ambiguous clause is handled correctly when the workflow surfaces it with its relevant alternatives or requests clarification. Choosing one interpretation silently is an error even if the chosen interpretation matches one annotator.

## 9. Procedure

1. Freeze the minimum rule IR and authoring documentation exercised by the study.
2. Create and adjudicate pilot source packages and scenario suites.
3. Implement B0, B1, B2, and T1; optionally preregister T2.
4. Run pilot generation and reviewer sessions.
5. Refine instructions using only pilot data.
6. Freeze prompts, schemas, model versions where available, validation rules, reviewer UI, rubrics, sample size, and analysis.
7. Generate all confirmatory outputs with complete logs and immutable identifiers.
8. Randomize and blind output condition labels for expert review.
9. Run deterministic validation and scenario execution against the reference suite.
10. Have reviewers inspect source-to-IR semantic diffs and correct outputs to an approved state.
11. Execute the preregistered analysis, then separately label exploratory findings.
12. Publish permissioned source packages, outputs, mappings, validator diagnostics, reviewer annotations, and analysis.

## 10. Measures

### Primary measures

- Unsupported behavior per 100 normative clauses.
- Normative clause coverage, counting declared uncovered clauses separately from silent omissions.
- Scenario-level semantic agreement with the adjudicated reference outcomes.

### Secondary measures

- Silent omission rate.
- Semantic mutation rate by severity.
- Ambiguity precision and recall.
- Source-map precision and recall.
- Invalid-schema and static-analysis defect rate.
- Reviewer defect-detection recall.
- Time to reach an approved representation.
- Number and type of reviewer edits.
- Reviewer confidence and cognitive-load rating using a frozen instrument.
- Model calls, tokens, latency, and cost.
- Performance by policy complexity, document length, domain, and model family.

No model-only semantic-equivalence score may serve as the primary measure. Use deterministic scenario execution and blinded expert review.

## 11. Analysis plan

- Compare automated conditions within source package using paired or mixed-effects models.
- Treat source package and reviewer as random effects where appropriate.
- Report effect sizes and confidence intervals for fidelity and correction effort.
- Analyze severe unsupported behaviors separately from stylistic or non-behavioral differences.
- Report exact failure examples for threshold changes, scope widening, missing exceptions, and invented actions.
- Conduct an ablation that removes source mapping, ambiguity reporting, or deterministic validation one at a time.
- Analyze whether the workflow improves fidelity because of the structured output, increased token budget, or reviewer interface.
- Correct confirmatory secondary comparisons and label post-hoc analyses exploratory.

## 12. Decision gate

Numeric thresholds will be fixed after the pilot. The design requires the following qualitative logic:

- **Proceed:** T1 materially reduces severe unsupported behavior and silent omissions relative to B1 and B2, preserves or improves scenario agreement, and lowers total correction burden relative to B0 or the direct conditions.
- **Narrow:** benefits hold only for controlled prose, low-complexity policies, or selected profiles.
- **Revise:** mappings help reviewers but generation remains too unreliable for proposal automation, suggesting EKO should use human-authored IR with model-assisted review only.
- **Stop the translation claim:** constrained generation does not improve fidelity, or severe silent invention remains above the preregistered safety ceiling.

Passing schema validation is never considered evidence of semantic fidelity by itself.

## 13. Validity threats and mitigations

| Threat | Mitigation |
|---|---|
| Reference IR is treated as uniquely correct when prose is ambiguous | Permit interpretation sets and indeterminate labels; adjudicate ambiguity rather than forcing consensus. |
| Scenario suites fail to expose semantic differences | Include clause-derived, boundary, adversarial, mutation, and unknown cases. |
| T1 receives more inference budget than baselines | Add B2 and report budget-normalized comparisons. |
| Reviewers recognize the treatment format | Normalize presentation and blind condition labels where possible. |
| Authors of the workflow also score it | Use independent reviewers and publish artifacts for external rescoring. |
| Model/version drift prevents reproduction | Pin exact identifiers when possible, archive prompts/outputs, and rerun a stable open or local model where feasible. |
| Corpus is too clean | Include naturalistic ambiguity, revisions, inconsistent terminology, and externally submitted counterexamples. |
| Human baseline quality varies | Record expertise, training, and time; use repeated or crossed assignments on a subset. |

## 14. Reproducibility artifacts

When implemented, this directory should add:

```text
002-prose-to-ir-fidelity/
├── DESIGN.md
├── README.md
├── corpus/
├── references/
├── scenarios/
├── prompts/
├── outputs/
├── reviewer-ui/
├── validator/
├── analysis/
├── results/
└── environment/
```

Store only public, permissioned, or synthetic source packages. Keep model outputs immutable after generation; corrections should be separate reviewed artifacts.

## 15. Dependencies

- Experiment 001 decision supports continued investment, or an independent justification is recorded.
- A reviewed minimum rule IR and normative execution semantics.
- Versioned `examples/refund-policy/` and `examples/minimal-claim/` fixtures.
- A deterministic schema validator and scenario runner.
- Qualified policy annotators and reviewers.
- A reviewer view that shows source clauses, mappings, generated IR, and behaviorally meaningful diffs.
