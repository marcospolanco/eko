# Schemas

This directory contains the canonical, machine-readable EKO schemas.

**New to this directory?** Read [`../eko-learning-path.md`](../eko-learning-path.md) first: a progressive tutorial on why these three files exist and how they fit together.

> Recommended entrypoint: start with [`../eko-learning-path.md`](../eko-learning-path.md) if you want the narrative before the schema details.

## Schema Files

| File | Purpose | Status |
|------|---------|--------|
| [`eko.schema.json`](eko.schema.json) | Release envelope and typed profiles (claim, policy, procedure, action_contract, composite) | Draft v0.1.0 |
| [`rule-ir.schema.json`](rule-ir.schema.json) | Constrained declarative rule intermediate representation | Draft v0.1.0 |
| [`resolution-profile.schema.json`](resolution-profile.schema.json) | Applicability, conflict resolution, and precedence semantics | Draft v0.1.0 |

## Schema Version

All schemas are currently **draft v0.1.0**. They are subject to change based on:
- Implementation experience
- Experimental results
- Community feedback
- Standardization processes

## Schema Structure

### eko.schema.json

The main EKO schema defines:
- **Release envelope**: identity, version, metadata, attestations
- **Typed profiles**: five component types with distinct accountability
  - `claim`: empirical assertions with evidence
  - `policy`: normative rules enacted by authority
  - `procedure`: stepwise processes with behavioral correctness
  - `action_contract`: capability bindings and tool interfaces
  - `composite`: release envelopes binding compatible components
- **Supporting structures**: evidence, applicability, fact bindings, tests, deployment

### rule-ir.schema.json

The rule IR schema defines:
- **States**: decision, action, approval, terminal, error, escalation
- **Transitions**: conditional state changes with predicates
- **Action references**: capability and procedure invocations (not implementations)
- **Approval gates**: human, automated, or hybrid approval configuration
- **Variables and constants**: for predicate expressions
- **Execution bounds**: timeouts, cycle detection, error handling

### resolution-profile.schema.json

The resolution profile schema defines:
- **Applicability strategy**: static scope, dynamic predicates, or hybrid
- **Precedence**: priority-based, temporal, hierarchical, or composite
- **Conflict resolution**: detection, precedence wins, merge, escalate, or deny all
- **Fact resolution**: source priority, unknown propagation, stale handling
- **Registry configuration**: authoritative EKO registry sources
- **Validation rules**: scope, precedence, and consistency requirements

## Using the Schemas

### Validation

Use any JSON Schema 2020-12 compliant validator:

```bash
# Validate an EKO component from the repository root.
jsonschema -i examples/seaway_cba/overtime-rules.policy.json schemas/eko.schema.json

# Validate its separately governed resolution profile.
jsonschema -i examples/seaway_cba/resolution-profile.json schemas/resolution-profile.schema.json
```

### What the schemas mean

The schemas define a release contract, not an interpreter. Their normative design commitments are:

| Commitment | Consequence |
|---|---|
| Profile selects accountability | A `claim`, `policy`, `procedure`, `action_contract`, and `composite` cannot be treated as interchangeable content. |
| Rule IR is declarative | Rules declare states, transitions, predicates, and action references. They do not embed arbitrary executable code. |
| Facts are declared dependencies | A policy names the runtime facts it needs and its response when a fact is unknown. |
| Capabilities are externally granted | An action contract can name an allowed tool interface; it cannot grant itself authority to use that tool. |
| Resolution is governed separately | Applicability, precedence, conflicts, and stale facts are not left to retrieval order or model preference. |
| Interpretation is pinned | A release identifies the interpreter expected to give the declarations meaning. |

JSON Schema can validate structure and many local constraints. It cannot prove that a predicate has the intended meaning, that source evidence is faithful, that an attestation is genuine, or that an interpreter behaves correctly. Those require a pinned interpreter, review, and external authority.

## Schema Concerns

The schemas address these technical concerns:

1. **Identity and versioning** — stable names, semantic versioning, content addressing
2. **Typed profiles** — distinct accountability for claims, policies, procedures, contracts
3. **Applicability** — explicit scope, jurisdiction, temporal validity, predicates
4. **Evidence and provenance** — sources, authority chains, freshness tracking
5. **Fact bindings** — runtime fact contracts with unknown handling
6. **Rule IR** — constrained declarative representation without arbitrary execution
7. **Behavioral constraints** — explanations, prohibitions, uncertainty, escalation
8. **Action contracts** — capability security without self-granted authority
9. **Governance** — attestations, tests, deployment constraints, rollback targets
10. **Resolution** — applicability, precedence, conflict, and registry semantics

## Limitations

Current limitations and planned extensions:

- **Expression language**: predicates use string expressions; formal syntax to be specified
- **Capability format**: capability references are strings; formal schema pending
- **Attestation format**: signatures are strings only; a cryptographic format is TBD
- **Test fixture format**: test structure defined; assertion language TBD
- **Compensation semantics**: compensation declared; detailed execution semantics TBD

## Contributing

Schema changes require:
- Clear rationale for change
- Updated test fixtures
- Documentation updates
- Version bump per semver

For major schema changes, propose via issue with:
- Problem statement
- Proposed change
- Impact analysis
- Migration path
