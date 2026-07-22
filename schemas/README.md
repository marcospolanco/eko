# Schemas

This directory contains the canonical, machine-readable EKO schemas.

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
# Using ajv (JavaScript)
ajv validate schemas/eko.schema.json examples/refund-policy/eko.json

# Using jsonschema (Python)
jsonschema -i examples/refund-policy/eko.json schemas/eko.schema.json
```

### Normative Behavior

JSON Schema cannot express all normative behavior. Additional semantics specified in:
- `docs/execution-model.md` — compile, link, execute semantics
- Test fixtures in `tests/schema/` — expected validation outcomes

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
- **Attestation format**: signatures use string placeholders; cryptographic format TBD
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

