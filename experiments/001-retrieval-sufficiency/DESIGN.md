# Experiment 001: Retrieval sufficiency

Status: planned; protocol draft; no results

Role in project: Phase 4a canary and investment gate

## 1. Research question

Does EKO-style analysis detect operational knowledge defects that ordinary retrieval-relevance evaluation misses, at an acceptable false-positive rate and review cost?

The experiment tests the practical adoption wedge, not the complete EKO runtime. It evaluates a **shadow EKO**: a non-authoritative structured analysis of retrieved content used to identify missing scope, evidence, authority, behavior, conflict handling, or evaluation.

## 2. Claim under test

For a representative decision task, semantically relevant retrieved text may still be insufficient to justify bounded behavior. An EKO-shaped completeness and consistency analysis should detect more consequential insufficiencies than relevance and groundedness evaluation alone.

This experiment does not test whether EKO can prove that a policy is substantively correct, whether full EKO execution is safe, or whether automatically extracted IR should receive authority.

## 3. Hypotheses

### Primary hypothesis

`H1`: At a matched or predeclared false-positive ceiling, the shadow-EKO condition has higher task-level defect recall than the strongest baseline condition.

`H0`: The shadow-EKO condition does not improve defect recall over the strongest baseline at the allowed false-positive rate.

### Secondary hypotheses

- `H2`: Shadow-EKO sufficiency judgments agree more closely with blinded expert judgments than baseline scores do.
- `H3`: Shadow-EKO analysis improves appropriate abstention on insufficient cases without materially increasing abstention on sufficient cases.
- `H4`: The analysis produces more actionable defect descriptions, measured by blinded reviewer ratings and correction success.
- `H5`: The incremental signal remains useful after accounting for model calls, latency, reviewer time, and manual structuring effort.

## 4. Unit of analysis

The primary unit is a **task packet**:

- A user or agent task.
- A governed source corpus.
- A retrieved context set.
- The intentionally present or absent operational requirements.
- A gold sufficiency judgment.
- Gold defect labels when the packet is insufficient.
- A bounded expected behavior: answer, ask, abstain, refuse, or escalate.

Multiple retrieval runs may be generated for a task packet, but train, development, and test splits must occur at the underlying policy or source-family level to prevent near-duplicate leakage.

## 5. Dataset design

### Domains

Use at least four domains with different authority and action characteristics:

1. Retail returns and refunds.
2. Employee leave or benefits guidance.
3. SaaS account administration and billing.
4. Equipment operations or incident response.

Do not include clinical, legal-advice, or live financial-decision tasks in the first study.

### Source composition

Use two complementary fixture classes:

- **Controlled fixtures:** authored so the exact missing or conflicting element is known.
- **Naturalistic fixtures:** adapted from public or permissioned policy and procedure material, with domain reviewers establishing the gold labels.

Record provenance and licensing for every source. Synthetic text must be labeled as synthetic and must not dominate the confirmatory set.

### Defect taxonomy

Include both single-defect and multi-defect packets:

- Missing or unverifiable authority.
- Missing effective date or stale evidence.
- Undefined or scope-dependent term.
- Missing applicability condition.
- Missing exception.
- Missing required fact or fact source.
- Conflicting policies without precedence.
- Action without prerequisite or authorization requirement.
- Missing unknown, abstention, refusal, or escalation behavior.
- Relevant evidence that remains insufficient to justify the requested action.

Include sufficient controls that contain all required elements. Include hard negative controls where unusual wording looks incomplete but is sufficient for the bounded task.

### Size

- Pilot: approximately 40 task packets across at least two domains.
- Confirmatory target: at least 200 task packets across four or more domains, balanced between sufficient and insufficient outcomes.
- Each major defect class should appear often enough for a useful confidence interval; final counts will be set after the pilot and a power analysis based on paired-condition disagreement rates.

The confirmatory sample size and stopping rule must be frozen before the held-out test set is evaluated.

## 6. Experimental conditions

All automated conditions receive the same task, corpus, retrieved context, and declared tool budget unless a difference is intrinsic to the method and reported.

### B0: Retrieval relevance

Standard lexical or vector retrieval metrics and a relevance judgment. This condition asks whether the retrieved context is about the task.

### B1: Standard RAG evaluation

An established relevance, groundedness, and answer-faithfulness evaluation prompt or framework. It may judge whether an answer is supported by the retrieved text but receives no EKO field structure.

### B2: Strong checklist audit

A domain-neutral LLM audit with an explicit checklist for completeness, exceptions, authority, recency, and escalation. This is the strongest fairness baseline: it tests whether EKO adds value beyond simply asking a model to inspect content more carefully.

### T1: Shadow-EKO analysis

A model-assisted extraction into a typed, non-authoritative shadow representation followed by deterministic completeness and consistency checks where possible. Extraction uncertainty, unmapped spans, and validation failures remain visible rather than being repaired silently.

Report extraction and checking performance separately so that a failed extraction cannot be mistaken for a failed completeness rule, or vice versa.

## 7. Gold labeling

Each confirmatory packet receives independent review from at least two qualified reviewers who are blind to condition outputs.

Reviewers label:

- Whether the retrieved context is sufficient for the bounded task.
- Which defect classes apply.
- The appropriate bounded behavior.
- Which source clauses establish the judgment.
- Severity if the system acted despite the defect.

Disagreements are adjudicated by a third reviewer or documented consensus process. Report raw agreement and an appropriate chance-corrected agreement measure. Packets without an adjudicated gold label do not enter the primary confirmatory analysis.

## 8. Procedure

1. Define the task template, defect taxonomy, reviewer rubric, and output schemas.
2. Build the pilot packets and obtain blind gold labels.
3. Implement B0, B1, B2, and T1 with logging for inputs, model/build identifiers, prompts, costs, and outputs.
4. Run the pilot; inspect only pilot errors.
5. Freeze the treatment rules, baseline prompts, thresholds, metric definitions, sample-size decision, and analysis code.
6. Build or release the held-out confirmatory set without exposing labels to condition authors.
7. Run all conditions on the same packets. Randomize condition order where human rating could be affected by fatigue or anchoring.
8. Have blinded reviewers rate defect-report actionability and bounded behavior.
9. Execute the preregistered analysis before any exploratory subgroup analysis.
10. Publish fixtures permitted by licensing, exact configurations, raw condition outputs, adjudicated labels, analysis, and limitations.

## 9. Measures

### Primary measure

- Task-level insufficiency recall at the predeclared false-positive ceiling on sufficient controls.

### Secondary measures

- Defect-level precision, recall, and macro F1.
- Balanced accuracy and area under the precision-recall curve for sufficiency classification.
- Brier score or another calibration measure when confidence is emitted.
- Appropriate abstention rate on insufficient packets.
- Incorrect abstention rate on sufficient packets.
- Severity-weighted unsafe-action rate.
- Agreement with expert bounded-behavior labels.
- Reviewer-rated actionability of defect reports using a frozen rubric.
- Successful correction rate when a separate editor receives the report.
- Model calls, tokens, latency, compute cost, reviewer time, and manual structuring time.

## 10. Analysis plan

- Use paired comparisons because every condition sees the same task packets.
- Report effect sizes and bootstrap confidence intervals, not only significance tests.
- Use McNemar's test or an appropriate paired model for task-level binary outcomes.
- Use hierarchical or mixed-effects analysis if multiple packets share domains, source families, or reviewers.
- Correct confirmatory secondary comparisons for multiple testing; label all post-hoc analyses exploratory.
- Report performance by domain, defect class, severity, single versus multiple defects, and controlled versus naturalistic source.
- Conduct an ablation of EKO field groups to identify whether any gain comes from a small checklist subset rather than the full structure.

Model-based judging may supplement but never replace expert gold labels. A judge model must not be the same model/configuration used to generate the output in the primary analysis unless that dependence is explicitly tested.

## 11. Decision gate

Freeze numeric thresholds after the pilot and before the confirmatory run. The default decision logic is:

- **Proceed:** T1 shows a practically meaningful, confidence-bounded recall improvement over the strongest baseline without breaching the false-positive ceiling, and the gain survives cost and ablation analysis.
- **Narrow:** the gain is concentrated in particular defect classes or policy-heavy domains.
- **Revise:** a simpler checklist captures most of the benefit, extraction errors dominate, or authoring/review cost is disproportionate.
- **Stop the broader practical claim:** T1 does not outperform the strongest baseline or causes materially more unsafe or unnecessary abstention.

A favorable comparison against B0 alone is not sufficient to proceed.

## 12. Validity threats and mitigations

| Threat | Mitigation |
|---|---|
| Synthetic defects are too easy or tailored to EKO fields | Include naturalistic sources, hard controls, and externally proposed counterexamples. |
| Baselines are deliberately weak | Include B2, match budgets, publish prompts, and invite alternative baselines before test-set execution. |
| Test leakage through similar source material | Split by source family and document model/version knowledge limitations. |
| EKO extraction and EKO checking are conflated | Score extraction fidelity separately and run an oracle-structured ablation. |
| Expert labels encode EKO assumptions | Blind reviewers to conditions and frame labels around bounded task sufficiency rather than EKO terminology. |
| Reviewer subjectivity | Use multiple reviewers, source-clause citations, adjudication, and agreement reporting. |
| High recall is purchased through excessive abstention | Report sufficient-case abstention, severity-weighted action outcomes, and calibration. |
| Results do not generalize across domains | Use multiple domains and label domain-level findings as exploratory until replicated. |

## 13. Reproducibility artifacts

When implemented, this directory should add:

```text
001-retrieval-sufficiency/
├── DESIGN.md
├── README.md
├── fixtures/
│   ├── pilot/
│   └── confirmatory/
├── rubrics/
├── prompts/
├── baselines/
├── src/
├── analysis/
├── results/
└── environment/
```

Raw subject or proprietary data must not be committed. Published fixtures must use public, permissioned, or synthetic material with provenance.

## 14. Dependencies

- A reviewed shadow-EKO field set sufficient for the tested defect taxonomy.
- A minimal validator that preserves extraction uncertainty.
- At least one credible standard RAG evaluation and the strong checklist baseline.
- Domain reviewers and a frozen adjudication rubric.
- Versioned example fixtures from `examples/rag-shadow-audit/` and `examples/minimal-claim/`.

Experiments 002–004 should not receive substantial investment until this experiment produces and passes through its decision gate, unless an independent rationale is recorded.
