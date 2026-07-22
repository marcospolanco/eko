# Generating EKOs: A Universal Guide for Converting PDFs into Executable Knowledge Object Families

## 1. Overview & Purpose

This guide defines the **standardized methodology** for transforming static documents (PDF contracts, policy manuals, regulatory frameworks, SOPs, and collective bargaining agreements) into canonical, machine-readable **EKO Families**.

By following this templated approach, any organization can convert unstructured prose into governed, testable, and auditable artifacts that conform strictly to the EKO JSON Schemas in [`schemas/`](file:///Users/marcos/Downloads/playground/eko/schemas/).

---

## 2. The 5-Step PDF-to-EKO Conversion Workflow

```text
┌──────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
│  1. PDF Document │ ──► │ 2. Clause Analysis   │ ──► │ 3. Profile Mapping   │
│     Decomposition│     │    & Fact Binding    │     │    & Schema Authoring│
└──────────────────┘     └──────────────────────┘     └──────────────────────┘
                                                                 │
┌──────────────────┐     ┌──────────────────────┐                │
│  5. Validation & │ ◄── │ 4. Test Suite &      │ ◄──────────────┘
│     Release Manifest   │    Guardrail Check   │
└──────────────────┘     └──────────────────────┘
```

### Step 1: Document Decomposition & Classification
Extract text and segment the PDF into logical domain components. Classify each clause or section into one of the 5 EKO profiles:

| Clause Type in PDF | EKO Profile | Schema Target | Description |
| :--- | :--- | :--- | :--- |
| **Definitions, Scope, Legal Claims** | `claim` | [`schemas/eko.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/eko.schema.json) | Empirical assertions supported by evidence, citations, and verification methods. |
| **Rules, Thresholds, Multipliers** | `policy` | [`schemas/eko.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/eko.schema.json) | Normative rules enacted by authority, incorporating declarative Rule IR logic ([`rule-ir.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/rule-ir.schema.json)). |
| **Multi-step Workflows, Deadlines** | `procedure` | [`schemas/eko.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/eko.schema.json) | Sequential steps toward an outcome, including step conditions and compensation rollbacks. |
| **Tool Actions, System Effects** | `action_contract` | [`schemas/eko.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/eko.schema.json) | Bounded tool capability interfaces, parameters, idempotency, and side effects. |
| **Authority & Scope Precedence** | *Resolution Profile* | [`schemas/resolution-profile.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/resolution-profile.schema.json) | Precedence ordering, scope hierarchy, temporal validity, and conflict resolution rules. |
| **Master Document Package** | `composite` | [`schemas/eko.schema.json`](file:///Users/marcos/Downloads/playground/eko/schemas/eko.schema.json) | Top-level release envelope pinning compatible versions of all components with content digests. |

---

### Step 2: Standard Repository Directory Structure
Organize each converted PDF domain under `examples/<category>/<domain_name>/`:

```text
examples/<category>/<domain_name>/
├── README.md                            # Comprehensive reference documentation and validation guide
├── plan.md                              # Domain-specific conversion plan & audit traceability matrix
├── <domain>.composite.json              # Profile: composite (pins all components with real SHA256 digests)
├── <component-1>.claim.json            # Profile: claim
├── <component-2>.policy.json           # Profile: policy (includes rule_ir)
├── <component-3>.procedure.json        # Profile: procedure (step sequence & compensation)
├── <component-4>.contract.json         # Profile: action_contract (capability security)
├── resolution-profile.json              # Resolution profile (precedence & conflict handling)
└── tests/                               # Test fixture suite
    ├── happy-path-1.fixture.json
    ├── happy-path-2.fixture.json
    ├── edge-case-deadline.fixture.json
    ├── edge-case-stale-evidence.fixture.json
    └── unknown-fact-abstain.fixture.json
```

---

### Step 3: Mandatory Production Quality Guardrails (A+ Reference Standard)

Every generated EKO component MUST comply with these 5 mandatory quality rules:

> [!IMPORTANT]
> 1. **Real Cryptographic SHA256 Content Digests:** Composite release manifests (`*.composite.json`) MUST use actual SHA256 hashes generated via `shasum -a 256 <file>`. Never use placeholder strings.
> 2. **Universal Interpreter Pinning:** Every component MUST include an `interpreter` pin block:
>    ```json
>    "interpreter": { "name": "eko-ir", "version": "v0.2.0" }
>    ```
> 3. **Explicit Behavioral Conduct Constraints (`behavioral_conduct`):** Policy and procedure components MUST include behavioral conduct:
>    * `required_explanations`: Human-readable explanation templates for decision outcomes.
>    * `prohibited_conduct`: Explicitly forbidden AI agent or manager behaviors.
>    * `uncertainty_handling`: Handling mode (`explicit`, `fail_closed`).
>    * `escalation_paths`: Target roles and urgency levels (`low`, `medium`, `high`, `critical`).
> 4. **Multi-Party Attestations & Signatures (`attestations`):** All components MUST include an `attestations` array representing institutional sign-offs, timestamps, and signature placeholders.
> 5. **Deployment & Rollout Configuration (`deployment`):** Active policies/procedures MUST specify target environments (`production`), rollout strategy (`canary`), rollback targets (`eko@version`), and monitoring alerts.

---

### Step 4: Constructing a Comprehensive Test Suite

Place at least **5 to 7 test fixtures** under `tests/` to verify execution:

**Minimum Fixture Coverage by Type:**

| Test Type | Minimum Count | Coverage Goal |
|-----------|---------------|---------------|
| `happy_path` | 2 | Standard success cases, all major outcomes |
| `edge_case` | 2 | Deadlines, thresholds, exclusions, boundaries |
| `unknown_fact` | 1 | Null values trigger abstention/refusal/ask |
| `stale_evidence` | 1 | Freshness violations, expired evidence |
| `conflict_precedence` | 1 | Competing policies, temporal ordering |

1. **Happy Path Fixtures:** Verify standard inputs produce expected states, outputs, and rate calculations.
2. **Edge Case Fixtures:**
   * Expired timelines (e.g. `days_since_incident > deadline` → `EXPIRED`).
   * Stale evidence (e.g. `evidence_age > freshness_days` → `STALE_EVIDENCE`).
   * Threshold boundaries (e.g. `hours_worked_today == 8.0` → exact boundary).
3. **Unknown Fact Fixtures:** Verify that null or missing facts trigger explicit abstention or refusal (`on_unknown: "refuse"` / `"ask"` / `"escalate"`) rather than hallucinated fallbacks.
4. **Conflict & Precedence Fixtures:** Verify overlapping policies resolve via precedence rules, temporal ordering, or escalation.

---

### Step 5: Programmatic Validation & Documentation

Validate all output files against the canonical schemas.

**Executable Validation Script:**

Save this as `validate-eko-family.sh` in your example directory:

```bash
#!/bin/bash
# validate-eko-family.sh
# Validate all EKO components against canonical schemas

set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
SCHEMA_DIR="$SCRIPT_DIR/../../../schemas"

echo "🔍 Validating EKO components against canonical schemas..."

# Track validation results
PASS=0
FAIL=0

# Validate each JSON file against appropriate schema
for file in "$SCRIPT_DIR"/*.json; do
    filename=$(basename "$file")
    
    # Skip validation script itself if present as .json
    if [[ "$filename" == *"validate"* ]]; then
        continue
    fi
    
    echo "  Checking $filename..."
    
    # Determine which schema to use
    if [[ "$filename" == *"resolution"* ]]; then
        schema="$SCHEMA_DIR/resolution-profile.schema.json"
    else
        schema="$SCHEMA_DIR/eko.schema.json"
    fi
    
    # Validate using ajv (preferred) or python
    if command -v ajv &> /dev/null; then
        if ajv validate -s "$schema" -d "$file" 2>/dev/null; then
            echo "    ✅ $filename PASSED"
            ((PASS++))
        else
            echo "    ❌ $filename FAILED"
            ((FAIL++))
        fi
    else
        # Fallback to python jsonschema
        python3 -c "
import json, jsonschema, sys
from pathlib import Path

schema = json.loads(Path('$schema').read_text())
instance = json.loads(Path('$file').read_text())

try:
    jsonschema.validate(instance, schema)
    sys.exit(0)
except jsonschema.ValidationError as e:
    print(f'    ❌ Validation failed: {e.message}')
    sys.exit(1)
" && ((PASS++)) || ((FAIL++))
    fi
done

echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "Validation Results:"
echo "  ✅ Passed: $PASS"
echo "  ❌ Failed: $FAIL"

if [ $FAIL -eq 0 ]; then
    echo ""
    echo "🎉 All components validated successfully!"
    exit 0
else
    echo ""
    echo "⚠️  Some components failed validation. Please fix errors above."
    exit 1
fi
```

**Usage:**
```bash
chmod +x validate-eko-family.sh
./validate-eko-family.sh
```

Create a domain-specific `README.md` containing:
* Component overview table with validation status
* CLI validation instructions
* Complete test fixture catalog
* Architecture & behavioral conduct highlights
* Workflow diagrams (Mermaid.js)

---

## 3. Generic Component Templates

### Template 1: Claim Component (`*.claim.json`)
```json
{
  "$schema": "https://eko.dev/schemas/eko.schema.json",
  "eko": "<domain>-<claim_name>-claim",
  "version": "1.0.0",
  "profile": "claim",
  "metadata": {
    "created_at": "<ISO-TIMESTAMP>",
    "owner": "<OWNING-AUTHORITY>",
    "description": "<CLAIM-DESCRIPTION>"
  },
  "interpreter": { "name": "eko-ir", "version": "v0.2.0" },
  "resolution_profile": "<RESOLUTION-PROFILE-ID>",
  "component": {
    "claim": {
      "statement": "<SUBJECT>.<PREDICATE> == <VALUE>",
      "subject": "<SUBJECT>",
      "predicate": "<PREDICATE>",
      "value": "<VALUE>"
    },
    "evidence": {
      "source": "<PDF-NAME> (Article X, Section Y)",
      "authority": "<AUTHORITY-NAME>",
      "retrieved_at": "<ISO-TIMESTAMP>",
      "freshness_days": 365,
      "confidence": "high"
    },
    "verification_method": "system_queried",
    "falsifiable": true
  },
  "attestations": [
    {
      "type": "approval",
      "authority": "<AUTHORITY-NAME>",
      "timestamp": "<ISO-TIMESTAMP>",
      "signature": "sig_<HASH>"
    }
  ]
}
```

### Template 2: Policy Component (`*.policy.json`)
```json
{
  "$schema": "https://eko.dev/schemas/eko.schema.json",
  "eko": "<domain>-<policy_name>-policy",
  "version": "1.0.0",
  "profile": "policy",
  "metadata": {
    "created_at": "<ISO-TIMESTAMP>",
    "owner": "<OWNING-AUTHORITY>",
    "description": "<POLICY-DESCRIPTION>"
  },
  "interpreter": { "name": "eko-ir", "version": "v0.2.0" },
  "resolution_profile": "<RESOLUTION-PROFILE-ID>",
  "component": {
    "policy": {
      "id": "<POLICY-ID>",
      "authority": "<AUTHORITY-NAME>",
      "effective": "<YYYY-MM-DD>",
      "jurisdiction": ["<JURISDICTION-CODE>"]
    },
    "evidence": {
      "source": "<PDF-NAME> (Section X)",
      "authority": "<AUTHORITY-NAME>",
      "retrieved_at": "<ISO-TIMESTAMP>",
      "freshness_days": 365,
      "confidence": "high"
    },
    "applicability": {
      "audience": ["<TARGET-ROLE>"],
      "jurisdiction": ["<JURISDICTION-CODE>"]
    },
    "fact_bindings": [
      {
        "name": "<FACT-NAME>",
        "source": "<SYSTEM-OF-RECORD-QUERY>",
        "type": "<DATATYPE>",
        "required": true,
        "on_unknown": "escalate",
        "escalation_target": "<ROLE-TARGET>"
      }
    ],
    "rule_ir": {
      "initial_state": "start_node",
      "states": [
        {
          "id": "start_node",
          "description": "<NODE-DESCRIPTION>",
          "type": "decision",
          "transitions": [
            { "condition": "<PREDICATE-EXPRESSION>", "to": "outcome_node" }
          ]
        },
        { "id": "outcome_node", "type": "terminal" }
      ]
    },
    "behavioral_conduct": {
      "required_explanations": [
        { "when": "state == 'outcome_node'", "template": "<EXPLANATION-TEMPLATE>" }
      ],
      "prohibited_conduct": [
        { "action": "<FORBIDDEN-ACTION>", "reason": "<POLICY-REASON>" }
      ],
      "uncertainty_handling": "explicit",
      "escalation_paths": [
        { "condition": "unknown_fact", "target": "<ESCALATION-TARGET>", "urgency": "medium" }
      ]
    }
  },
  "attestations": [
    { "type": "approval", "authority": "<AUTHORITY-NAME>", "timestamp": "<ISO-TIMESTAMP>" }
  ],
  "deployment": {
    "environments": ["production"],
    "rollout_strategy": "canary",
    "canary_percentage": 10,
    "rollback_target": "eko@1.0.0"
  }
}
```

### Template 3: Procedure Component (`*.procedure.json`)
```json
{
  "$schema": "https://eko.dev/schemas/eko.schema.json",
  "eko": "<domain>-<procedure_name>-procedure",
  "version": "1.0.0",
  "profile": "procedure",
  "metadata": {
    "created_at": "<ISO-TIMESTAMP>",
    "owner": "<OWNING-AUTHORITY>",
    "description": "<PROCEDURE-DESCRIPTION>"
  },
  "interpreter": { "name": "eko-ir", "version": "v0.2.0" },
  "resolution_profile": "<RESOLUTION-PROFILE-ID>",
  "component": {
    "procedure": {
      "id": "<PROCEDURE-ID>",
      "outcome": "<INTENDED-OUTCOME>"
    },
    "steps": [
      {
        "order": 1,
        "action": "<STEP-ACTION-NAME>",
        "capability": "<CAPABILITY-IDENTIFIER>",
        "conditions": [
          "<PREDICATE-EXPRESSION>",
          "days_since_<EVENT> <= <DEADLINE>"
        ],
        "on_failure": "stop",
        "compensation": {
          "action": "<COMPENSATION-ACTION>",
          "capability": "<COMPENSATION-CAPABILITY>"
        }
      }
    ],
    "fact_bindings": [
      {
        "name": "<FACT-NAME>",
        "source": "<SYSTEM-OF-RECORD>",
        "type": "<DATATYPE>",
        "required": true,
        "on_unknown": "abstain"
      }
    ],
    "behavioral_conduct": {
      "required_explanations": [
        { "when": "step == 4", "template": "<EXPLANATION-TEMPLATE>" }
      ],
      "prohibited_conduct": [
        { "action": "<FORBIDDEN-ACTION>", "reason": "<PROCEDURAL-REASON>" }
      ],
      "escalation_paths": [
        { "condition": "deadline_exceeded", "target": "<ESCALATION-TARGET>", "urgency": "high" }
      ]
    }
  },
  "attestations": [
    { "type": "approval", "authority": "<AUTHORITY-NAME>", "timestamp": "<ISO-TIMESTAMP>" }
  ],
  "deployment": {
    "environments": ["production"],
    "rollout_strategy": "canary"
  }
}
```

### Template 4: Action Contract Component (`*.contract.json`)
```json
{
  "$schema": "https://eko.dev/schemas/eko.schema.json",
  "eko": "<domain>-<contract_name>-contract",
  "version": "1.0.0",
  "profile": "action_contract",
  "metadata": {
    "created_at": "<ISO-TIMESTAMP>",
    "owner": "<OWNING-AUTHORITY>",
    "description": "<CONTRACT-DESCRIPTION>"
  },
  "interpreter": { "name": "eko-ir", "version": "v0.2.0" },
  "resolution_profile": "<RESOLUTION-PROFILE-ID>",
  "component": {
    "action_contract": {
      "id": "<CONTRACT-ID>",
      "action": "<ACTION-NAME>"
    },
    "interface": "<SYSTEM.INTERFACE.VERSION>",
    "capability_required": "<CAPABILITY-IDENTIFIER>",
    "parameters": [
      {
        "name": "<PARAMETER-NAME>",
        "type": "<DATATYPE>",
        "required": true,
        "constraints": "<CONSTRAINT-EXPRESSION>"
      }
    ],
    "side_effects": [
      {
        "type": "state_change",
        "system": "<TARGET-SYSTEM>",
        "idempotency": "client_supplied_id"
      }
    ],
    "compensation": {
      "on_failure": "notify_<ROLE>",
      "action": "<COMPENSATION-ACTION>"
    }
  },
  "attestations": [
    { "type": "approval", "authority": "<AUTHORITY-NAME>", "timestamp": "<ISO-TIMESTAMP>" }
  ]
}
```

### Template 5: Resolution Profile (`*.resolution-profile.json`)
```json
{
  "$schema": "https://eko.dev/schemas/resolution-profile.schema.json",
  "profile_id": "<PROFILE-ID>",
  "description": "<RESOLUTION-PROFILE-DESCRIPTION>",
  "applicability_strategy": "hybrid",
  "scope_resolution": {
    "hierarchy": ["jurisdiction", "tenant", "audience"],
    "wildcard_behavior": "match_explicit_only",
    "inheritance": {
      "allow_parent_match": true,
      "default_scope": "<DEFAULT-SCOPE>"
    }
  },
  "precedence": {
    "strategy": "composite",
    "temporal": {
      "compare_field": "metadata.effective",
      "direction": "newest_wins",
      "grace_period_seconds": 86400
    },
    "hierarchical": {
      "authority_levels": [
        { "level": 1, "authorities": ["<HIGHEST-AUTHORITY>"] },
        { "level": 2, "authorities": ["<SECONDARY-AUTHORITY>"] }
      ],
      "parent_override": false
    }
  },
  "conflict_resolution": {
    "detection": {
      "check_overlapping_scope": true,
      "check_contradictory_actions": true,
      "check_incompatible_versions": true,
      "unknown_is_conflict": false
    },
    "on_conflict": "precedence_wins",
    "precedence_resolution": {
      "apply_precedence": true,
      "require_clear_winner": true
    }
  }
}
```

---

## 4. Agent Prompts for Each Step

Use these explicit prompts when deploying coding agents for PDF-to-EKO conversion:

### Step 1 Prompt: Document Decomposition & Classification
```
You are converting a PDF document into EKO components. 

TASK: Analyze the PDF at <path/to/document.pdf> and extract all clauses.

For each clause:
1. Extract the full text
2. Identify the article/section number
3. Classify into ONE of these profiles:
   - claim: Definitions, scope, legal certifications, empirical assertions
   - policy: Rules, thresholds, multipliers, conditions
   - procedure: Multi-step workflows, deadlines, escalation paths
   - action_contract: Tool actions, system effects, API calls

OUTPUT FORMAT: Markdown table with columns:
- Article/Section
- Clause Type (claim/policy/procedure/action_contract)
- Extracted Text
- Proposed EKO Component Name
- Evidence Source (article/section reference)
```

### Step 2 Prompt: Profile Mapping & Schema Authoring
```
You are authoring EKO JSON components from classified clauses.

TASK: For each classified clause from Step 1, generate the corresponding EKO JSON component using the templates in Section 3.

REQUIREMENTS for each component:
1. Use the EXACT template structure for the assigned profile
2. Replace ALL <PLACEHOLDER> variables with extracted values
3. Create fact_bindings for ALL dynamic runtime variables:
   - Define source system (e.g., hr.employee.status)
   - Set type (boolean, number, string, datetime)
   - Specify on_unknown behavior (refuse, ask, escalate, abstain)
4. Add evidence linkage:
   - source: PDF name and article/section
   - authority: Enacting body
   - retrieved_at and verified_at timestamps
5. Include behavioral_conduct for policies and procedures:
   - required_explanations for key outcomes
   - prohibited_conduct (forbidden actions)
   - escalation_paths with urgency levels

OUTPUT: One valid JSON file per component, named <name>.<profile>.json
```

### Step 3 Prompt: Test Suite Generation
```
You are generating comprehensive test fixtures for EKO validation.

TASK: Create 5-7 test fixtures under tests/ covering:

REQUIRED FIXTURES (minimum):
1. 2x happy_path: Standard inputs produce expected successful outcomes
2. 2x edge_case: Boundary conditions, deadlines, thresholds, exclusions
3. 1x unknown_fact: null/missing values trigger explicit abstention/refusal
4. 1x stale_evidence: Evidence exceeds freshness_days threshold
5. 1x conflict_precedence: Overlapping policies resolve correctly

Each fixture MUST include:
- name: Descriptive test name
- type: One of (happy_path, edge_case, unknown_fact, stale_evidence, conflict_precedence)
- description: What this test verifies
- fixture: Input facts and context
- expected: Expected outcome (state, status, values)
- assertions: Specific boolean conditions to verify

OUTPUT: Separate .fixture.json file for each test case
```

### Step 4 Prompt: Validation & Guardrail Verification
```
You are validating an EKO family for production readiness.

TASK: Verify all components meet A+ quality standards.

CHECKLIST:
1. Schema Compliance: Every .json validates against its canonical schema
2. Real SHA256 Digests: Composite uses actual digests (not placeholders)
3. Interpreter Pinning: Every component has interpreter block
4. Behavioral Conduct: Policies/procedures include behavioral_conduct
5. Attestations: All components have attestation arrays
6. Deployment Config: Active components specify deployment strategy
7. Test Coverage: At least 5 fixtures covering all required types
8. Evidence Linkage: All claims/policies cite source documents
9. Fact Bindings: All dynamic values have source + on_unknown handler
10. Compatibility: Composite compatibility_matrix constraints satisfied

VALIDATION COMMAND: Run ./validate-eko-family.sh

REPORT FORMAT: Markdown table with columns:
- Check | Status | Notes
- Pass/Fail for each requirement above

FAIL: If any check fails, report specific remediation needed.
```

### Step 5 Prompt: Composite Release & Documentation
```
You are generating the final composite release and documentation.

TASK: Create the master release envelope and README.

COMPOSITE REQUIREMENTS:
1. Include ALL component references with actual SHA256 digests
2. Generate digests via: shasum -a 256 <component-file>
3. Define compatibility_matrix constraints:
   - policy.jurisdiction ⊆ claim.jurisdiction
   - procedure.capabilities ⊆ action_contract.capabilities
   - All components share same resolution_profile
4. Include attestations from all authorities
5. Specify deployment: environments, rollout_strategy, rollback_target

README REQUIREMENTS:
1. Component overview table with validation status
2. Validation instructions (use ./validate-eko-family.sh)
3. Test fixture catalog with coverage types
4. Architecture highlights (behavioral conduct, fact bindings)
5. Workflow diagrams (Mermaid.js format)
6. Exit criteria checklist

OUTPUT: <domain>.composite.json and README.md
```

---

## 5. Error Recovery & Fallback Handling

When failures occur during PDF-to-EKO conversion, use this recovery matrix:

| Failure Mode | Detection Signal | Recovery Action | Fallback |
|--------------|------------------|-----------------|----------|
| **Schema validation fails** | ajv exit code 1 | Fix component error, re-validate | Skip component, flag for manual review |
| **SHA256 digest mismatch** | Composite verification failed | Re-run `shasum -a 256` on changed files | Use placeholder with `FIXME` comment |
| **Missing fact binding** | Test fixture shows undefined variable | Add binding with source and on_unknown | Use on_unknown="escalate" with manual target |
| **Evidence source unclear** | Manual review flag in Step 1 | Add source_uri and authority fields | Use "source: PDF (general)" with low confidence |
| **Behavioral conduct missing** | A+ guardrail check fails | Add required_explanations and prohibited_conduct | Add template: "See policy documentation" |
| **Attestation incomplete** | Authority signature missing | Add attestation with type="approval" | Use "authority: TBD" with review flag |
| **Test fixture fails** | Expected ≠ Actual outcome | Update fixture or fix component logic | Log as known limitation |
| **Compatibility violation** | Composite matrix constraint fails | Adjust component or remove from release | Document incompatibility in README |
| **PDF extraction incomplete** | Missing clauses in Step 1 output | Re-run extraction with manual review | Flag missing sections for manual conversion |

**Error Escalation Paths:**

```
┌─────────────────────────────────────────────────────────────┐
│                     Error Recovery Decision Tree             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Error Detected                                             │
│       │                                                     │
│       ├──► Can fix automatically? ──YES─► Fix, retry        │
│       │                                                     │
│       └──► Requires manual review? ──YES─►                  │
│              │                                               │
│              ├──► Blocks generation? ──YES─► Escalate       │
│              │                                                 │
│              └──► Can document workaround? ──YES─►           │
│                     Add FIXME flag, continue                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 6. Version Migration When Source Documents Change

When source PDFs are updated, follow this migration workflow:

### Migration Triggers

- PDF source document revised (new articles, amended clauses)
- Legal interpretation changes (court rulings, regulatory guidance)
- Technical systems updated (new APIs, changed data sources)
- Organizational structure changes (new authorities, reorganization)

### Migration Process

**Step 1: Change Analysis**
```bash
# Compare old and new PDFs clause-by-clause
diff <old_pdf_clauses.md> <new_pdf_clauses.md> > clause_changes.diff

# Classify changes by impact:
# - NO_CHANGE: Clause unchanged → No migration needed
# - COMPATIBLE_CHANGE: Wording clarified, logic same → Bump patch version
# - INCOMPATIBLE_CHANGE: Logic/behavior changed → Bump major version
```

**Step 2: Component Updates**
```markdown
For each changed clause:

1. Identify affected EKO component(s)
2. Update component JSON with new clause text
3. Preserve unchanged elements (metadata, attestations if valid)
4. Update version per semver:
   - MAJOR: Incompatible behavior change
   - MINOR: Backwards-compatible new feature
   - PATCH: Bug fix, wording clarification
```

**Step 3: Digest Regeneration**
```bash
# Re-generate SHA256 for changed components
shasum -a 256 <changed-component>.json > new_digest.txt

# Update composite with new digests
# Update component version references
```

**Step 4: Test Suite Updates**
```bash
# Re-run test suite
./validate-eko-family.sh

# For each failing test:
- Determine if behavior changed (intentional or bug)
- Update fixture if behavior intentionally changed
- Document migration notes in plan.md
```

**Step 5: Release Documentation**
```markdown
Add to README.md or CHANGELOG.md:

## Version <NEW_VERSION> (<DATE>)
### Changed
- <component-1>: Updated per Article X revision Y
- <component-2>: Clarified threshold per new guidance

### Migration Notes
- Breaking change: Overtime calculation now includes <condition>
- Existing deployments must update fact binding <name>
```

### Migration Compatibility Matrix

| Old Version | New Version | Migration Path | Action Required |
|-------------|-------------|----------------|-----------------|
| 1.0.0 | 1.0.1 | Patch | No action required |
| 1.0.0 | 1.1.0 | Minor | Optional: Review new features |
| 1.0.0 | 2.0.0 | Major | Required: Update composite, re-validate |

---

## 7. Composite Compatibility Checks

Before finalizing a composite release, verify these compatibility constraints:

### Required Compatibility Checks

| Constraint | Check | Failure Action |
|------------|-------|----------------|
| **Jurisdiction Alignment** | `policy.jurisdiction` ⊆ `claim.jurisdiction` | Narrow policy jurisdiction or expand claim |
| **Capability Coverage** | `procedure.capabilities` ⊆ `action_contract.capabilities` | Add missing capability to contract |
| **Resolution Profile Consistency** | All components share same `resolution_profile` | Align components to single profile |
| **Temporal Validity** | `policy.effective` ≤ `claim.effective` | Adjust effective dates or document gap |
| **Version Compatibility** | Component versions satisfy interdependencies | Update to compatible versions |

### Automated Verification Script

Add to `validate-eko-family.sh`:

```bash
# Compatibility checks after schema validation
echo "🔍 Checking compatibility constraints..."

# Extract components for analysis
components=$(jq -r '.component.components[]?.ref' <domain>.composite.json 2>/dev/null || echo "")

# Check 1: All components reference same resolution_profile
resolution_profiles=$(jq -r '.resolution_profile' *.json 2>/dev/null | sort -u | wc -l)
if [ "$resolution_profiles" -gt 1 ]; then
    echo "  ⚠️  Multiple resolution profiles detected"
    echo "     All components should use the same resolution_profile"
fi

# Check 2: Verify SHA256 digests match actual files
echo "  Verifying SHA256 digests..."
for file in claim*.json policy*.json procedure*.json contract*.json; do
    if [ -f "$file" ]; then
        actual_digest=$(shasum -a 256 "$file" | awk '{print $1}')
        # Check if this digest appears in composite
        if ! grep -q "$actual_digest" *.composite.json 2>/dev/null; then
            echo "  ⚠️  Digest mismatch for $file"
            echo "     Actual: sha256:$actual_digest"
        fi
    fi
done

echo "  ✅ Compatibility checks complete"
```

---

## 8. PDF-to-EKO Conversion Checklist (Definition of Done)

### Document Phase
- [ ] **Document Extracted:** Text and clauses segmented into discrete article topics
- [ ] **Clause Classification:** Every clause classified into claim, policy, procedure, or action_contract
- [ ] **Source Inventory:** Complete mapping of articles/sections to components documented

### Component Authoring Phase
- [ ] **Profile Mapping:** Every component assigned to correct EKO profile
- [ ] **Template Compliance:** All components follow their profile template exactly
- [ ] **Evidence Linkage:** All claims/policies cite source documents with article/section
- [ ] **Fact Bindings:** All dynamic runtime variables declared with sources and on_unknown handlers
- [ ] **Rule IR (policies):** State machines defined with transitions and terminal states
- [ ] **Step Sequences (procedures):** Ordered steps with conditions and compensation paths

### Quality Guardrails Phase
- [ ] **Behavioral Conduct:** Required explanations and prohibited conduct defined for all policies and procedures
- [ ] **Interpreter Pinning:** `interpreter` block present in all components (eko-ir v0.2.0)
- [ ] **Attestations:** Institutional sign-offs and signatures populated (minimum 1 per component)
- [ ] **Deployment Config:** Target environments, rollout strategy, rollback targets specified
- [ ] **SHA256 Content Addressing:** Real SHA256 hashes generated (not placeholders) and recorded in composite

### Test Suite Phase
- [ ] **Happy Path Tests:** At least 2 fixtures covering standard success cases
- [ ] **Edge Case Tests:** At least 2 fixtures covering boundaries, deadlines, exclusions
- [ ] **Unknown Fact Tests:** At least 1 fixture for null/missing value handling
- [ ] **Stale Evidence Tests:** At least 1 fixture for freshness violations
- [ ] **Conflict/Precedence Tests:** At least 1 fixture for competing policies
- [ ] **Test Coverage:** All major decision paths and outcomes exercised

### Validation Phase
- [ ] **Schema Validation:** 100% pass rate against canonical schemas using validate-eko-family.sh
- [ ] **SHA256 Verification:** All composite digests match actual component files
- [ ] **Compatibility Checks:** All composite constraints satisfied (jurisdiction, capabilities, profiles)
- [ ] **Test Execution:** All fixtures pass with expected outcomes
- [ ] **Error Recovery:** Documented fallbacks for any validation failures

### Documentation Phase
- [ ] **README.md:** Comprehensive documentation with validation instructions
- [ ] **Component Overview:** Table showing all components with validation status
- [ ] **Test Catalog:** All test fixtures documented with coverage types
- [ ] **Architecture Highlights:** Behavioral conduct, fact bindings, compatibility matrix
- [ ] **Workflow Diagrams:** Mermaid.js diagrams showing execution flows
- [ ] **Conversion Plan:** plan.md documents conversion strategy and traceability

### Release Phase
- [ ] **Composite Release:** Master release envelope with all components pinned
- [ ] **Version Tagged:** Semantic version assigned per change impact
- [ ] **Attestations Complete:** All authorities have signed off
- [ ] **Rollback Target:** Previous version identified for emergency rollback
- [ ] **Monitoring Configured:** Deployment alerts and checks specified
