# Communication Trace Record Specification v0.1

Communication Trace Record (CTR) v0.1 is a draft specification for recording communication-derived trace events across humans, AIs, and agents.

It defines a minimal, machine-readable event format for capturing how meaning is referenced, transformed, summarized, reused, and circulated.

This repository focuses on the recording layer of Communication Royalty OS. It does not directly calculate monetary royalties. Instead, it provides the trace basis that later systems may use for gratitude, credit, trust, audit, attribution, influence analysis, or royalty candidate review.

---

## Why this repository exists

In AI-native environments, value is increasingly generated not only by static works, but by communication processes such as:

- referencing
- summarizing
- responding
- transforming
- reusing
- influencing downstream outputs

CTR v0.1 exists to make those communication-derived traces visible, structured, and interoperable.

The goal is **not** to charge for communication itself.  
The goal is to record how communication contributes to later meaning generation, so that downstream systems can decide whether and how value should circulate.

---

## Scope

This repository defines a trace record format for communication events.

CTR v0.1 covers:

- event-level communication trace records
- actor / counterparty / action modeling
- input and output context
- trace assessment
- governance metadata
- optional settlement hooks for downstream circulation layers

CTR v0.1 does **not** cover:

- direct message pricing
- automatic legal ownership determination
- mandatory monetary settlement
- full cryptographic signature infrastructure
- full provenance graph serialization in a single record

---

## Core idea

A Communication Trace Record answers a simple but important question:

> What happened, who acted, what sources were involved, what output was produced, and how strongly did this event contribute to later meaning generation?

That makes CTR a foundational layer for:

- Communication Royalty OS
- gratitude systems
- trust systems
- attribution systems
- audit and accountability pipelines
- communication influence mapping

---

## Design principles

- Communication itself is not a billing target.
- Meaning generation is the primary value source.
- Trace does not automatically imply ownership.
- Trace does not automatically imply monetary royalty.
- The same trace may later map to gratitude, credit, trust, royalty, or none.
- Records should be machine-readable, human-auditable, and privacy-aware.
- Minimal viable interoperability is preferred over premature completeness.

---

## Repository structure

```text
.
├── .github/
│   └── workflows/
│       └── validate-specs.yml
├── schemas/
│   ├── communication-trace-record-v0.1.schema.json
│   └── ctr-id-unified-protocol-v0.1.schema.json
├── examples/
│   ├── communication-trace-record.sample.json
│   └── ctr-id-unified-protocol.sample.json
├── LICENSE
├── README.md
└── communication-trace-record-v0.1.yaml
Start here

Read the files in this order:

communication-trace-record-v0.1.yaml
Human-readable source specification for CTR v0.1.
schemas/communication-trace-record-v0.1.schema.json
Machine-validatable JSON Schema for the main Communication Trace Record format.
examples/communication-trace-record.sample.json
Example CTR record that should validate successfully against the main schema.
schemas/ctr-id-unified-protocol-v0.1.schema.json
Machine-validatable JSON Schema for the CTR-ID interoperability profile.
examples/ctr-id-unified-protocol.sample.json
Example CTR-ID record that should validate successfully against the CTR-ID schema.
.github/workflows/validate-specs.yml
CI workflow that checks schema/sample consistency on every push and pull request.
Specification overview

CTR v0.1 models a communication event as a structured record with these main sections:

1. Record metadata
record_type
spec_version
record_id
trace_id
timestamps
2. Actors
actor
optional counterparty
3. Communication act
action
4. Input context
input_context.sources
context hashes
source counts
source references
5. Output context
output type
output hashes
artifact references
6. Trace assessment
trace strength
derivation level
novelty or transformation indicators
return channel candidates
7. Governance
consent
privacy
retention
access scope
8. Settlement hooks
optional references for downstream gratitude / trust / royalty systems
9. Evidence
log references
tool run IDs
hashes
notes
CTR-ID and adjacent layers

CTR v0.1 is closely aligned with CTID-style trace recording, but it is specialized for communication-derived meaning generation.

A useful way to see the stack is:

Layer 1: Recording

CTR / CTID-style trace records

Layer 2: Circulation

gratitude, credit, trust, royalty candidate allocation

Layer 3: Governance and visibility

dashboards, audit, policy, legal interpretation

In that sense, CTR v0.1 is a recording-layer spec that can later feed adjacent systems such as:

Gratitude Event Protocol
Trust Event Specification
Royalty Allocation Specification
communication influence graphs
audit and compliance pipelines

The CTR-ID Unified Protocol exists to make identifier usage more consistent across those adjacent layers without forcing full provenance graph serialization into CTR v0.1 itself.

Example use cases

CTR v0.1 can be used to record events such as:

an AI summarizing a user’s conceptual note
an agent reusing prior discussion traces in a workflow
a system transforming retrieved documents into a structured answer
a tool chain producing a derived artifact from multiple communication inputs
a review pipeline marking a trace as a gratitude, credit, trust, or royalty candidate
Schema usage
Requirements
Python 3.10+
jsonschema

Install the validator locally:

python -m pip install --upgrade pip
pip install jsonschema
Validate all schemas and samples locally

Run the following command from the repository root:

python - <<'PY'
import json
import os
import sys
from jsonschema import Draft202012Validator

validations = [
    (
        "Communication Trace Record v0.1",
        "schemas/communication-trace-record-v0.1.schema.json",
        "examples/communication-trace-record.sample.json",
    ),
    (
        "CTR-ID Unified Protocol v0.1",
        "schemas/ctr-id-unified-protocol-v0.1.schema.json",
        "examples/ctr-id-unified-protocol.sample.json",
    ),
]

failed = False

for label, schema_path, sample_path in validations:
    print(f"\n=== Validating {label} ===")
    print(f"Schema: {schema_path}")
    print(f"Sample: {sample_path}")

    if not os.path.exists(schema_path):
        print(f"ERROR: Schema file not found: {schema_path}")
        failed = True
        continue

    if not os.path.exists(sample_path):
        print(f"ERROR: Sample file not found: {sample_path}")
        failed = True
        continue

    try:
        with open(schema_path, "r", encoding="utf-8") as f:
            schema = json.load(f)
    except json.JSONDecodeError as e:
        print(f"ERROR: Invalid JSON in {schema_path}: {e}")
        failed = True
        continue

    try:
        with open(sample_path, "r", encoding="utf-8") as f:
            sample = json.load(f)
    except json.JSONDecodeError as e:
        print(f"ERROR: Invalid JSON in {sample_path}: {e}")
        failed = True
        continue

    try:
        Draft202012Validator.check_schema(schema)
    except Exception as e:
        print(f"ERROR: Invalid schema in {schema_path}: {e}")
        failed = True
        continue

    validator = Draft202012Validator(schema)
    errors = sorted(validator.iter_errors(sample), key=lambda e: list(e.path))

    if errors:
        print(f"ERROR: Validation failed for {label}")
        for err in errors:
            path = ".".join(str(x) for x in err.path) if err.path else "<root>"
            print(f" - {path}: {err.message}")
        failed = True
    else:
        print(f"OK: {label} sample is valid.")

if failed:
    print("\nValidation failed.")
    sys.exit(1)

print("\nAll validations passed.")
PY
Validate only the main CTR schema
python - <<'PY'
import json
from jsonschema import Draft202012Validator

schema_path = "schemas/communication-trace-record-v0.1.schema.json"
sample_path = "examples/communication-trace-record.sample.json"

with open(schema_path, "r", encoding="utf-8") as f:
    schema = json.load(f)

with open(sample_path, "r", encoding="utf-8") as f:
    sample = json.load(f)

Draft202012Validator.check_schema(schema)
validator = Draft202012Validator(schema)
errors = sorted(validator.iter_errors(sample), key=lambda e: list(e.path))

if errors:
    print("Validation failed:")
    for err in errors:
        path = ".".join(str(x) for x in err.path) if err.path else "<root>"
        print(f" - {path}: {err.message}")
else:
    print("OK: CTR sample is valid.")
PY
Validate only the CTR-ID schema
python - <<'PY'
import json
from jsonschema import Draft202012Validator

schema_path = "schemas/ctr-id-unified-protocol-v0.1.schema.json"
sample_path = "examples/ctr-id-unified-protocol.sample.json"

with open(schema_path, "r", encoding="utf-8") as f:
    schema = json.load(f)

with open(sample_path, "r", encoding="utf-8") as f:
    sample = json.load(f)

Draft202012Validator.check_schema(schema)
validator = Draft202012Validator(schema)
errors = sorted(validator.iter_errors(sample), key=lambda e: list(e.path))

if errors:
    print("Validation failed:")
    for err in errors:
        path = ".".join(str(x) for x in err.path) if err.path else "<root>"
        print(f" - {path}: {err.message}")
else:
    print("OK: CTR-ID sample is valid.")
PY
CI validation

This repository also validates schema/sample pairs in GitHub Actions through:

.github/workflows/validate-specs.yml

The workflow is expected to run on push and pull request events so that schema drift and sample mismatches are caught early.

Validation expectations

A valid CTR record should satisfy at least the following:

record_type must be communication_trace_record
spec_version must be v0.1
record_id must match the CTR identifier pattern
trace_id must match the CTID identifier pattern
trace-strength-related numeric fields must remain within expected bounds
return channels must be valid
none must not be combined with other return channels
model_name is required when actor.actor_type is ai or agent
privacy-related fields should remain consistent with personal-data flags

Some higher-order checks, such as whether source_count exactly matches the real number of sources, may be better enforced in additional test code rather than pure JSON Schema.

For CTR-ID records, a valid record should satisfy at least the following:

event_record_id must match the CTR-style identifier pattern
trace_event_id must match the CTID-style identifier pattern
optional communication / conversation / artifact / structure IDs must match their declared prefixes
structure_refs[*].relation must use only allowed relation values
structure_refs[*].confidence, when present, must be between 0.0 and 1.0
Privacy note

CTR is designed to be privacy-aware.

Implementations are encouraged to prefer:

hashes over raw content
references over full payload retention
scoped identifiers over unnecessary identity exposure
redaction-friendly storage patterns

CTR should not be interpreted as a license to retain all communication content indefinitely.

Current status
Status: Draft
Version: v0.1

This version is intended as a minimal, interoperable first release for:

drafting
testing
schema validation
adjacent protocol integration

It should be treated as a stable draft for experimentation, not as a final legal or institutional standard.

Roadmap

Planned next steps may include:

stricter schema refinements
pass/fail compliance test vectors
explicit mapping to gratitude / trust / royalty layers
optional signature and verification fields
richer provenance linkage rules
dashboard-facing aggregation guidance
relationship documents to adjacent OS layers
Contributing

Contributions that improve clarity, interoperability, and validation rigor are welcome.

Especially useful contributions include:

schema corrections
better examples
compliance test vectors
validator improvements
documentation refinements
downstream mapping proposals

When proposing changes, try to preserve the core design principle:

communication is not the billing target; communication-derived meaning generation is the traceable value source

License

This repository is distributed under the terms of the repository-level LICENSE file.

One-line summary

CTR v0.1 is a minimal recording-layer specification for tracing how communication generates value across humans, AIs, and agents.
