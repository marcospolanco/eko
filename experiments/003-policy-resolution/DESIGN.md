# Experiment 003: Policy resolution

Status: planned; protocol draft; no results

Role in project: conditional Phase 4b conformance and evaluation study

## 1. Research question

Can a deterministic EKO resolver produce correct, stable, and explainable outcomes across overlapping policy releases, temporal changes, unknown facts, authority constraints, and governed precedence profiles?

## 2. Claim under test

Policy applicability and precedence should be resolved against the complete authoritative candidate set using pinned semantics, not by retrieval order or model judgment. The resolver should return `resolved`, `not_applicable`, `indeterminate`, or `conflict` without silently breaking ties or treating unknown as false.

This study tests the proposed resolution mechanism. It does not establish that any particular organization's precedence policy is substantively correct.

## 3. Required properties

### Correctness

For fixtures with an adjudicated expected outcome, the resolver returns the expected selected release set or failure state.

### Determinism

The same registry snapshot, trusted invocation context, fact observations, resolution profile, and resolver build always produce the same outcome and trace.

### Order invariance

Permuting candidate release order, retrieval order, or serialization order does not change the result unless order is an explicit governed input.

### Unknown preservation

A missing, denied, stale, invalid, or unverifiable applicability fact is not coerced to false. If it could alter the combined result, resolution is `indeterminate`.

### Authority safety

Unsigned, revoked, out-of-scope, or invalidly delegated issuers cannot win precedence or expand authority.

### Failure explicitness

Equal-precedence or otherwise unresolved applicable policies return `conflict`. Invalid semantics, incomplete authoritative search, or unresolved consequential facts return `indeterminate`.

### Replayability

The trace pins the registry snapshot, candidates, fact acquisitions, applicable set, precedence operations, selected set, semantics versions, and outcome.

## 4. Hypotheses

- `H1`: The EKO resolver reaches 100% expected-outcome agreement on the frozen conformance suite.
- `H2`: Candidate and retrieval order permutations produce zero outcome changes.
- `H3`: The resolver detects consequential unknown and conflict cases more reliably than naive last-write-wins, retrieval-first, and model-selected baselines.
- `H4`: Independent reviewers can identify the decisive applicability and precedence steps from the trace more accurately than from baseline explanations.
- `H5`: Generated and metamorphic testing expose no unsafe false-resolution case after the implementation is frozen for the confirmatory run.

For safety properties, a single confirmed high-severity false resolution is a blocker, not a tolerable average error.

## 5. Unit of analysis

The primary unit is a **resolution case**:

- Authoritative registry snapshot.
- Trusted static invocation envelope.
- Candidate EKO releases and attestations.
- Applicability fact-binding contracts.
- Authorized fact observations with freshness and verification state.
- Pinned resolution profile and parent authority chain.
- Effective and record times.
- Expected resolution outcome and decisive rationale.

A fixture family may generate many related cases, but splits and reporting must preserve the family relationship.

## 6. Fixture matrix

Build hand-authored and generated cases across these dimensions:

### Scope and applicability

- Tenant, domain, product, channel, audience, requested action, and jurisdiction.
- Static scope mismatch.
- Dynamic applicability true, false, or unknown.
- Shared, compatible, and incompatible fact-binding requests.
- Denied, stale, malformed, or missing observations.

### Time

- Future, active, expired, superseded, and withdrawn releases.
- Different valid time and record time.
- Backdated corrections and late-arriving knowledge.
- Registry snapshots taken before and after deployment events.

### Authority

- Valid issuer and delegation chain.
- Out-of-scope delegation.
- Revoked or expired attestation.
- Missing parent profile or trust anchor.
- Child profile attempting to expand authority or weaken mandatory refusal.

### Precedence

- Explicit override.
- Authority rank.
- Specificity.
- Earlier or later valid-from time where the profile permits it.
- `deny_overrides`, `permit_overrides`, `first_applicable`, `only_one_applicable`, and `exactly_one_outcome` profiles.
- Equal precedence requiring conflict.
- A lower-ranked but more specific release under different governed comparator orders.

### Completeness

- Complete authoritative partition.
- Missing registry partition or unverifiable snapshot.
- Retrieved subset that omits an active release.
- Duplicate and semantically equivalent releases.

## 7. Suite composition

### Hand-authored conformance suite

At least 120 small cases with independently reviewed expected outcomes, including every normative resolver branch and boundary.

### Scenario families

At least 20 realistic policy families derived from `examples/policy-conflict/` and `examples/refund-policy/`, each with temporal revisions and scoped exceptions.

### Generated/property-based suite

Generate at least 10,000 valid and invalid combinations from a grammar constrained by the schemas. Preserve random seeds and minimized counterexamples.

### Metamorphic suite

Apply transformations with expected invariants:

- Permute candidate and serialization order.
- Add an inapplicable release.
- Add a lower-precedence release that cannot change the result.
- Rename non-semantic identifiers consistently.
- Replace a fresh observation with stale or denied status.
- Remove an authoritative partition.
- Narrow a child profile's scope without altering cases outside that scope.

Each transformation declares whether the outcome must remain identical or change to a specific failure state.

## 8. Baselines

### B0: Retrieval-first

Select the highest-ranked retrieved policy without proving registry completeness or deterministic applicability.

### B1: Last-write-wins

Select the latest recorded or versioned applicable policy without a governed precedence profile.

### B2: Model resolution

Provide all candidate prose and facts to a language model and ask it to select the governing policy and explain why.

### T1: EKO resolver

Use the frozen deterministic resolver, pinned release artifacts, three-valued applicability, authority verification, and governed precedence profile.

Baselines illustrate failure modes and explainability tradeoffs; they do not lower the conformance requirement for T1.

## 9. Oracle independence

Avoid testing the implementation against expectations generated by the same implementation.

Use three oracle sources:

1. Independently authored truth tables for small conformance cases.
2. Reviewer-adjudicated expected outcomes for realistic scenario families.
3. An independent reference model or specification-level executable model for generated cases where feasible.

Disagreements among oracle sources are specification findings. Resolve them before confirmatory scoring rather than choosing the result closest to T1.

## 10. Procedure

1. Freeze resolver semantics, outcome vocabulary, profile schema, and trace requirements exercised by the study.
2. Build and independently review the hand-authored suite.
3. Implement baselines and T1 without access to held-out family labels.
4. Run pilot conformance and minimize failures.
5. Freeze the resolver build, reference model, generators, seeds, severity rubric, and analysis.
6. Execute the confirmatory hand-authored, scenario-family, generated, and metamorphic suites.
7. Run each deterministic case at least twice in clean processes to detect hidden state or nondeterminism.
8. Run stochastic B2 across predeclared seeds and report the distribution rather than the best run.
9. Conduct a blinded trace-comprehension study on a stratified subset.
10. Publish all non-sensitive fixtures, generators, seeds, minimized failures, traces, and results.

## 11. Measures

### Primary conformance measures

- Expected-outcome agreement.
- Unsafe false-resolution count and severity.
- Order-invariance violations.
- Unknown-preservation violations.
- Authority-safety violations.
- Conflict and indeterminate detection recall.

### Trace and explanation measures

- Trace completeness against required fields.
- Replay agreement using pinned artifacts.
- Reviewer accuracy identifying decisive release, fact, comparator, and failure reason.
- Reviewer time and confidence.

### Operational measures

- Resolution latency by candidate-set size.
- Fact-acquisition count and deduplication rate.
- Memory use and trace size.
- Performance under large but valid candidate partitions.

Operational performance is secondary to correctness and may not justify weakening a safety property.

## 12. Analysis plan

- Report exact conformance failures, not only aggregate percentages.
- Calculate confidence intervals for baseline comparisons and human trace comprehension.
- For generated suites, report coverage by rule, branch, comparator, outcome, and interaction pair.
- Analyze every unsafe false resolution through a minimized counterexample.
- Report baseline behavior by candidate count, ambiguity, and unknown status.
- Compare trace comprehension with paired or mixed-effects analysis on the blinded subset.
- Label throughput and scalability findings as implementation-specific rather than semantic properties.

## 13. Decision gate

- **Proceed:** zero confirmed high-severity false resolutions, complete agreement on the frozen normative conformance suite, no order or authority invariant violations, and adequate trace replay.
- **Narrow:** semantics are sound only for a smaller precedence/profile subset; publish that subset as the supported core.
- **Revise:** oracle disagreements or recurring edge cases reveal an underspecified resolver; revise the specification and rerun a new versioned suite.
- **Stop the deterministic-resolution claim:** the mechanism cannot preserve its invariants without hidden model judgment or impractical registry assumptions.

Performance or explainability gains cannot compensate for an unsafe false resolution.

## 14. Validity threats and mitigations

| Threat | Mitigation |
|---|---|
| Tests mirror implementation structure | Use independent truth tables, external case authors, metamorphic properties, and a reference model. |
| Generated cases are syntactically broad but semantically shallow | Combine generated cases with realistic policy families and interaction coverage. |
| Baseline prompts are weak | Publish prompts and allow preregistered alternatives before held-out execution. |
| “Correct” precedence embeds one domain's norms | Treat precedence profiles as governed inputs and test multiple legitimate regimes. |
| Registry completeness is assumed rather than tested | Include omitted partition, stale snapshot, and unverifiable registry cases. |
| Trace-comprehension ratings favor EKO terminology | Use task-based questions and normalized output presentation. |
| A single implementation is mistaken for a standard | Separate semantic conformance from implementation performance and invite independent implementations. |

## 15. Reproducibility artifacts

When implemented, this directory should add:

```text
003-policy-resolution/
├── DESIGN.md
├── README.md
├── spec-model/
├── conformance/
├── scenarios/
├── generators/
├── baselines/
├── resolver/
├── traces/
├── analysis/
├── results/
└── environment/
```

Every published failure should include a minimal fixture, expected outcome, observed outcome, severity, and disposition.

## 16. Dependencies

- Experiment 001 supports continued investment, or an independent justification is recorded.
- Frozen minimum release, applicability, resolution-profile, and trace schemas.
- Normative three-valued logic and precedence semantics.
- Versioned `examples/policy-conflict/` and `examples/refund-policy/` fixtures.
- An independent oracle or specification model.
- Reviewers familiar with the tested policy domains but blind to condition identity during scoring.
