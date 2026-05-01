# CTR-ID Unified Protocol v0.1

CTR-ID Unified Protocol v0.1 is a draft specification for a minimal identifier profile used to connect communication trace records across events, sessions, threads, conversations, structures, and derived artifacts.

It defines interoperable identifier semantics for trace-aware systems without forcing a full provenance graph into a single record.

This repository currently focuses on the CTR-ID layer only.

---

## Why this repository exists

Trace-oriented systems often fail not because they lack records, but because they lack consistent identifiers.

Different systems may record:

- the serialized record itself
- the traceable event
- the communication object
- the larger conversation
- the persistent semantic structure
- the derived artifact

If these are mixed together, downstream uses become unstable.

CTR-ID v0.1 exists to separate those layers cleanly and provide a minimal identifier profile that can later support:

- attribution
- audit
- trust systems
- gratitude systems
- influence mapping
- royalty candidate review
- adjacent communication trace standards

---

## Scope

CTR-ID v0.1 defines a minimal interoperable identifier profile.

It covers:

- record-level identifiers
- trace-event identifiers
- parent and multi-source lineage identifiers
- communication object identifiers
- conversation-root identifiers
- artifact identifiers
- structure reference identifiers

It does **not** cover:

- legal ownership determination
- monetary settlement
- full provenance graph serialization
- cryptographic signature infrastructure
- identity verification infrastructure
- mandatory global registry behavior

---

## Core idea

CTR-ID answers a basic structural question:

> What exactly is being identified?

It distinguishes between:

- the **record instance**
- the **trace event**
- the **communication object**
- the **conversation lineage**
- the **semantic structure**
- the **derived artifact**

This makes downstream trace systems more stable, more composable, and easier to audit.

---

## Design principles

- Keep the protocol minimal.
- Do not overload one identifier with multiple semantic roles.
- Separate runtime scope from meaning scope.
- Preserve compatibility with event-level trace recording.
- Allow future graph expansion without requiring graph completeness in v0.1.
- Prefer interoperability over premature complexity.
- Keep the format machine-readable and human-auditable.

---

## Identifier model

CTR-ID v0.1 uses the following identifier classes:

- `event_record_id`  
  Identifier for a serialized record instance.

- `trace_event_id`  
  Stable identifier for the traceable event node.

- `parent_trace_event_id`  
  Preferred direct parent event when one exists.

- `derived_from_trace_ids`  
  Optional multi-source lineage references.

- `communication_id`  
  Identifier for a communication object or exchange unit.

- `session_id`  
  Runtime session identifier defined by the issuing system.

- `thread_id`  
  Runtime thread identifier defined by the issuing system.

- `conversation_root_id`  
  Stable root identifier for a larger conversation tree.

- `artifact_id`  
  Identifier for a persisted output or deliverable.

- `structure_refs[].structure_id`  
  Identifier for a higher-order meaning structure or semantic continuity anchor.

---

## Prefix conventions

CTR-ID v0.1 currently uses these recommended prefixes:

- `ctr_` for record instances
- `ctid_` for trace events
- `comm_` for communication objects
- `conv_` for conversation roots
- `art_` for artifacts
- `sid_` for semantic structures

These prefixes are not a global registry. They are a minimal interoperability convention.

---

## Repository structure

```text
.
├── .github/
│   └── workflows/
│       └── validate-specs.yml
├── examples/
│   └── ctr-id-unified-protocol.sample.json
├── schemas/
│   └── ctr-id-unified-protocol-v0.1.schema.json
├── LICENSE
├── README.md
└── ctr-id-unified-protocol-v0.1.yaml
Start here

Read the files in this order:

ctr-id-unified-protocol-v0.1.yaml
Human-readable source specification for CTR-ID v0.1.
schemas/ctr-id-unified-protocol-v0.1.schema.json
Machine-validatable JSON Schema for the protocol.
examples/ctr-id-unified-protocol.sample.json
Example record that should validate successfully against the schema.
.github/workflows/validate-specs.yml
CI workflow that checks schema/sample consistency.
Example use cases

CTR-ID v0.1 can be used in cases such as:

linking multiple communication trace events across a dialogue
distinguishing a record instance from the underlying trace event
connecting a trace event to a larger conversation lineage
linking one event to multiple upstream sources
identifying a derived artifact separately from the event that produced it
marking semantic continuity across summary, translation, reframing, or transformation
Schema usage
Requirements
Python 3.10+
jsonschema

Install the validator locally:

python -m pip install --upgrade pip
pip install jsonschema
Validate the schema and sample locally

Run the following command from the repository root:

python - <<'PY'
import json
import os
import sys
from jsonschema import Draft202012Validator

label = "CTR-ID Unified Protocol v0.1"
schema_path = "schemas/ctr-id-unified-protocol-v0.1.schema.json"
sample_path = "examples/ctr-id-unified-protocol.sample.json"

failed = False

print(f"\n=== Validating {label} ===")
print(f"Schema: {schema_path}")
print(f"Sample: {sample_path}")

if not os.path.exists(schema_path):
    print(f"ERROR: Schema file not found: {schema_path}")
    failed = True

if not os.path.exists(sample_path):
    print(f"ERROR: Sample file not found: {sample_path}")
    failed = True

if failed:
    print("\nValidation failed.")
    sys.exit(1)

try:
    with open(schema_path, "r", encoding="utf-8") as f:
        schema = json.load(f)
except json.JSONDecodeError as e:
    print(f"ERROR: Invalid JSON in {schema_path}: {e}")
    sys.exit(1)

try:
    with open(sample_path, "r", encoding="utf-8") as f:
        sample = json.load(f)
except json.JSONDecodeError as e:
    print(f"ERROR: Invalid JSON in {sample_path}: {e}")
    sys.exit(1)

try:
    Draft202012Validator.check_schema(schema)
except Exception as e:
    print(f"ERROR: Invalid schema in {schema_path}: {e}")
    sys.exit(1)

validator = Draft202012Validator(schema)
errors = sorted(validator.iter_errors(sample), key=lambda e: list(e.path))

if errors:
    print("ERROR: Validation failed")
    for err in errors:
        path = ".".join(str(x) for x in err.path) if err.path else "<root>"
        print(f" - {path}: {err.message}")
    sys.exit(1)

print("OK: CTR-ID Unified Protocol v0.1 sample is valid.")
PY
CI validation

This repository validates the schema/sample pair in GitHub Actions through:

.github/workflows/validate-specs.yml

The workflow is intended to run on push and pull request events so that broken schemas, broken samples, or missing files are caught early.

Validation expectations

A valid CTR-ID record should satisfy at least the following:

event_record_id must match the ctr_ identifier pattern
trace_event_id must match the ctid_ identifier pattern
parent_trace_event_id, when present, must match the ctid_ pattern
derived_from_trace_ids, when present, must contain only ctid_ identifiers
communication_id, when present, must match the comm_ pattern
conversation_root_id, when present, must match the conv_ pattern
artifact_id, when present, must match the art_ pattern
structure_refs[*].structure_id must match the sid_ pattern
structure_refs[*].relation must use only allowed relation values
structure_refs[*].confidence, when present, must be between 0.0 and 1.0

Some semantic checks may remain implementation-level concerns rather than pure schema constraints. For example:

whether a declared parent is truly the preferred immediate lineage edge
whether derived_from_trace_ids reflect actual upstream influence
whether a structure_id is justified by stable semantic continuity
Current example profile

The current sample demonstrates:

a CTR-style record instance ID
a CTID-style trace event ID
a preferred direct parent trace
multi-source derivation
a communication-level ID
a conversation-root ID
an artifact ID
semantic structure references with relation types and confidence values

This sample is intentionally minimal but sufficient for validation and downstream experimentation.

Relationship to adjacent systems

CTR-ID v0.1 is designed to be usable alongside trace-oriented systems such as:

communication trace record formats
attribution pipelines
trust-event systems
gratitude-event systems
audit systems
influence graph builders
royalty candidate review systems

It is not itself a royalty protocol, a trust protocol, or a provenance graph protocol.

It is a small interoperability layer.

Privacy note

CTR-ID is only an identifier profile.

Implementations are encouraged to prefer:

scoped IDs instead of excessive identity exposure
reference-based linkage instead of unnecessary raw payload retention
privacy-aware storage and retention policies
clear separation between identifiers and personal content

Identifier interoperability should not be treated as a justification for unlimited content retention.

Current status
Status: Draft
Version: v0.1

This version is intended as a minimal working profile for:

drafting
schema validation
identifier interoperability testing
downstream protocol experimentation

It should be treated as a stable draft for structural use, not as a final institutional or legal standard.

Roadmap

Possible next steps include:

stricter schema refinements
pass/fail compliance test vectors
richer lineage rules
optional signature and verification fields
explicit mapping to adjacent trace standards
profile documents for gratitude / trust / royalty layers
governance notes for identifier issuance and reuse
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

separate record identity, event identity, communication identity, and structure identity without collapsing them into one ID

License

This repository is distributed under the terms of the repository-level LICENSE file.

One-line summary

CTR-ID Unified Protocol v0.1 is a minimal identifier interoperability profile for trace-aware communication systems.
