# Übersicht


- **ADR-005:** Project Workspace Architecture

- **ADR-009:** Textual Source Processing Boundary

- **ADR-010:** Project Source Registry Architecture

- **ADR-011:** Semantic Information Unit and Ontology Boundary

- **ADR-012:** Processing State and Artifact Organization

- **ADR-013:** Preliminary Coverage and Potential Model Support Assessment

- **ADR-014:** Project Dashboard Architecture and Evidence Navigation

- **ADR-015:** Project-bound Agentic Ingestion Integration Architecture

- **ADR-016:** Human Review Workspace and Approved Input Promotion Architecture

- **ADR-017:** Simple-by-Default Interaction and Progressive Disclosure

- **ADR-018:** Model Candidate Layer and Structural Comparability

- **ADR-019:** Internal Engineering Model Assembly Architecture

- **ADR-020:** Hybrid Target Projection and Coverage Architecture

- **ADR-021:** SYSIDE-Compatible SysML v2 Generation Architecture

- **ADR-022:** SysML v2 Validation Layer Architecture

- **ADR-023:** Final Model Review and Output Publication Architecture

- **ADR-024:** Guided Engineering Workflow and UX Projection Architecture

- **ADR-025:** Semantic Proposal Consolidation and Persona-Aware Consensus

- **ADR-026:** Source-Anchored Multi-Persona Interpretation and Cross-Unit Semantic Synthesis

- **ADR-027:** Source-Grounded Evidence Detection and Persona Interpretation Architecture

- **ADR-028a:** Model Derivation Mode and Review Escalation Architecture

- **ADR-028b:** Context-Preserving Canonical Engineering Subject Discovery

- **ADR-029:** Human-Reviewed Model Placement Before Model Assembly

- **ADR-030:** Semantic Interpretation and Controlled Classification Alignment

- **ADR-031:** Semantic Field Consistency Alignment

- **ADR-032:** Project-Level Multi-Source Reconciliation and Controlled Engineering Evolution

- **ADR-033:** Concern-Centric Project Reconciliation and Coherent Model Handoff

- **ADR-034:** Source Provenance Does Not Constrain Concern Grouping

- **ADR-035:** Safe-Demo Review Compression and Final Review Routing


\newpage


# ADR-005 — Project Workspace Architecture


Project Workspace Architecture

Status

Accepted

Date

2026-07-21

Context

Phase P introduces a persistent Project Workspace around the completed Phase F
ingestion pipeline.

P1 established the versioned framework template
`TURING_RFLP_FRAMEWORK` version `1.0.0`.

P2 requires a deterministic architecture for:

- project identity
- project metadata
- workspace discovery
- persistence and reopening
- project isolation
- future source, information-unit, run and coverage storage
- validation and recovery behavior

The Project Workspace contains software-operational project metadata. It shall
not redefine engineering knowledge from the authoritative CATIA SysML v2 model.

External repositories, including the Apollo 11 repository, remain
non-normative references and do not define this architecture.

Decision

## Project Identity

Every project receives an immutable technical identifier named `project_id`.

The `project_id`:

- is stored as a JSON string
- consists of exactly six decimal digits
- matches `^[0-9]{6}$`
- permits leading zeros
- is unique within the configured workspace
- remains unchanged for the lifetime of the project
- is used as the project directory name

Valid examples include:

- `000042`
- `318604`
- `999999`

The identifier is generated with:

```python
f"{secrets.randbelow(1_000_000):06d}"
```

Before an identifier is accepted, the workspace checks whether the corresponding
project directory already exists. A collision causes generation to be repeated.

Failure to produce an available identifier raises
`ProjectIdGenerationError`.

The six-digit identifier is a technical identifier only. It is not a password,
authentication credential or security secret.

## Project Display Name

Every project has a human-readable `display_name`.

The display name:

- is required
- contains between 1 and 120 characters after trimming
- is mutable
- must be unique across the complete configured workspace

Display-name uniqueness is checked during:

- project creation
- project rename
- workspace validation

Uniqueness comparison uses one deterministic normalization function:

1. trim leading and trailing whitespace
2. collapse consecutive whitespace
3. apply Unicode normalization
4. apply case-insensitive `casefold()` comparison

The original validated display text is stored in the manifest. The normalized
comparison value is derived and is not persisted.

Two projects must therefore not use names that differ only through casing,
equivalent Unicode representation or whitespace formatting.

The immutable `project_id` remains the authoritative identity even when the
display name changes.

## Workspace Root

The product workspace root is:

`data/projects`

The root is injectable so that tests and future integrations can use an isolated
location.

A persisted project is located at:

`data/projects/<project_id>/`

The P2 project directory initially contains exactly one required file:

`data/projects/<project_id>/project_manifest.json`

P2 does not pre-create empty directories.

Later phases create their own storage only when required:

- P3 owns `sources/`
- P4 owns `information_units/`
- P5 owns `runs/`
- P6 owns `coverage/`

This avoids placeholder directories whose contracts have not yet been
implemented.

## Project Manifest Contract

The manifest schema version is:

`1.0.0`

The manifest contains exactly these top-level fields:

```json
{
  "schema_version": "1.0.0",
  "project_id": "318604",
  "display_name": "Example Project",
  "description": "",
  "framework_template": {
    "template_id": "TURING_RFLP_FRAMEWORK",
    "template_version": "1.0.0"
  },
  "created_at": "2026-07-21T12:00:00Z",
  "updated_at": "2026-07-21T12:00:00Z"
}
```

The required rules are:

- `schema_version` is exactly `1.0.0`
- `project_id` matches the six-digit identifier contract
- `project_id` matches the containing directory name
- `display_name` follows the display-name contract
- `description` is required
- `description` may be empty
- `description` contains at most 2000 characters
- `framework_template.template_id` is
  `TURING_RFLP_FRAMEWORK`
- `framework_template.template_version` is `1.0.0`
- `created_at` is a UTC ISO-8601 timestamp ending in `Z`
- `updated_at` is a UTC ISO-8601 timestamp ending in `Z`
- `created_at` remains unchanged after project creation
- `updated_at` is refreshed when mutable metadata changes
- unknown fields are rejected

The framework reference is pinned. A project is never silently migrated to a
new framework-template version.

A future framework upgrade requires an explicit migration decision and
implementation.

The manifest does not contain:

- source lists
- ingestion runs
- reports
- information units
- coverage results
- approval decisions
- generated-model data
- derived counters
- duplicated filesystem paths

Those concerns belong to their responsible Phase P or later components.

## Project Discovery

The workspace has no central `projects.json` index.

Projects are discovered by scanning:

`data/projects/*/project_manifest.json`

The directory structure and each validated manifest are the source of truth for
project discovery.

Avoiding a central index prevents the index and individual manifests from
diverging.

A scan returns a `WorkspaceScanResult` containing:

- `valid_projects`
- `workspace_issues`

A malformed project must not make valid projects unavailable.

The scanner reports issues for conditions including:

- visible directories whose names are not valid six-digit project identifiers
- missing manifests
- invalid JSON
- missing required fields
- unknown fields
- unsupported schema versions
- invalid framework references
- mismatch between `project_id` and directory name
- duplicate normalized display names
- unsafe paths
- symbolic-link project directories

When two manifests use the same normalized display name, both projects are
reported as conflicting. They remain identifiable through their immutable
project IDs so that one can be renamed explicitly.

Hidden filesystem metadata such as `.DS_Store` is ignored.

Temporary creation directories matching `.create-<project_id>.tmp` are ignored
during discovery but are never deleted automatically.

The workspace does not silently skip validation errors and does not
automatically repair or delete malformed data.

## Safe Persistence

Project creation uses a temporary sibling directory:

`data/projects/.create-<project_id>.tmp/`

Creation follows this sequence:

1. generate an available project ID
2. validate the requested metadata
3. create the temporary sibling directory
4. write the manifest into the temporary directory
5. read and validate the written manifest
6. atomically rename the temporary directory to the final project directory

Manifest updates use:

`project_manifest.json.tmp`

An update follows this sequence:

1. load and validate the existing manifest
2. apply the permitted metadata change
3. preserve `project_id`, `created_at` and the pinned framework reference
4. write the updated manifest to the temporary file
5. validate the temporary manifest
6. atomically replace the existing manifest with `os.replace`

Interrupted or invalid writes must not replace the last valid manifest.

Temporary data is retained for explicit diagnosis and is not automatically
deleted.

## Project Isolation and Path Safety

All project operations are resolved below the configured workspace root.

The implementation rejects:

- absolute external project paths
- path traversal
- paths escaping the configured workspace root
- symbolic-link project directories
- project IDs that do not match the six-digit identifier contract

The implementation does not follow symbolic links while discovering or loading
projects.

Sources, runs, information units and artifacts introduced in later steps must
reference the immutable `project_id` and remain within that project’s directory.

Cross-project data mixing is prohibited.

## Module Boundaries

P2 introduces:

```text
modules/project_workspace/
├── __init__.py
├── errors.py
├── identifiers.py
├── manifest.py
├── types.py
└── workspace.py
```

### `errors.py`

Defines the Project Workspace exception hierarchy:

- `ProjectWorkspaceError`
- `ProjectManifestError`
- `ProjectNotFoundError`
- `DuplicateProjectNameError`
- `ProjectIdGenerationError`
- `UnsafeProjectPathError`

### `identifiers.py`

Owns:

- six-digit project-ID generation
- project-ID validation
- deterministic display-name normalization

### `types.py`

Defines immutable data types:

- `FrameworkTemplateReference`
- `ProjectManifest`
- `WorkspaceIssue`
- `WorkspaceScanResult`

### `manifest.py`

Owns:

- manifest parsing
- manifest serialization
- manifest validation
- schema-version validation
- timestamp validation
- framework-reference validation
- rejection of unknown fields

It does not access project directories or scan the workspace.

### `workspace.py`

Owns:

- workspace scanning
- project creation
- project loading
- project metadata updates
- project-ID collision checks
- display-name uniqueness checks
- path and symbolic-link safety
- atomic filesystem operations
- workspace issue collection

The public `ProjectWorkspace` interface is:

```python
create_project(display_name, description="")
load_project(project_id)
update_project(
    project_id,
    *,
    display_name=None,
    description=None,
)
scan_projects()
```

The workspace root, ID generator and clock are injectable for deterministic
tests.

## Dependency Direction

The dependency direction is:

```text
workspace
    -> manifest
    -> identifiers
    -> types
    -> errors
    -> framework template validation
```

`modules/framework` does not depend on `modules/project_workspace`.

No reverse dependency from the framework-template module to the Project
Workspace is permitted.

## P2 Scope Boundary

P2 includes:

- project identity
- project metadata
- manifest validation
- safe persistence
- project discovery
- project reopening
- project isolation foundations

P2 does not include:

- project deletion or archiving
- source registration
- source upload
- information-unit persistence
- ingestion-run organization
- coverage calculation
- approval decisions
- model-candidate creation
- SysML v2 generation
- Project Dashboard integration

Those capabilities remain assigned to later roadmap steps.

Consequences

Positive consequences:

- Projects have compact, immutable and easily comparable identifiers.
- Display names remain human-readable without becoming technical identities.
- Duplicate or confusing display names are prevented.
- Project metadata can be validated independently of the UI.
- Projects can be reopened without maintaining a second index.
- Atomic writes reduce the risk of corrupted manifests.
- Invalid projects do not prevent access to valid projects.
- Future Phase P components receive a stable isolation boundary.
- Framework-template versions remain explicit and reproducible.

Trade-offs:

- The identifier space is limited to one million values.
- Identifier generation requires collision detection.
- Workspace scanning performs filesystem validation instead of reading a
  central index.
- Strict schema validation rejects manifests with unexpected fields.
- Incomplete temporary data requires explicit operator review.
- Framework-template upgrades require explicit migration.

Alternatives Considered

UUID project identifiers were rejected because they are unnecessarily difficult
to compare manually for the expected workspace size.

Mutable display names as project identities were rejected because renaming would
break references and equal or similar names would create ambiguity.

A central project index was rejected because it would duplicate manifest state
and introduce synchronization risk.

Pre-creating all future Phase P directories was rejected because their storage
contracts belong to later implementation steps.

Permissive manifest parsing and silent issue skipping were rejected because they
would hide inconsistent project state.

Automatic repair or deletion of malformed and temporary data was rejected
because recovery must remain explicit and auditable.

Affected Components

- `modules/project_workspace/`
- `tests/test_project_manifest.py`
- `tests/test_project_workspace.py`
- `data/projects/`
- later P3–P7 components that reference `project_id`

Supersedes

None

Related Roadmap Phase

P2 — Project Manifest and Workspace Structure

Related Implementation

Not yet implemented.


\newpage


# ADR-009 — Textual Source Processing Boundary


Textual Source Processing Boundary

Status

Accepted

Date

2026-07-22

Context

The Turing Generator shall ingest heterogeneous customer documents and derive
traceable engineering information through the agentic ingestion pipeline.

The original Phase F implementation reads UTF-8 text directly from a source
path. Phase P introduces registered project sources and heterogeneous,
source-traceable information units.

Modern LLM APIs can accept document files such as PDF, DOCX, PPTX and
spreadsheets directly. Some providers and models may additionally interpret
page images, diagrams and other visual content.

Supporting arbitrary multimodal engineering sources would require architectural
decisions for:

- optical character recognition
- drawing and diagram interpretation
- symbol recognition
- geometric and spatial relationships
- visual provenance
- multimodal uncertainty
- conflict resolution between textual and visual information
- human validation of visually extracted statements

These concerns exceed the accepted MVP and thesis scope.

Decision

## Supported Information Modality

The only supported source-information modality in the MVP is textual
information.

The original source file does not have to be a plain-text file.

A binary container format may be used when its relevant textual content can be
converted deterministically into a traceable textual representation before the
LLM-based engineering extraction begins.

Examples include:

- a PDF containing a machine-readable text layer
- a future DOCX adapter that extracts paragraphs and tables
- other document containers for which a deterministic textual adapter is
  explicitly implemented and validated

File-container support and information-modality support are separate concerns.

Supporting a file extension does not imply support for every type of content
that the file may contain.

## Immutable Original Source

The registered original source remains immutable.

It is stored with:

- `project_id`
- `source_id`
- original filename
- media type
- byte size
- SHA-256 hash
- explicit source role
- registration timestamp

Normalization never replaces or modifies the registered original source.

## Deterministic Text Normalization

Before an LLM processes a registered source, the source must produce a
normalized textual artifact through a deterministic, versioned source adapter.

The normalized artifact must remain traceable to:

- `project_id`
- `source_id`
- original source SHA-256
- adapter identifier
- adapter version
- source locations used to produce the normalized text

Source-location references depend on the input format.

Examples include:

- text and Markdown: line ranges
- PDF: page references
- JSON: JSON Pointer locations
- CSV: row and column references
- future DOCX support: sections, paragraphs or table cells

The normalized textual artifact is a derived processing artifact. It is not an
authoritative engineering source and does not replace the original file.

## PDF Boundary

The MVP may support PDF files only when they contain a usable
machine-readable text layer.

PDF normalization extracts textual content and retains page boundaries for
traceability.

The MVP does not interpret:

- technical drawings
- diagrams
- photographs
- graphical relationships
- dimensions represented only visually
- symbols represented only visually
- scanned pages without a machine-readable text layer

A PDF must not be sent as unrestricted multimodal input when doing so would
allow the selected model to derive engineering information from visual content
outside the accepted MVP scope.

## Explicit Failure States

A source that cannot produce sufficient traceable textual content must not be
silently treated as an empty or irrelevant source.

Processing must end with an explicit diagnostic state such as:

- `unsupported_non_textual_content`
- `text_extraction_insufficient`
- `unsupported_source_format`
- `source_normalization_failed`

The original source remains registered and available for diagnosis.

No failed source is silently discarded or promoted.

## LLM Processing Boundary

The LLM receives the normalized textual artifact together with explicit
provenance information.

The LLM may semantically interpret, classify and derive candidate engineering
information from that textual input.

The LLM must not silently invent information that is absent from the normalized
source.

The LLM output remains unreviewed and non-authoritative.

## Canonical Extraction Output

The canonical result of LLM extraction is validated structured data.

The canonical structured output shall use a versioned JSON contract for
source-traceable candidate information units.

Markdown is not the canonical extraction format.

A Markdown review report is generated deterministically from the validated
structured extraction data.

The raw LLM output is retained as a technical traceability artifact but is not
used directly as approved engineering information.

## Human-in-the-Loop Boundary

Extracted information units remain unreviewed until a human decision has been
recorded.

The review workflow must allow information units to be:

- accepted
- corrected
- rejected
- marked as requiring clarification

Generating or reading a Markdown report does not approve its contents.

Phase P may persist preliminary and review-oriented information, but it does not
promote information into generation-ready input.

Approved Input Promotion remains assigned to Phase G.

Only explicitly human-approved structured engineering information may become
input to later model-candidate and model-generation stages.

## Provider and Model Independence

The Project Workspace does not assume that every provider, endpoint or model
supports the same file types.

Provider-specific capabilities are evaluated separately from source
registration.

The deterministic normalized textual artifact forms the provider-independent
boundary for MVP ingestion.

Direct provider file input may be introduced later only when it preserves the
accepted modality boundary, provenance requirements and review behavior.

## Excluded MVP Scope

The following capabilities are explicitly excluded from the MVP:

- optical character recognition
- scanned-document interpretation
- technical-drawing interpretation
- diagram interpretation
- image-based engineering evidence
- handwritten-content recognition
- audio ingestion
- video ingestion
- spatial or geometric relation extraction from visual media
- general multimodal engineering information extraction

Consequences

Positive consequences:

- The MVP scope remains technically and academically manageable.
- Existing text-oriented Phase F pipeline concepts remain reusable.
- Different source containers can use a common ingestion boundary.
- LLM inputs remain reproducible and provider-independent.
- Engineering statements can reference deterministic source locations.
- Human review operates on structured and traceable information units.
- Visual information cannot silently become engineering evidence.
- Future multimodal extensions have a clear architectural starting point.

Trade-offs:

- Scanned PDFs cannot be processed without a future OCR capability.
- Relevant information contained only in drawings or diagrams is not extracted.
- Binary document containers require a deterministic text adapter.
- Source normalization introduces an additional persisted artifact and
  validation step.
- Some direct multimodal capabilities of selected LLMs remain intentionally
  unused in the MVP.

Alternatives Considered

Accepting every file type supported by the selected LLM was rejected because
provider support does not establish traceability, reproducibility or a stable
human-review boundary.

Sending PDFs directly as unrestricted multimodal model input was rejected for
the MVP because it could introduce engineering information derived from
drawings, images or spatial relationships without an accepted visual-evidence
contract.

Restricting registration to plain-text file extensions was rejected because
common customer documents may use binary containers while still containing
deterministically extractable textual information.

Using Markdown as the canonical extracted-information store was rejected
because Markdown is intended for human-readable reporting rather than strict
schema validation and downstream processing.

Replacing the original file with extracted text was rejected because it would
destroy source integrity and weaken traceability.

Affected Components

- Project Source Registry introduced in P3
- deterministic source-normalization adapters
- Phase F ingestion integration
- framework-mapped information units introduced in P4
- processing state and artifact organization introduced in P5
- human review and Approved Input Promotion introduced in Phase G
- future provider and model adapters
- future SysML v2 representation of the Turing Generator

Model Impact

When the authoritative SysML v2 model is next updated, it shall represent the
accepted textual-source-processing constraint.

The model update shall distinguish:

- supported textual information
- text-bearing document containers
- excluded non-textual and multimodal information
- deterministic source normalization
- human review before Approved Input Promotion
- multimodal processing as a future extension

Stable model element identifiers and their relationships shall be assigned in
the authoritative model. This ADR does not create or redefine those engineering
model elements.

SSOT Impact

The next scheduled SSOT UPDATE after completion of Phase P shall document:

- the textual-only MVP information-modality constraint
- deterministic text normalization
- the exclusion of OCR, drawings and multimodal extraction
- structured JSON as the canonical unreviewed extraction format
- Markdown as the derived review representation
- the required future SysML v2 model synchronization

Supersedes

None

Related Roadmap Phase

- P3 — Source Registry and Mandatory Project Assignment
- P4 — Framework-mapped Heterogeneous Information Units
- P5 — Processing State and Artifact Organization
- G — Approved Input Promotion

Related Implementation

Not yet implemented.


\newpage


# ADR-010 — Project Source Registry Architecture


Project Source Registry Architecture

Status

Accepted

Date

2026-07-22

Context

Phase P introduces project-oriented processing around the completed Phase F
agentic ingestion pipeline.

P2 implemented the persistent Project Workspace and established immutable
six-digit project identifiers.

P3 requires a deterministic architecture for:

- mandatory assignment of every new source to exactly one project
- stable source identity
- immutable storage of original source files
- explicit source roles
- duplicate detection
- source discovery and integrity validation
- project isolation
- later traceability from information units, runs, reports and artifacts back to
  their originating source

ADR-009 establishes that the MVP supports textual information only.

The Source Registry does not perform text projection, semantic interpretation,
ontological mapping or LLM processing. Those responsibilities begin in P4.

Decision

## Source Identity

Every registered source receives an immutable technical identifier named
`source_id`.

The `source_id`:

- is stored as a JSON string
- matches `^SRC-[0-9]{6}$`
- is unique within its containing project
- remains unchanged for the lifetime of the source
- is used as the source directory name

Valid examples include:

- `SRC-000001`
- `SRC-004281`
- `SRC-999999`

The `project_id` and `source_id` are separate identifiers.

The project identifier identifies the project container. The source identifier
identifies one individual source within that project.

The globally unambiguous source reference is the pair:

```text
<project_id>/<source_id>
```

Example:

```text
318604/SRC-000001
```

A source must not be identified only by:

- original filename
- stored filename
- display label
- content hash
- source role

## Source-ID Allocation

Source IDs are allocated sequentially within one project.

The first source ID is:

`SRC-000001`

The next source ID is greater than every existing or temporarily occupied source
ID in that project.

Allocation considers:

- final source directories
- temporary registration directories

Gaps are not reused automatically.

Source IDs remain reserved when a temporary registration directory exists.

If `SRC-999999` has already been reached, registration fails with
`SourceIdExhaustedError`.

If a concurrent operation occupies the selected final directory, the registry
rescans the project and attempts registration with the next available sequential
identifier.

The six-digit source number is a technical identifier only. It is not a
password, authentication credential or security secret.

## Mandatory Project Assignment

Every source registration requires a valid `project_id`.

The referenced project must:

- exist
- contain a valid project manifest
- use the expected Project Workspace schema
- resolve safely below the configured Project Workspace root

There is no API for registering a permanent unassigned source.

There is no global source pool.

A source belongs to exactly one project.

The same original file may be registered in different projects because project
storage and evidence remain isolated.

## Source Storage Structure

The source root is created only when the first source is registered.

A registered source is stored under:

```text
data/projects/<project_id>/sources/<source_id>/
```

The final source directory contains:

```text
source_manifest.json
content.<suffix>
```

Example:

```text
data/projects/318604/sources/SRC-000001/
├── source_manifest.json
└── content.pdf
```

The original filename is stored only as metadata.

The original filename is not used directly as a filesystem path.

The stored filename is generated by the registry.

A safe, recognized lowercase suffix may be retained.

Examples include:

- `content.md`
- `content.txt`
- `content.json`
- `content.csv`
- `content.pdf`

An absent, unsupported or unsafe suffix produces:

`content.bin`

Unknown media types are represented as:

`application/octet-stream`

The registry never executes a registered source file.

## Format-Neutral Registration

The Source Registry stores original bytes and technical metadata.

It does not decide whether a registered source can be processed by the selected
LLM, provider or future source adapter.

Registration and processability are separate concerns.

A source may therefore be successfully registered but later receive a
processing state such as:

`registered_but_not_processable`

The textual-source-processing constraint from ADR-009 is evaluated before
semantic ingestion begins.

A source must not be rejected solely because the current LLM does not support
its format.

The registry still rejects sources that violate storage, safety or integrity
rules.

## Immutable Original Source

The source content is immutable after successful registration.

The registry records:

- byte size
- SHA-256 hash

Both values are calculated from the bytes stored in the final source directory.

Loading or scanning a source validates its stored content against the manifest.

A mismatch is an integrity error.

The registry never silently:

- replaces source content
- overwrites an existing source
- updates a hash to match changed content
- repairs a damaged source
- substitutes a different file
- deletes inconsistent source data

A changed document must be registered as a new source with a new `source_id`.

## Source Roles

Every source has exactly one explicit source role.

Permitted roles are:

- `engineering_source`
- `context_only`

There is no default source role.

The caller must select the source role during registration.

An `engineering_source` may later produce source-traceable engineering
information units and contribute to clearly marked preliminary coverage.

A `context_only` source may support terminology and interpretation but shall not:

- create engineering evidence
- create framework assignments
- satisfy preliminary coverage
- satisfy approved readiness
- contribute to model generation

Source-role behavior remains governed by the accepted Phase P rules.

## Source Manifest Contract

The Source Manifest schema version is:

`1.0.0`

The manifest contains exactly these top-level fields:

```json
{
  "schema_version": "1.0.0",
  "project_id": "318604",
  "source_id": "SRC-000001",
  "source_role": "engineering_source",
  "original_filename": "System Requirements.pdf",
  "stored_filename": "content.pdf",
  "media_type": "application/pdf",
  "size_bytes": 1845203,
  "sha256": "full-lowercase-sha256-value",
  "registered_at": "2026-07-22T10:30:00Z",
  "updated_at": "2026-07-22T10:30:00Z"
}
```

Required validation rules are:

- `schema_version` is exactly `1.0.0`
- `project_id` follows the Project Workspace identifier contract
- `project_id` matches the containing project directory
- `source_id` follows the Source Registry identifier contract
- `source_id` matches the containing source directory
- `source_role` is an explicitly permitted source role
- `original_filename` is present and non-empty
- `original_filename` contains no path semantics
- `stored_filename` is a registry-generated basename
- `stored_filename` contains no absolute path or traversal
- `media_type` is present and non-empty
- `size_bytes` is a positive integer
- `sha256` is a lowercase 64-character hexadecimal SHA-256 value
- `registered_at` is a UTC ISO-8601 timestamp ending in `Z`
- `updated_at` is a UTC ISO-8601 timestamp ending in `Z`
- `updated_at` is not earlier than `registered_at`
- unknown fields are rejected

The manifest does not contain:

- normalized text
- LLM output
- information units
- ontology mappings
- framework assignments
- processing runs
- reports
- coverage calculations
- review decisions
- Approved Input
- model candidates
- generated model content

Those concerns belong to later components.

## Mutable and Immutable Metadata

The following values are immutable:

- `schema_version`
- `project_id`
- `source_id`
- original source bytes
- `original_filename`
- `stored_filename`
- `media_type`
- `size_bytes`
- `sha256`
- `registered_at`

The following values may be updated under the defined role-change rules:

- `source_role`
- `updated_at`

During P3, a source role may be corrected because no Project Workspace
processing runs exist yet.

When P5 introduces persisted processing runs, role changes for sources with
dependent runs or artifacts must be blocked until an explicit invalidation and
reprocessing contract has been accepted.

Changing a role must never silently reinterpret existing downstream artifacts.

## Duplicate Content

Before registration, the registry calculates the input SHA-256 hash and compares
it with existing valid sources in the selected project.

If the same SHA-256 already exists in that project:

- registration is rejected
- `DuplicateSourceContentError` is raised
- the error identifies the existing `source_id`

This prevents the same source content from being counted repeatedly as
apparently independent engineering evidence.

The same SHA-256 may exist in different projects.

Equal original filenames with different content are permitted.

The filename is never used for duplicate detection.

## Atomic Registration

Registration accepts an existing local regular file.

The input path must:

- exist
- identify a regular file
- not be a symbolic link
- not be empty

Registration follows this sequence:

1. load and validate the selected project
2. validate the source role
3. validate the input file
4. calculate the input size and SHA-256
5. scan existing project sources for duplicate content
6. allocate the next sequential source ID
7. create a temporary sibling directory
8. copy the source bytes blockwise into the temporary directory
9. calculate size and SHA-256 again while copying
10. verify that the source did not change during registration
11. create the Source Manifest
12. validate the manifest and temporary stored content
13. atomically rename the temporary directory to the final source directory
14. load and return the persisted Source Manifest

The temporary directory is:

```text
data/projects/<project_id>/sources/.register-<source_id>.tmp/
```

Example:

```text
data/projects/318604/sources/.register-SRC-000001.tmp/
```

An interrupted registration must not create a final source directory.

Temporary registration directories:

- are ignored during normal source discovery
- reserve their contained source number
- are never automatically deleted
- remain available for explicit diagnosis

## Atomic Source-Role Update

A source-role update:

1. loads and validates the existing source
2. verifies that the new role is permitted
3. preserves every immutable manifest value
4. changes only `source_role` and `updated_at`
5. writes `source_manifest.json.tmp`
6. validates the temporary manifest
7. atomically replaces the existing manifest with `os.replace`

An invalid update must not replace the last valid manifest.

## Source Discovery

Sources are discovered by scanning:

```text
data/projects/<project_id>/sources/*/source_manifest.json
```

There is no central source index.

The project directory and validated source manifests are the source of truth.

A scan returns a `SourceScanResult` containing:

- `valid_sources`
- `source_issues`

A malformed source must not make valid sources unavailable.

## Source Scan Issues

The scanner reports issues including:

- visible directories whose names are not valid Source IDs
- missing Source Manifests
- invalid JSON
- missing required fields
- unknown fields
- unsupported schema versions
- invalid source roles
- mismatch between manifest and containing `project_id`
- mismatch between manifest and containing `source_id`
- invalid stored filenames
- missing stored content
- unexpected additional content files
- symbolic-link source directories
- symbolic-link content files
- invalid byte sizes
- stored-size mismatches
- invalid SHA-256 values
- stored-content hash mismatches
- duplicate content within one project
- unsafe paths

Sources with blocking conflicts are excluded from `valid_sources`.

They remain individually identifiable through `project_id` and `source_id` for
diagnosis.

When duplicate content is discovered in existing persisted data, every
conflicting source is reported and excluded from `valid_sources`.

Hidden operating-system metadata such as `.DS_Store` is ignored.

Temporary registration directories are ignored by normal discovery but are
considered during Source-ID allocation.

No malformed or temporary data is automatically repaired or deleted.

## Path and Symlink Safety

All persisted source paths are generated below the validated project directory.

The implementation rejects:

- absolute stored filenames
- stored path traversal
- paths escaping the selected project
- symbolic-link project source roots
- symbolic-link source directories
- symbolic-link source content
- Source IDs that do not satisfy the identifier contract
- filenames that resolve outside their source directory

The implementation does not follow source-directory or source-content symbolic
links.

The input file may originate outside the Project Workspace, but it is only read
and copied into a generated safe destination.

The input file itself must not be a symbolic link.

## Module Boundaries

P3 introduces:

```text
modules/project_sources/
├── __init__.py
├── errors.py
├── identifiers.py
├── types.py
├── manifest.py
└── registry.py
```

### `errors.py`

Defines the Source Registry exception hierarchy:

- `ProjectSourceError`
- `SourceManifestError`
- `SourceNotFoundError`
- `DuplicateSourceContentError`
- `SourceIdExhaustedError`
- `UnsupportedSourceRoleError`
- `UnsafeSourcePathError`
- `SourceIntegrityError`

### `identifiers.py`

Owns:

- Source-ID validation
- Source-ID formatting
- sequential Source-ID allocation
- Source-ID exhaustion detection

It does not access source content or parse manifests.

### `types.py`

Defines immutable data types:

- `SourceManifest`
- `SourceIssue`
- `SourceScanResult`

### `manifest.py`

Owns:

- Source Manifest parsing
- Source Manifest serialization
- Source Manifest validation
- schema-version validation
- source-role validation
- timestamp validation
- stored-filename validation
- rejection of unknown fields

It does not scan project directories or modify source files.

### `registry.py`

Owns:

- source registration
- source loading
- source-content path resolution
- source discovery
- duplicate detection
- source-integrity verification
- source-role updates
- safe path handling
- symbolic-link rejection
- atomic filesystem operations
- Source Issue collection

### `__init__.py`

Exposes only the intentional public Source Registry API.

## Public Interface

The public service is:

`ProjectSourceRegistry`

Its initial public operations are:

```python
register_source(
    project_id,
    source_path,
    *,
    source_role,
)

load_source(
    project_id,
    source_id,
)

source_content_path(
    project_id,
    source_id,
)

update_source_role(
    project_id,
    source_id,
    *,
    source_role,
)

scan_sources(
    project_id,
)
```

The Project Workspace root and clock are injectable for deterministic tests.

Filesystem content is processed blockwise so registration and integrity
validation do not require loading complete source files into memory.

## Dependency Direction

The dependency direction is:

```text
project_sources
    -> project_workspace
    -> Python standard library
```

`modules/project_workspace` does not depend on
`modules/project_sources`.

The completed P2 module remains independent and stable.

`modules/project_sources` does not depend on:

- ingestion agents
- LLM providers
- source-normalization adapters
- information-unit modules
- coverage modules
- UI modules
- model-generation modules

## P3 Scope Boundary

P3 includes:

- mandatory project assignment
- source identity
- Source Manifest validation
- immutable original-source storage
- duplicate detection
- explicit source roles
- source-role correction before dependent processing
- source discovery
- source integrity validation
- project isolation
- safe and atomic persistence

P3 does not include:

- deterministic textual source projection
- PDF text extraction
- semantic interpretation
- terminology or ontology lookup
- LLM execution
- information-unit extraction
- framework mapping
- processing-run persistence
- reports
- human approval
- Approved Input Promotion
- Project Dashboard integration
- model-candidate creation
- SysML v2 generation

Deterministic textual source projection and semantic candidate extraction begin
in P4 under the boundary established by ADR-009.

Consequences

Positive consequences:

- Every source is assigned to exactly one project.
- Source identity is independent of filenames.
- Short Source IDs remain easy to compare manually.
- Original customer files remain unchanged.
- Duplicate evidence within a project is prevented.
- Source integrity can be verified deterministically.
- Registration remains independent of providers and LLM capabilities.
- Valid sources remain usable when other source directories are malformed.
- P4 receives a stable, traceable source boundary.
- The completed P2 module remains independent.

Trade-offs:

- Sequential Source IDs require scanning existing and temporary directories.
- Gaps are not automatically reused.
- Temporary failed registrations require explicit operator review.
- A registered source may not yet be processable.
- Source-role correction becomes more constrained after processing runs exist.
- Strict integrity validation exposes manual filesystem changes as errors.
- There is no automatic cleanup, repair or content replacement.

Alternatives Considered

Using the project ID as the source ID was rejected because one project may
contain multiple sources.

Using filenames as source identity was rejected because filenames are mutable,
repeatable and unsafe as stable references.

Using globally unique Source IDs was rejected because project-scoped identity is
sufficient when every external reference contains both `project_id` and
`source_id`.

Using random Source IDs was rejected because sequential project-local IDs are
easier to compare and do not require collision generation in the expected
project scale.

Maintaining a central source index was rejected because it would duplicate
manifest state and introduce synchronization risk.

Restricting registration to the file types currently supported by the Phase F
pipeline was rejected because registration and processability are separate
responsibilities.

Allowing duplicate content within one project was rejected because duplicate
sources could incorrectly multiply apparent engineering evidence.

Automatically deleting incomplete registration directories was rejected because
recovery and cleanup must remain explicit and auditable.

Allowing source-content replacement was rejected because it would break
traceability from downstream artifacts to the original evidence.

Adding text extraction or semantic interpretation to the Source Registry was
rejected because it would violate single responsibility and hide semantic
decisions inside persistence logic.

Affected Components

- `modules/project_sources/`
- `tests/test_source_manifest.py`
- `tests/test_project_source_registry.py`
- `data/projects/<project_id>/sources/`
- future P4 information-unit components
- future P5 run and artifact components
- future Project Dashboard integration

Model Impact

This ADR defines software persistence and traceability architecture.

The textual information-modality constraint and later semantic-processing
boundary are defined by ADR-009 and shall be represented when the authoritative
SysML v2 model is updated.

Stable model identifiers remain the responsibility of the authoritative model
and are not created by this ADR.

SSOT Impact

The next scheduled SSOT UPDATE after completion of Phase P shall document:

- completion of P3
- project-local Source IDs
- mandatory project assignment
- immutable original-source storage
- explicit source roles
- duplicate detection
- Source Registry integrity behavior
- the separation of source registration from processability
- the dependency from P3 to the P4 textual-source-processing boundary

Supersedes

None

Related Roadmap Phase

P3 — Source Registry and Mandatory Project Assignment

Related Decisions

- ADR-005 — Project Workspace Architecture
- ADR-009 — Textual Source Processing Boundary

Related Implementation

Not yet implemented.


\newpage


# ADR-011 — Semantic Information Unit and Ontology Boundary


Semantic Information Unit and Ontology Boundary

Status

Accepted

Date

2026-07-22

Context

Phase P introduces project-oriented processing around the completed Phase F
agentic ingestion pipeline.

P1 implemented the accepted Stakeholder, System and Subsystem framework.

P2 implemented the persistent Project Workspace.

P3 implemented the Project Source Registry with mandatory project assignment,
immutable original sources and explicit source roles.

P4 shall transform registered textual engineering sources into heterogeneous,
source-traceable Information Units and map them to valid nodes of the accepted
framework.

ADR-009 limits the MVP to textual information. Binary document containers may
be supported only when their relevant textual content can be projected
deterministically into a traceable textual representation.

ADR-010 establishes that the Source Registry remains format-neutral and does
not perform text projection, semantic interpretation, ontology mapping or LLM
processing.

P4 therefore requires explicit architecture decisions for:

- deterministic source projection
- semantic extraction
- Information Unit identity and atomicity
- information classification
- terminology and ontology references
- project-specific terminology
- framework mapping
- multiagent consensus and confidence
- immutable semantic persistence
- boundaries to P5, P6 and Phase G

No universal ontology is treated as an implicit or complete MBSE gold standard.

The selected reference architecture combines:

- SysML v2 and KerML semantics
- BFO 2020
- IOF Core
- a curated Turing Core Vocabulary
- a human-reviewed Project Glossary

External reference systems shall support semantic consistency without replacing
the accepted project framework or the authoritative engineering model.

Decision

## Semantic Processing Stages

P4 separates the semantic workflow into four stages.

### P4.1 — Deterministic Source Projection

A registered source is converted into a deterministic, traceable textual
representation.

This stage performs no semantic interpretation.

### P4.2 — LLM Semantic Extraction

Independent persona agents identify candidate Information Units from the
deterministic Source Projection.

### P4.3 — Terminology and Ontology Candidate Mapping

Extracted terminology is compared with:

- accepted Project Glossary concepts
- the Turing Core Vocabulary
- selected IOF Core and BFO concepts

All mappings remain explicit and traceable.

### P4.4 — Framework Mapping

Information Units are mapped to zero, one or multiple valid nodes of the
accepted framework template.

Framework mapping produces candidates. It does not perform Engineering
Approval.

## Artifact Separation

P4 distinguishes the following artifact types.

### Source Projection

A Source Projection is a deterministic textual representation of one registered
source.

It does not contain semantic interpretations.

### Information Unit

An Information Unit is the smallest independently understandable,
source-traceable professional statement that:

- was semantically extracted from an engineering source
- expresses one independently reviewable main claim
- can be independently classified
- can be independently reviewed
- can be independently mapped to the framework

### Terminology Concept

A Terminology Concept represents a controlled project or reference-system
meaning.

It is not an engineering statement and does not create framework coverage.

### Framework Assignment

A Framework Assignment connects exactly one Information Unit to exactly one
valid mapping target of a specific framework-template version.

These artifact types shall not be merged into one mutable record.

## Source-Role Boundary

Only a source with the role:

`engineering_source`

may create:

- engineering Information Units
- Framework Assignments
- preliminary engineering coverage

A source with the role:

`context_only`

may create:

- a Source Projection
- terminology candidates
- candidate definitions
- candidate explanations
- Project Glossary provenance

A context-only source shall not create:

- engineering Information Units
- Framework Assignments
- preliminary coverage
- approved readiness
- model-generation input

Terminology extracted from a context-only source remains visibly traceable to
that source.

## Information Unit Identity

Every Information Unit receives an immutable project-local identifier named
`information_unit_id`.

The identifier:

- is stored as a JSON string
- matches `^IU-[0-9]{6}$`
- is unique within one project
- is allocated sequentially
- is not reused
- remains unchanged for the lifetime of the Information Unit

Valid examples include:

- `IU-000001`
- `IU-004281`
- `IU-999999`

The globally unambiguous reference is the pair:

```text
<project_id>/<information_unit_id>
```

Example:

```text
318604/IU-000001
```

The two identifiers remain separate stored fields.

The display form does not replace the individual identifiers.

## Information Unit Immutability

The following Information Unit content is immutable after persistence:

- Information Unit identity
- project reference
- source reference
- Source Projection reference
- source anchors
- source excerpt
- interpreted statement
- information classification
- extraction provenance

If later processing produces a materially changed interpretation, it creates a
new Information Unit with a new identifier.

An existing Information Unit shall not be silently rewritten.

P5 may later record that one Information Unit supersedes or invalidates another.
Such lifecycle information remains separate from the immutable Information Unit.

## Information Unit Atomicity

An Information Unit contains exactly one independently reviewable main claim.

Independent obligations shall be split into separate Information Units.

Conditions, qualifiers and limits that are necessary to understand one claim
remain attached to that claim.

An Information Unit belongs to exactly one engineering source.

It may reference multiple anchors within that source.

P4 shall not synthesize one Information Unit from multiple sources.

Equivalent or overlapping claims from different sources remain separate
Information Units.

They may be linked later, but they shall not be automatically merged.

Content fingerprints may support:

- duplicate detection
- idempotence
- comparison
- reproducibility

A content fingerprint never replaces the Information Unit identifier.

## Source Excerpt and Interpreted Statement

An Information Unit distinguishes:

```text
source_excerpt
interpreted_statement
```

`source_excerpt` contains the unchanged relevant text from the deterministic
Source Projection.

`interpreted_statement` contains the semantic candidate interpretation.

The field name `normalized_statement` shall not be used for the semantic
interpretation because deterministic normalization and semantic interpretation
are separate operations.

## Source Projection Identity

Every Source Projection receives an immutable project-local identifier named
`source_projection_id`.

The identifier:

- matches `^SP-[0-9]{6}$`
- is unique within one project
- is allocated sequentially
- is not reused

Valid examples include:

- `SP-000001`
- `SP-004281`
- `SP-999999`

A Source Projection belongs to exactly one registered source.

## Source Projection Segment Identity

Every segment within one Source Projection receives a projection-local
identifier named `segment_id`.

The identifier:

- matches `^SEG-[0-9]{6}$`
- is unique within one Source Projection
- preserves deterministic source order

An Information Unit shall reference at least one segment.

All segments referenced by one Information Unit shall belong to the same Source
Projection and therefore to the same registered source.

## Deterministic Source Projection

The LLM shall not receive an original registered file directly.

Every semantic process begins with a deterministic, persistable Source
Projection.

Permitted deterministic operations include:

- deterministic UTF-8 decoding
- removal of a UTF-8 byte-order mark
- normalization of line endings
- deterministic textual extraction from a supported container
- preservation of source order
- deterministic structural segmentation
- generation of source locators
- generation of projection issues
- calculation of hashes and fingerprints

The deterministic stage shall not perform:

- spelling correction
- synonym replacement
- terminology harmonization
- unit conversion
- semantic rewriting
- summarization
- requirement rewriting
- ontology mapping
- framework mapping
- domain inference

Deterministic source projection is syntactic and structural, not semantic.

## Supported P4 Source Adapters

The P4 MVP provides explicit adapters for:

```text
.txt
.md
.json
.csv
.tsv
.pdf
```

An extension is supported only when a deterministic adapter has been
implemented and validated.

The P4 MVP does not support:

```text
.doc
.docx
.odt
.ppt
.pptx
.xls
.xlsx
.rtf
```

These formats may be added later through explicit, versioned adapters.

The P4 MVP also excludes:

- scanned PDFs
- OCR
- image interpretation
- drawing interpretation
- diagram interpretation
- geometric reasoning
- audio
- video
- handwritten information

## Text Encoding

Textual source adapters accept:

- UTF-8
- UTF-8 with byte-order mark

Encoding guessing and silent fallback decoding are prohibited.

Unsupported or invalid encodings shall produce a visible projection issue.

## JSON Projection

A JSON source is parsed deterministically.

JSON locations use JSON Pointer references.

Duplicate object keys are rejected because they would make the source meaning
and traceability ambiguous.

Semantic interpretation of JSON values remains outside the deterministic
projection step.

## CSV and TSV Projection

CSV uses:

- RFC 4180-compatible parsing
- comma as the fixed delimiter

TSV uses:

- tab as the fixed delimiter

Dialect sniffing is prohibited.

Row and column locations remain available for traceability.

## PDF Projection

PDF support uses an explicit and pinned `pypdf` dependency.

Only machine-readable text is extracted.

Page boundaries are retained as source locators.

P4 does not perform:

- OCR
- image extraction for semantic processing
- drawing interpretation
- diagram interpretation

An empty or unextractable page produces a visible projection issue.

A PDF containing both extractable text and unsupported visual information may
produce a partial Source Projection.

## Source Projection Result

A Source Projection has exactly one result:

```text
complete
partial
unavailable
```

`complete` means that the supported textual content was projected without a
known projection loss.

`partial` means that usable textual content exists, but unsupported,
unextractable or failed content was detected.

`unavailable` means that no usable deterministic textual projection could be
created.

A partial result remains visible and requires its issues to be retained.

An unavailable result shall not enter semantic extraction.

## Source Projection Fingerprint

A Source Projection fingerprint is derived from at least:

- original source SHA-256
- adapter identifier
- adapter version
- deterministic adapter configuration

The fingerprint supports reproducibility and idempotence.

It does not replace:

- `project_id`
- `source_id`
- `source_projection_id`

## Information Classification

P4 separates three classification dimensions:

- Information Type
- Statement Modality
- Epistemic Class

These dimensions shall not be collapsed into one field.

## Information Types

The accepted Information Types are:

```text
stakeholder
actor
user_need
requirement
use_case
function
logical_element
physical_element
interface
constraint
information_item
definition
rationale
decision
risk
ambiguity
gap
open_question
unclassified
```

`subsystem` is not an Information Type.

Subsystem is a framework level and shall not be confused with the semantic kind
of an Information Unit.

`unclassified` is an explicit valid result. The system shall not force an
unsupported classification.

## Statement Modalities

The accepted Statement Modalities are:

```text
descriptive
normative
definitional
interrogative
```

Statement Modality describes how the statement is expressed.

It does not determine its framework mapping or Engineering Approval.

## Epistemic Classes

The accepted Epistemic Classes are:

```text
explicit
interpretation
derivation
assumption
```

`explicit` means that the professional statement is directly expressed in the
source.

`interpretation` means that semantic interpretation is necessary, but the
statement remains grounded in the source.

`derivation` means that the statement is derived from other Information Units.

`assumption` means that information not established by the source is introduced
to make an interpretation possible or visible.

A derivation requires:

- referenced supporting Information Unit identifiers
- a derivation rationale

During P4, supporting Information Units for a derivation shall belong to the
same source.

An assumption requires:

- an explicit missing-evidence explanation
- a visible assumption classification

The source anchor of an assumption records what triggered the assumption. It
does not turn the assumption into source evidence.

## Preliminary Coverage Exclusions

The following Information Types shall never create positive preliminary
coverage:

```text
risk
ambiguity
gap
open_question
```

Information Units classified as `assumption` shall never create positive
preliminary coverage.

They may still be associated with a relevant framework node for diagnostic
traceability.

The complete deterministic coverage policy belongs to P6.

## Human Review Separation

An immutable Information Unit shall not contain a mutable Human Review status.

Human Review decisions are separate records.

Terminology acceptance and Engineering Approval are separate processes.

Engineering Approval remains assigned to Phase G.

## Semantic Confidence

An individual LLM agent shall not determine the final confidence of an
Information Unit, terminology mapping or Framework Assignment.

Final confidence is derived deterministically from the variance of independent
persona-agent results.

The accepted confidence values are:

```text
high
medium
low
```

Confidence is ordinal.

It is not:

- a statistical probability
- a truth value
- a Human Review decision
- an Engineering Approval
- a readiness decision

A confidence value requires an auditable rationale.

## Multiagent Execution

P4 reuses the existing Phase F multiagent architecture.

All required members of one semantic team receive:

- the same task
- the same structured input
- the same role
- different explicit personas or professional perspectives

Team members execute independently.

They shall not coordinate before consensus analysis.

P4 may define dedicated teams for:

- semantic extraction
- terminology and ontology mapping
- framework mapping

Existing Phase F outputs are not automatically P4 Information Units.

P4 requires P4-specific structured output schemas and validators.

## Consensus and Variance

A deterministic consensus analyzer compares structured persona results.

The analyzer shall preserve:

```text
consensus_level
variance_level
confidence
total_personas
supporting_personas
dissenting_personas
value_distribution
review_required
confidence_rationale
consensus_report_id
```

The confidence is derived according to the following policy.

### High Confidence

`high` requires agreement of all required personas on the comparable result,
without a contradictory result or unexplained omission.

This corresponds to low inter-persona variance.

### Medium Confidence

`medium` may be assigned when:

- a strict majority supports the same result and other personas omit it
- a strict majority supports the same result but a minority provides a
  differing result

A differing result requires Human Review even when a majority exists.

### Low Confidence

`low` is assigned when:

- only one persona proposes the result
- no clear majority exists
- conflicting results exist
- the team run is technically incomplete
- the result cannot be compared reliably

Low confidence requires Human Review.

## Inter-Persona and Intra-Persona Variance

Inter-persona variance measures differences between distinct professional
perspectives.

Intra-persona variance measures differences between repeated runs of the same
persona.

Repeated runs of the same persona shall not be counted as additional
independent votes.

Each persona receives at most one vote in the team consensus.

Repeated runs measure stability and may reduce confidence when the persona
produces inconsistent results.

They shall not create an artificial majority.

The persona team is intentionally heterogeneous and is not a random,
independent statistical sample.

The resulting variance is an epistemic uncertainty signal, not a statistically
calibrated probability.

## Field-Level Confidence

Consensus and variance may be calculated separately for:

- existence of an Information Unit
- semantic interpretation
- Information Type
- Statement Modality
- Epistemic Class
- terminology mapping
- ontology mapping
- framework target

A Framework Assignment receives its confidence from the agreement about its
specific target and mapping rationale.

The lowest confidence of a critical required field conservatively limits the
overall confidence of the resulting candidate.

## Semantic Reference Stack

The accepted semantic reference stack is:

1. SysML v2 and KerML
2. BFO 2020
3. IOF Core
4. Turing Core Vocabulary
5. Project Glossary
6. LLM candidate generation

These layers have distinct responsibilities.

## SysML v2 and KerML

SysML v2 and KerML define the normative target-model and target-notation
semantics.

The curated repository references remain:

```text
context/sysml/sysml_v2_spec_reference.json
context/sysml/sysml_v2_target_notation.json
```

These files constrain later model generation and valid target notation.

They are not treated as a complete industrial-domain ontology.

## BFO 2020

BFO 2020 is the selected top-level ontology reference.

It provides general distinctions for entities, processes, qualities, roles and
related foundational categories.

The selected reference corresponds to BFO 2020 and ISO/IEC 21838-2.

BFO does not directly provide the complete MBSE or project vocabulary.

## IOF Core

IOF Core is the selected industrial mid-level ontology reference.

The pinned reference version uses:

```text
versionIRI: 202602
maturity: Released
```

IOF Core provides industrial semantic structure above BFO and below
project-specific terminology.

IOF Core does not replace:

- SysML v2
- the accepted framework
- Turing Core
- the Project Glossary
- human engineering decisions

## Local Ontology Snapshots

BFO and IOF are used through pinned local snapshots.

The intended structure is:

```text
external/ontologies/bfo/2020/bfo-core.owl
external/ontologies/iof/202602/AboutIOFProd.rdf
external/ontologies/iof/202602/Core.rdf
external/ontologies/iof/202602/AnnotationVocabulary.rdf
```

The corresponding license material shall be stored with the snapshots.

P4 shall not perform:

- live ontology queries
- automatic ontology downloads
- automatic ontology updates
- remote runtime dependency resolution

Updating an ontology snapshot requires an explicit reviewed change.

## Ontology Registry

The selected ontology snapshots are registered in:

```text
context/semantics/ontology_registry.json
```

The registry records at least:

- reference-system identifier
- version
- version IRI where applicable
- local file path
- source authority
- maturity
- license reference
- enabled runtime role
- checksum

The registry is curated and versioned.

## Reference Concept Index

P4 does not require an OWL reasoner or triple store.

A deterministic generated read-only concept index is stored in:

```text
context/semantics/reference_concept_index.json
```

The index supports runtime retrieval of selected:

- concept identifiers
- IRIs
- preferred labels
- alternative labels
- definitions
- parent relationships
- source-system references
- version references

The generated index is not the ontology authority.

The pinned RDF and OWL snapshots remain the auditable external-reference
artifacts.

The index can be regenerated deterministically.

## Runtime Ontology Boundary

P4 shall not introduce:

- a triple store
- an OWL reasoner
- live SPARQL endpoints
- unrestricted ontology graph traversal
- automatic inference from the complete external ontology graph
- automatic ontology updates

Only relevant retrieved concepts are supplied to an LLM.

Complete ontology files shall not be loaded into every prompt.

## Turing Core Vocabulary

A curated Turing Core Vocabulary is stored in:

```text
context/semantics/turing_core_vocabulary.json
```

Turing Core provides the controlled bridge between:

- MBSE terminology
- the accepted RFLP-oriented framework
- SysML v2 target concepts
- selected external ontology concepts
- project-specific concepts

Turing Core concepts receive stable identifiers matching:

```text
^TC-[0-9]{6}$
```

Valid examples include:

- `TC-000001`
- `TC-004281`
- `TC-999999`

Turing Core is global to the Turing Generator.

It shall not be modified automatically from one project.

## SysDO Boundary

SysDO is retained as a non-normative research and structuring reference.

It is not the primary semantic authority because its demonstrated scope and
maturity do not satisfy the complete requirements of the Turing Generator.

In particular, it does not replace the selected combination of:

- SysML v2 and KerML
- BFO
- IOF Core
- Turing Core
- Project Glossary

SysDO remains disabled as a runtime ontology source during the P4 MVP.

Nothing is copied from SysDO without explicit review.

## External Ontology Mappings

A Turing or Project Concept may map to an external concept using one of the
following relations:

```text
exact_match
narrower_than
broader_than
related_to
no_equivalent
```

Every mapping requires:

- the referenced external concept or IRI
- the referenced ontology and version
- a mapping relation
- a rationale
- provenance

An unmapped concept is valid.

The system shall not force an `exact_match`.

External ontology mappings cannot:

- create engineering requirements
- create framework coverage
- approve engineering information
- overwrite a project meaning
- overwrite Turing Core
- alter the authoritative engineering model

## Project Glossary

Every project owns a versioned Project Glossary under:

```text
data/projects/<project_id>/semantics/project_glossary.json
```

The Project Glossary captures reviewed project-specific vocabulary.

It supplements the external reference stack.

It does not replace or overwrite BFO, IOF Core or Turing Core.

## Project Concept Identity

Every Project Concept receives a stable project-local identifier named
`project_concept_id`.

The identifier:

- matches `^PC-[0-9]{6}$`
- is unique within one project
- is allocated sequentially
- is not reused

The globally unambiguous reference is the pair:

```text
<project_id>/<project_concept_id>
```

Example:

```text
318604/PC-000001
```

Project Concept identity remains stable across revisions.

## Project Concept Content

A Project Concept may contain:

- multilingual preferred labels
- multilingual alternative labels
- multilingual definitions
- broader Project Concept references
- related Project Concept references
- Turing Core mappings
- BFO or IOF mappings
- provenance
- lifecycle status
- revision
- rationale

Each project defines a default language.

## Preferred-Label Uniqueness

Accepted preferred labels shall be unique within one project and language.

Uniqueness comparison applies:

- Unicode NFKC normalization
- whitespace trimming
- Unicode case folding

Labels that differ only through these comparison operations conflict.

For example, case-only or compatible full-width variants shall not silently
create separate accepted concepts.

## Ambiguous Alternative Labels

A true homonym may be retained as an alternative label only through an explicit
Ambiguity Group.

An Ambiguity Group records at least:

- label
- language
- candidate Project Concept identifiers
- `resolution_rule: context_required`

Ambiguous labels shall never be resolved automatically.

The user interface may display disambiguated names such as:

```text
Port (Physical Interface)
Port (Network Endpoint)
```

The display text does not replace the stable Project Concept identifiers.

## Project Concept Provenance

Every Project Concept or revision requires provenance from at least one of:

- an engineering source
- a context-only source
- an explicit human terminology decision
- a selected external reference system
- Turing Core

Context-only provenance is permitted for terminology.

It does not create engineering evidence or framework coverage.

## Project Concept Lifecycle

The accepted Project Concept lifecycle states are:

```text
candidate
accepted
rejected
deprecated
```

Concepts shall not be deleted or silently overwritten.

A concept cannot become accepted solely through an LLM result.

## Terminology Decisions

Only a human may create the authoritative decision that accepts, rejects or
later replaces a Project Concept.

Terminology Decisions receive immutable project-local identifiers matching:

```text
^TD-[0-9]{6}$
```

A Terminology Decision records at least:

- project identifier
- Terminology Decision identifier
- Project Concept identifier
- Project Concept revision
- decision
- reviewer identity
- decision timestamp
- rationale

Reviewer identity is documented for auditability.

It is not treated as cryptographic identity proof.

## Terminology and Engineering Approval

Terminology acceptance confirms the intended meaning of a Project Concept.

It does not approve an engineering statement.

Engineering Review and Approved Input Promotion remain assigned to Phase G.

A terminology-approved concept may be used consistently in later processing,
but it does not create an approved Information Unit.

## LLM Terminology Permissions

An LLM may propose:

- Project Concepts
- preferred labels
- alternative labels
- definitions
- ambiguity candidates
- Turing Core mappings
- external ontology mappings

An LLM shall not:

- accept a Project Concept
- reject a Project Concept authoritatively
- merge concepts authoritatively
- resolve a homonym silently
- declare an `exact_match` authoritatively
- overwrite an accepted concept
- promote a Project Concept into Turing Core
- share a Project Concept with another project

## Terminology Conflicts

When new source usage conflicts with an accepted Project Glossary meaning, the
system creates a visible Terminology Conflict.

The conflict shall not be resolved automatically.

A human determines whether the result requires:

- a new concept
- a new concept revision
- an additional alternative label
- an Ambiguity Group
- deprecation
- rejection of the candidate interpretation
- no glossary change

## Project Concept Revisions

A Project Concept identifier remains stable.

A material edit creates a new revision.

Revision history remains preserved.

Only accepted revisions are authoritative in later semantic processing.

Candidate revisions may be shown to an LLM and reviewer but are not
authoritative.

Rejected revisions shall not be used as positive semantic evidence.

Deprecated revisions remain available only for traceability.

P5 defines the detailed lifecycle-event and invalidation storage.

## Project Isolation

Project Concepts remain project-specific.

They shall not be automatically:

- copied to another project
- shared across projects
- promoted into Turing Core
- treated as globally authoritative

A future cross-project curation workflow requires a separate architecture
decision.

## Framework Assignment Identity

Every Framework Assignment receives an immutable project-local identifier named
`framework_assignment_id`.

The identifier:

- matches `^FA-[0-9]{6}$`
- is unique within one project
- is allocated sequentially
- is not reused

The globally unambiguous reference is the pair:

```text
<project_id>/<framework_assignment_id>
```

Example:

```text
318604/FA-000001
```

## Framework Assignment Semantics

A Framework Assignment connects:

- exactly one Information Unit
- to exactly one valid framework mapping target
- in exactly one framework-template version

An Information Unit may have:

- no Framework Assignment
- one Framework Assignment
- multiple Framework Assignments

Every individual assignment requires its own:

- identifier
- mapping rationale
- mapping basis
- confidence evidence

Multiple assignments are not represented by one ambiguous target field.

## Framework Assignment Content

A Framework Assignment records at least:

```text
schema_version
project_id
framework_assignment_id
information_unit_id
framework_template_id
framework_template_version
framework_node_id
mapping_bases
mapping_rationale
confidence
confidence_rationale
consensus_report_id
created_at
supersedes_assignment_id
```

`supersedes_assignment_id` is absent when the assignment does not supersede an
earlier assignment.

## Framework Target Validation

`framework_node_id` must identify a valid mapping target in the referenced
framework-template version.

Free-form target names are prohibited.

Unknown framework targets are rejected.

The initial accepted framework remains:

```text
TURING_RFLP_FRAMEWORK
version 1.0.0
```

## Multiple Framework Assignments

Multiple Framework Assignments are permitted only when each target is
professionally justified.

A mapping to a child node shall not automatically create a mapping to its parent.

A mapping shall not be propagated automatically to:

- another framework level
- a sibling node
- another subsystem
- another Information Unit

Framework hierarchy and semantic relevance remain separate concerns.

## Framework Mapping Bases

An assignment may reference one or multiple mapping bases:

```text
direct_semantic
structural_context
project_glossary
turing_core
external_ontology
```

Referenced concepts, Project Concept revisions and ontology IRIs remain
explicitly traceable.

Label similarity alone is not a sufficient mapping rationale.

An accepted Project Glossary concept may support a Framework Assignment.

It shall not force one.

## Framework Mapping Evaluation

The absence of a Framework Assignment is ambiguous because it could mean either:

- mapping has not been performed
- mapping was performed and no valid target was found

Every completed mapping attempt therefore creates an immutable Framework
Mapping Evaluation.

Framework Mapping Evaluation identifiers match:

```text
^FME-[0-9]{6}$
```

The accepted evaluation outcomes are:

```text
mapped
unmapped
ambiguous
failed
```

An evaluation references zero or multiple Framework Assignments.

`unmapped`, `ambiguous` and `failed` require an explicit rationale or error
description.

## Framework-Mapping Governance

The LLM may:

- propose Framework Assignments
- propose multiple possible targets
- report an ambiguous mapping
- report that no valid mapping was found
- provide concise mapping rationales

The LLM shall not:

- approve a Framework Assignment
- silently choose one target from an ambiguous result
- create a framework node
- change the framework hierarchy
- treat confidence as approval
- perform Engineering Approval

P4 produces mapping candidates.

Engineering Approval remains assigned to Phase G.

## Framework Version Binding

Every Framework Assignment remains permanently bound to the framework-template
version used during its creation.

A new framework-template version requires a new mapping evaluation.

Existing assignments shall not be silently migrated.

## Mapping Correction

A corrected mapping produces:

- a new Framework Mapping Evaluation
- new Framework Assignments where required
- explicit later supersession references

Existing assignments and evaluations remain auditable.

## Semantic Persistence Root

Project-specific semantic records are stored logically below:

```text
data/projects/<project_id>/semantics/
```

The accepted semantic areas are:

```text
data/projects/<project_id>/semantics/
├── source_projections/
├── information_units/
├── project_glossary.json
├── terminology_decisions/
├── terminology_conflicts/
└── framework_mappings/
```

The exact organization of processing runs, temporary artifacts and derived
indexes belongs to P5.

## Source Projection Persistence

A persisted Source Projection contains:

```text
source_projections/<source_projection_id>/
├── projection.json
└── content.txt
```

`projection.json` records at least:

- schema version
- project identifier
- source identifier
- Source Projection identifier
- source SHA-256
- adapter identifier
- adapter version
- adapter configuration
- projection fingerprint
- projection result
- segments
- source locators
- issues
- projected-content SHA-256
- creation timestamp

`content.txt` contains the deterministic UTF-8 textual projection.

It does not replace the immutable original source in the P3 Source Registry.

## Persistence Rules

P4 records are:

- schema-validated
- project-isolated
- written atomically
- immutable after publication unless explicitly defined as versioned
- never silently overwritten
- never silently deleted

A new record shall not reference a different project.

Derived indexes may be regenerated.

They are not authoritative.

The validated P4 records remain authoritative for the P4 semantic repository.

## Reproducibility Metadata

Semantic processing remains traceable to:

- project identifier
- source identifier
- Source Projection and fingerprint
- adapter identifier and version
- team configuration
- persona configuration
- LLM provider
- LLM model
- prompt or schema version
- Consensus Report
- Ontology Registry version
- Reference Concept Index version
- Turing Core version
- Project Glossary concept revisions
- framework-template identifier and version

BFO and IOF files are not copied into every project.

Project records reference the registered pinned versions and concept IRIs.

## Reprocessing

Reprocessing the same Source Projection creates a new Processing Run.

An identical validated result may be recognized through fingerprints.

A materially different semantic result creates a new semantic record with a new
identifier or revision.

Fingerprints support comparison and idempotence.

They do not replace semantic identities.

## P4 Responsibility

P4 owns:

- deterministic Source Projection adapters
- Source Projection validation
- Information Unit schema and validation
- semantic multiagent extraction
- terminology and ontology candidates
- Project Glossary domain rules
- Framework Assignment and Evaluation rules
- field-level consensus and variance
- confidence derivation
- persistence of validated semantic records

## P5 Boundary

P5 owns:

- Processing Run identity
- processing states
- run resumption
- organization of raw agent outputs
- Consensus Report organization
- temporary processing artifacts
- lifecycle events
- supersession
- invalidation
- derived operational indexes
- detection of incomplete publication

P5 shall not rewrite the professional content of an Information Unit,
Framework Assignment or terminology decision.

A Processing Run is complete only after its required P4 records have been
validated and published successfully.

Partial processing results shall not appear as a completed run.

## P6 Boundary

P6 reads the P4 semantic records that P5 identifies as usable.

P6 calculates:

- Preliminary Coverage
- coverage gaps
- mapping conflicts
- preliminary framework support

P6 shall not modify P4 records.

P6 does not treat an Information Unit as approved engineering input.

## Phase G Boundary

P4 records are candidates.

Phase G creates separate Human Review and Engineering Approval records.

An approval references the exact immutable record or concept revision that was
reviewed.

Approval of one version does not automatically approve:

- a successor Information Unit
- a new Framework Assignment
- a new Project Concept revision
- a remapped framework-template version

Only Phase-G-approved engineering information may enter Phases H through J.

## Phase F Integration

P4 reuses the existing Phase F infrastructure for:

- team execution
- persona instructions
- LLM-provider access
- raw agent artifacts
- deterministic consensus analysis

P4 extends this infrastructure with:

- P4-specific structured result schemas
- P4-specific teams and personas where required
- field-level consensus comparison
- inter-persona variance
- intra-persona stability
- semantic validators
- controlled creation of P4 domain records

Existing Phase F reports and agent outputs are not automatically converted into
P4 Information Units.

## Model-Generation Boundary

P4 shall not:

- create approved engineering input
- create model candidates
- generate an internal engineering model
- generate SysML v2 code
- update CATIA
- override the authoritative engineering model

Approved Input Promotion belongs to Phase G.

Model Candidate creation belongs to Phase H.

Internal model generation belongs to Phase I.

SysML v2 code generation belongs to Phase J.

Consequences

## Positive Consequences

The architecture provides:

- deterministic traceability from source bytes to semantic interpretation
- explicit separation of syntactic projection and semantic processing
- stable project-local semantic identifiers
- immutable and auditable Information Units
- visible assumptions, ambiguity and disagreement
- controlled use of external ontology references
- project-specific terminology without global contamination
- reproducible framework assignments
- multiagent variance as an auditable uncertainty signal
- separation of confidence and Engineering Approval
- clear boundaries between P4, P5, P6 and Phase G
- continued reuse of the implemented Phase F agent infrastructure
- a bounded MVP without an ontology server or multimodal interpretation

## Costs and Limitations

The architecture introduces:

- multiple semantic artifact types
- additional validators and repositories
- explicit ontology and vocabulary curation
- more storage for immutable records and revisions
- multiple LLM calls per semantic task
- increased processing cost for multiagent execution
- a Human Review requirement for terminology and ambiguous mappings
- no support for visual engineering information
- no automatic cross-source synthesis
- no statistical calibration of confidence
- no automatic ontology reasoning
- no automatic cross-project terminology reuse

These costs are accepted in exchange for traceability, auditability and
controlled semantic processing.

Alternatives Considered

## Single Universal MBSE Ontology

Rejected.

No single reference system provides the complete combination of:

- top-level semantics
- industrial concepts
- MBSE and RFLP terminology
- SysML v2 target semantics
- project-specific vocabulary

The layered reference stack is more explicit and adaptable.

## BFO or IOF as the Complete Project Vocabulary

Rejected.

BFO is too general and IOF Core is not a complete project or SysML vocabulary.

Both remain useful reference layers.

## SysDO as the Primary Runtime Ontology

Rejected.

SysDO is useful as a non-normative research and structuring reference, but its
demonstrated maturity and scope are insufficient for primary runtime authority.

## Project Glossary Without External References

Rejected.

A project-only vocabulary would improve local consistency but reduce
interoperability and make concept mappings harder to justify.

## Live Ontology Service

Rejected for the MVP.

Live services would reduce reproducibility and introduce availability,
versioning and security dependencies.

## Full OWL Reasoner or Triple Store

Rejected for the MVP.

The accepted P4 use cases require controlled concept retrieval and explicit
mapping, not unrestricted automated inference.

## Loading Complete Ontologies Into Every Prompt

Rejected.

It would increase token use and reduce prompt focus without providing a
controlled semantic guarantee.

## Direct Original-File Processing by the LLM

Rejected.

It would make text extraction provider-dependent and weaken deterministic
traceability.

## Semantic Normalization During Source Projection

Rejected.

Spelling correction, synonym replacement, unit conversion and rewriting are
semantic operations and could change source meaning.

## Mutable Information Units

Rejected.

Overwriting extracted information would destroy auditability and make existing
reviews and mappings ambiguous.

## Automatic Cross-Source Claim Merging

Rejected.

Apparently equivalent statements may have different authority, wording,
constraints or context.

They remain separate and traceable to their individual sources.

## Framework Assignment Embedded Directly in the Information Unit

Rejected.

Framework mappings have their own rationale, confidence, lifecycle and
framework-version binding.

They remain separate records.

## Automatic Parent or Level Propagation

Rejected.

A semantic mapping to one node does not automatically prove relevance to its
parent, siblings or another framework level.

## Single-Agent Confidence

Rejected.

An individual LLM self-assessment is not a sufficiently auditable confidence
basis.

Confidence is derived from independent persona results.

## Treating Repeated Runs as Independent Votes

Rejected.

Repeated runs of the same persona measure stability but do not create additional
independent professional perspectives.

## Numeric Confidence as Probability

Rejected.

The persona team is not a statistically independent random sample.

The accepted confidence is an ordinal epistemic signal.

## Automatic Terminology Acceptance

Rejected.

Terminology meaning requires explicit Human-in-the-Loop governance.

## Engineering Approval During P4

Rejected.

P4 produces semantic and framework-mapping candidates.

Engineering Approval remains a separate Phase G responsibility.

Implementation Constraints

P4 implementation shall:

- preserve all P1 framework validation rules
- preserve P2 project isolation
- preserve P3 source immutability
- enforce ADR-009 textual-modality restrictions
- reject unsupported adapter behavior
- reject cross-project semantic references
- reject unknown framework targets
- reject malformed semantic identifiers
- reject direct context-only engineering contribution
- reject silent overwrites
- retain projection and semantic issues
- retain agent disagreement
- keep confidence separate from approval
- keep terminology approval separate from Engineering Approval
- remain compatible with the existing Phase F pipeline

P4 implementation shall not begin to depend on a different architecture without
a new explicitly accepted Architecture Decision Record.

Verification Criteria

P4 is not complete until automated tests demonstrate at least:

- deterministic projection for every supported adapter
- rejection of unsupported encodings
- rejection of JSON duplicate keys
- fixed CSV and TSV parsing behavior
- PDF text-layer extraction with page traceability
- visible partial and unavailable projection results
- stable Source Projection and segment identifiers
- stable Information Unit identifiers
- Information Unit atomicity validation
- same-source anchor enforcement
- rejection of cross-source Information Unit synthesis
- rejection of context-only Information Units
- validation of all Information Types
- validation of all Statement Modalities
- validation of all Epistemic Classes
- derivation-support reference validation
- assumption missing-evidence validation
- Project Concept identifier and revision validation
- preferred-label uniqueness by language
- Ambiguity Group validation
- Terminology Decision validation
- external ontology mapping validation
- framework-template version validation
- rejection of unknown framework targets
- zero-to-many Framework Assignments
- explicit unmapped, ambiguous and failed Mapping Evaluations
- no automatic framework hierarchy propagation
- deterministic consensus classification
- separation of inter-persona and intra-persona variance
- one vote per persona
- deterministic ordinal confidence derivation
- mandatory review for conflicting or low-confidence results
- immutable semantic persistence
- atomic record publication
- rejection of cross-project semantic references
- preservation of Phase F behavior
- complete project test-suite compatibility

Related Decisions

- ADR-005 — Project Workspace Architecture
- ADR-009 — Textual Source Processing Boundary
- ADR-010 — Project Source Registry Architecture

Implementation Status

Architecture accepted.

P4 implementation not started.


\newpage


# ADR-012 — Processing State and Artifact Organization


Processing State and Artifact Organization

Status

Accepted

Date

2026-07-25

Context

Phase P introduces project-oriented processing around the completed Phase F
agentic ingestion pipeline.

P1 implemented the versioned Turing RFLP Framework.

P2 implemented the persistent Project Workspace.

P3 implemented mandatory project assignment, immutable registered sources and
explicit source roles.

P4 implemented deterministic Source Projections, source-traceable Information
Units, semantic consensus, terminology mappings, Framework Assignments and
Human Review Decisions.

The P1 through P4 repositories persist their individual artifacts
independently. They do not yet provide:

- project-local Processing Run identity
- a canonical operational processing state
- an auditable state-transition history
- project-local organization of agent outputs and Consensus Reports
- retry, resumption and recovery behavior
- supersession and invalidation behavior
- source-level and project-level processing aggregation

P5 shall introduce these capabilities without replacing or duplicating the
authority of existing Project, Source or Semantic Manifests.

P5 shall not perform Approved Input Promotion, model-candidate generation,
internal model generation or SysML v2 generation.

Decision

## Source-bound Processing Runs

A Source Processing Run processes exactly one primary registered source within
exactly one project.

A Processing Run is not equivalent to one LLM invocation.

One Processing Run may contain:

- multiple processing stages
- multiple agent executions
- multiple persona runs
- multiple LLM invocations
- deterministic consensus calculations
- Human Review waits
- technical retries

Every Information Unit remains traceable to exactly one registered engineering
source.

P5 shall not synthesize one Information Unit from multiple sources.

Equivalent, overlapping or contradictory statements from different sources
remain separate, source-traceable Information Units.

Project-level comparison may later reference the results of multiple Source
Processing Runs while preserving every individual source reference.

P5 provides the operational and traceability foundation for that comparison.

Project-wide coverage, overlap and conflict analysis belongs to P6.

The human decision concerning which reviewed information becomes Approved Input
belongs to Phase G.

## Source Processing Disposition

P5 introduces explicit project-local Processing Decisions for operational
source treatment.

Supported Source Processing Dispositions are:

```text
in_scope
context_only
out_of_scope
```

`in_scope` identifies a source that remains eligible for processing according
to its registered source role.

`context_only` restricts a source to contextual and terminology use.

An effectively context-only source shall not create:

- engineering Information Units
- Framework Assignments
- preliminary engineering coverage
- approved readiness
- model-generation input

`out_of_scope` identifies a registered source that does not belong to the
currently processed system or project scope.

Out-of-scope sources remain registered and auditable. They are not deleted or
silently ignored.

A Processing Decision is an operational Human-in-the-Loop decision.

It is not:

- Engineering Approval
- Approved Input Promotion
- a framework assignment
- a terminology acceptance decision
- model-generation authority

The existing P3 Source Manifest remains authoritative for the registered source
and its stored source role.

A Processing Decision does not silently rewrite the Source Manifest.

## Processing Run Identity

Every Processing Run receives an immutable project-local identifier named
`processing_run_id`.

The identifier:

- matches `^RUN-[0-9]{6}$`
- is unique within one project
- is allocated sequentially
- is not reused
- remains unchanged for the lifetime of the run

The globally unambiguous reference is:

```text
<project_id>/<processing_run_id>
```

Example:

```text
318604/RUN-000001
```

## Run Manifest

Every Processing Run contains an immutable `run_manifest.json`.

The Run Manifest binds the run to at least:

```text
schema_version
project_id
processing_run_id
source_id
source_sha256
source_role_snapshot
workflow_profile
configuration_fingerprint
framework_template_id
framework_template_version
semantic_reference_versions
created_at
supersedes_run_id
```

`supersedes_run_id` is absent when the run has no predecessor.

The Run Manifest contains identity, scope and reproducibility bindings.

It does not contain a mutable current-state field.

## Workflow Profiles

The required stages of one Processing Run are determined by an explicit,
versioned workflow profile.

An engineering-source workflow may include:

```text
source_projection
semantic_extraction
semantic_consensus
terminology_mapping
framework_assignment
human_review
publication
```

A context-only workflow may omit engineering Information Unit extraction and
Framework Assignment.

A run shall not be considered incomplete merely because a stage that is
ineligible for its workflow profile was not executed.

## Event History

The authoritative operational state of a Processing Run is represented by an
immutable Event History.

Every state transition creates a new event.

Existing events shall not be overwritten, reordered or deleted.

Event identifiers:

- match `^EVT-[0-9]{6}$`
- are unique within one Processing Run
- are allocated sequentially
- are not reused

An event records at least:

```text
schema_version
project_id
processing_run_id
event_id
event_sequence
previous_state
next_state
processing_stage
event_type
attempt_id
reason_code
artifact_references
occurred_at
previous_event_fingerprint
event_fingerprint
```

The fingerprint chain makes missing, reordered or modified events detectable.

An artifact reference contains only the information required to identify and
validate the referenced artifact, including:

```text
artifact_type
artifact_id
content_fingerprint
repository_relative_path
```

The event does not duplicate the professional content of the referenced
artifact.

## Current-state Projection

The current Processing Run state is derived deterministically from the valid
Event History.

The derived current-state view is not an independent authority.

It may be calculated during project loading or exposed through a regenerable
derived index.

A derived index may be deleted and regenerated without loss of authoritative
processing information.

## Run States

The canonical Processing Run states are:

```text
created
running
awaiting_review
blocked
failed
completed
superseded
```

`created` means that the run has been persistently created but processing has
not started.

`running` means that an allowed processing stage is being executed.

`awaiting_review` means that an exact Human Review decision is required before
processing may continue.

`blocked` means that a known deterministic prerequisite is missing or invalid.

Examples include:

- unavailable Source Projection
- required context exceeding the token budget
- invalid mandatory references
- unresolved processing disposition
- incomplete publication recovery
- inconsistent Event History

`failed` means that a technical execution or artifact-validation failure
occurred.

`completed` means that the complete workflow required by the selected workflow
profile has been resolved.

`completed` does not mean:

- Engineering Approval
- Approved Input
- generation readiness
- model acceptance

A completely reviewed rejection may result in a completed run without
publication.

`superseded` means that an explicitly linked successor run replaces the run for
current operational use.

A superseded run remains fully available for traceability.

## Processing Stages

Run state and active Processing Stage are separate dimensions.

Supported P5 stages include:

```text
source_projection
semantic_extraction
semantic_consensus
terminology_mapping
framework_assignment
human_review
publication
```

This separation prevents stage-specific combinations from becoming independent
state values.

## Allowed State Transitions

The allowed core transitions are:

```text
created
→ running
→ blocked
→ failed
→ superseded

running
→ awaiting_review
→ blocked
→ failed
→ completed
→ superseded

awaiting_review
→ running
→ completed
→ blocked
→ superseded

blocked
→ running
→ failed
→ superseded

failed
→ running
→ superseded

completed
→ superseded

superseded
→ no further transition
```

Every transition requires an explicit reason and valid transition evidence.

## Human Review Transitions

Human Review controls operational continuation but does not replace Phase G
Engineering Approval.

The supported behavior is:

```text
confirm
→ continue processing or publication

reject
→ do not publish the rejected target
→ complete the run when all required targets are resolved

request_changes
→ return to the required processing stage
→ create a new attempt when the run bindings remain unchanged
→ create a successor run when the bindings changed
```

Only an exact decision bound to the current target and validation fingerprints
may control the corresponding transition.

Consensus, confidence and variance remain review evidence.

They shall not create an automatic state transition that bypasses Human Review.

## Project-local Artifact Organization

P5 introduces:

```text
data/projects/<project_id>/
├── runs/
│   └── RUN-000001/
│       ├── run_manifest.json
│       ├── events/
│       │   ├── EVT-000001.json
│       │   └── EVT-000002.json
│       ├── artifacts/
│       │   ├── agent_outputs/
│       │   └── consensus_reports/
│       └── work/
└── processing_decisions/
    └── PD-000001.json
```

The directories are created only when required.

## Existing Artifact Authority

Existing P2 through P4 repositories remain authoritative for their own domain
records.

These include:

```text
project_manifest.json
sources/<source_id>/source_manifest.json
sources/<source_id>/content.<suffix>
semantics/source_projections/
semantics/information_units/
semantics/project_glossary.json
semantics/terminology_decisions/
semantics/terminology_mappings/
semantics/framework_assignments/
semantics/human_reviews/
```

P5 shall not:

- move these artifacts into Processing Run directories
- copy them into Processing Run directories as a second maintained record
- rewrite their professional content
- replace their identifiers
- replace their fingerprints
- weaken their individual validation contracts

P5 references these artifacts through stable identifiers and fingerprints.

## Run-owned Artifacts

`artifacts/agent_outputs/` stores immutable technical outputs of individual
agent and persona executions.

`artifacts/consensus_reports/` stores deterministic consensus and variance
evidence.

Run-owned artifacts remain traceable to:

- project
- Processing Run
- primary source
- processing stage
- attempt
- team
- agent
- persona
- provider and model
- prompt and schema version

These technical artifacts are execution evidence.

They are not authoritative engineering information.

## Temporary Work Artifacts

`work/` contains incomplete and non-authoritative processing artifacts.

Temporary work artifacts shall not:

- satisfy run completion
- create preliminary coverage
- pass a publication gate
- be treated as published semantic records

Temporary artifacts may be retained for failure diagnosis.

They shall not be silently promoted.

## Phase F Run Boundary

Existing Phase F artifacts under:

```text
data/team_runs/
```

remain available for existing demonstrations, regression evidence and Phase F
inspection.

They do not become authoritative Project Workspace state.

New project-oriented P5 Processing Runs are stored only below their containing
project.

P5 shall not require destructive migration or deletion of existing Phase F
artifacts.

## Attempt Identity

Every retry attempt receives an immutable attempt identifier matching:

```text
^ATT-[0-9]{6}$
```

Attempts are ordered within their Processing Run and stage.

Attempt artifacts are stored separately.

Example:

```text
artifacts/agent_outputs/
└── semantic_extraction/
    ├── ATT-000001/
    └── ATT-000002/
```

No retry overwrites output from an earlier attempt.

## Retry Behavior

A retry may remain within the same Processing Run only when all material run
bindings remain unchanged.

These bindings include:

```text
source_id
source_sha256
source_role_snapshot
workflow_profile
adapter configuration
prompt and schema versions
framework-template version
semantic reference versions
```

A retry creates:

- a new Attempt
- new technical artifacts
- new state-transition events
- explicit retry rationale

A retry shall not replace or edit earlier attempts.

## Successor Runs

A material change to a run binding requires a new Processing Run.

Examples include:

- changed source content
- changed source identity
- changed effective source role
- changed workflow profile
- changed adapter configuration
- changed prompt or output schema
- changed framework-template version
- changed semantic reference version

The new Run Manifest references its predecessor through
`supersedes_run_id`.

A completed run is never reopened for a material change.

## Supersession

Supersession follows this order:

1. validate the predecessor
2. create and validate the successor Run Manifest
3. persist the successor run
4. append the supersession event to the predecessor
5. validate the complete relationship

An interrupted supersession is detected during project reopening.

It is not silently completed or ignored.

A run marked as superseded remains immutable and available.

## Artifact Lifecycle

P5 may derive the following operational lifecycle states for existing immutable
artifacts:

```text
active
superseded
invalidated
```

Lifecycle state remains separate from artifact content.

Supersession or invalidation:

- does not delete the artifact
- does not alter its content
- does not alter its original Human Review Decision
- does not turn a rejected artifact into an accepted artifact
- does not create Approved Input

A lifecycle event references the exact artifact identity and fingerprint.

## Source-disposition Changes

A Processing Decision that changes the effective treatment of a source requires
dependency analysis.

When a source becomes effectively `context_only` or `out_of_scope`, dependent
engineering runs and their use in later P6 processing are invalidated.

The affected records remain available for traceability.

A source-disposition change shall not silently reinterpret existing artifacts.

## Project Reopening

Project reopening validates at least:

- Project Manifest
- Source Manifests
- Run Manifests
- Event identities and sequences
- event fingerprint chains
- allowed state transitions
- Attempt identities
- artifact references and fingerprints
- Processing Decisions
- supersession relationships
- incomplete publication conditions

A valid history is reconstructed deterministically.

No project state is reconstructed from filenames alone.

## Recovery Behavior

Recovery is explicit and fail-closed.

A consistent history produces the derived current state.

A recoverable interrupted operation produces:

```text
blocked
```

together with an explicit recovery diagnostic.

A technical execution failure produces:

```text
failed
```

An inconsistent, incomplete or manipulated Event History produces:

```text
blocked
```

and shall not be silently repaired.

A published artifact that exists without the expected completion event is
reported as an incomplete publication.

An explicit recovery operation may validate the exact existing artifact and
complete the Event History.

Recovery shall not create a duplicate artifact.

## Source-level Aggregation

For each source, P5 identifies the current non-superseded Processing Run.

The source-processing view includes at least:

```text
processing disposition
current processing run
current run state
current processing stage
latest attempt
blocking issues
failure issues
pending Human Review
superseded runs
invalidated artifacts
```

## Project-level Aggregation

Project Processing State is derived from validated project, source, run, event
and Processing Decision records.

The supported project states are:

```text
empty
not_started
in_progress
awaiting_review
attention_required
partially_processed
processed
```

`empty` means that the project contains no registered sources.

`not_started` means that relevant sources exist but no active processing has
started.

`in_progress` means that one or more active Processing Runs are being executed.

`awaiting_review` means that an active run requires Human Review.

`attention_required` means that one or more relevant sources are blocked,
failed or inconsistent.

`partially_processed` means that some relevant sources are completed while
others remain unprocessed.

`processed` means that all relevant source-processing workflows have reached
resolved terminal outcomes.

Out-of-scope sources remain visible but do not prevent a project from becoming
processed.

## Aggregation Counts

The project aggregation exposes separate counts for at least:

```text
total sources
in-scope sources
context-only sources
out-of-scope sources
not-started sources
running sources
sources awaiting review
blocked sources
failed sources
completed sources
superseded runs
invalidated artifacts
```

A headline Project Processing State shall never hide its underlying counts and
issues.

## Aggregation Authority Boundary

Project Processing State is an operational and dashboard-oriented view.

It is not:

- preliminary coverage
- Approved Input
- approved readiness
- Engineering Approval
- generation readiness
- model-generation authority

P6 calculates Preliminary Coverage from eligible P4 records that P5 identifies
as operationally usable.

Phase G performs Approved Input Promotion using exact Human Review evidence.

## P5 Scope Boundary

P5 includes:

- Source Processing Run identity
- immutable Run Manifests
- immutable Event History
- deterministic current-state derivation
- project-local agent-output organization
- project-local Consensus Report organization
- Processing Decisions
- retry and Attempt organization
- supersession and invalidation
- reopening and explicit recovery behavior
- source-level processing aggregation
- project-level processing aggregation

P5 does not include:

- automatic multi-source Information Unit synthesis
- Approved Input Promotion
- Engineering Approval
- coverage calculation
- model-candidate creation
- internal model generation
- SysML v2 generation
- CATIA updates

Consequences

Positive consequences:

- Every processing transition remains auditable.
- Current state can be reconstructed deterministically.
- Multiple LLM and persona executions remain grouped within one source-bound
  workflow.
- Multiple sources remain independently traceable.
- Overlap and contradictions can later be compared without merging source
  authority.
- Existing P2 through P4 repositories remain stable.
- Retry does not destroy earlier execution evidence.
- Material changes produce explicit successor runs.
- Failed and interrupted processing can be diagnosed safely.
- Out-of-scope sources remain visible without blocking project progress.
- Project processing state can support the future dashboard.
- Processing completion remains separate from Engineering Approval.

Trade-offs:

- Event persistence introduces additional artifacts.
- Current-state reconstruction requires deterministic event validation.
- Retry and supersession require strict fingerprint comparison.
- Run-owned technical artifacts increase project storage.
- Recovery requires explicit operator action.
- Project aggregation must preserve detailed counts and issues.
- Source-disposition changes require dependency analysis.

Alternatives Considered

Treating one Processing Run as one LLM invocation was rejected because one
source workflow requires multiple stages, personas and deterministic analyses.

Creating multi-source Information Units was rejected because it would weaken
source traceability and make contradictory evidence difficult to review.

Storing only a mutable current-state file was rejected because it would destroy
transition, retry and recovery history.

Using only an Event History without a derived current-state view was rejected
because project reopening and the future dashboard require efficient state
access.

Copying existing semantic artifacts into Run directories was rejected because
it would duplicate authority and create synchronization risk.

Moving existing P4 artifacts into Run directories was rejected because it would
break established repositories and public contracts.

Using `data/team_runs/` as Project Workspace authority was rejected because
those artifacts are not project-isolated.

Overwriting earlier Agent Outputs during retry was rejected because it would
destroy reproducibility evidence.

Reopening a completed run for material changes was rejected because it would
make previous review and publication evidence ambiguous.

Automatically repairing invalid Event Histories was rejected because recovery
must remain explicit and auditable.

Using Project Processing State as approval or generation authority was rejected
because operational completion and Engineering Approval are separate concerns.

Affected Components

- future P5 processing-state modules
- `data/projects/<project_id>/runs/`
- `data/projects/<project_id>/processing_decisions/`
- existing P2 through P4 repositories as referenced dependencies
- future P6 coverage processing
- future P7 Project Dashboard
- P5 automated tests and phase review

Implementation Constraints

P5 implementation shall:

- preserve P2 project isolation
- preserve P3 source identity and source immutability
- preserve P4 artifact immutability
- reject cross-project run and artifact references
- reject invalid identifiers
- reject invalid state transitions
- reject broken event sequences
- reject invalid fingerprint chains
- reject silent artifact overwrite
- preserve earlier attempts
- preserve superseded and invalidated artifacts
- separate processing completion from Engineering Approval
- remain compatible with existing Phase F behavior
- avoid destructive migration of existing Phase F artifacts

Verification Criteria

P5 is not complete until automated tests demonstrate at least:

- project-local Processing Run identifier allocation
- strict Run Manifest validation
- exactly one primary source per Source Processing Run
- multiple Agent and LLM executions within one run
- workflow-profile validation
- immutable Event persistence
- deterministic event ordering
- event fingerprint-chain validation
- rejection of invalid state transitions
- deterministic current-state reconstruction
- Processing Decision validation
- source-disposition behavior
- separate Attempt persistence
- retry with unchanged bindings
- rejection of same-run retry with changed bindings
- successor-run creation for changed bindings
- supersession validation
- artifact invalidation without deletion
- recovery from incomplete publication
- blocking of inconsistent Event Histories
- source-level aggregation
- project-level aggregation
- out-of-scope exclusion from project-progress blocking
- separation of Project Processing State and approval
- preservation of P1 through P4 tests
- complete project test-suite compatibility

Supersedes

None

Related Roadmap Phase

P5 — Processing State and Artifact Organization

Related Decisions

- ADR-005 — Project Workspace Architecture
- ADR-009 — Textual Source Processing Boundary
- ADR-010 — Project Source Registry Architecture
- ADR-011 — Semantic Information Unit and Ontology Boundary

Related Implementation

Not yet implemented.


\newpage


# ADR-013 — Preliminary Coverage and Potential Model Support Assessment


Preliminary Coverage and Potential Model Support Assessment

Status

Accepted

Date

2026-07-27

Context

Phase P introduces project-oriented processing around the completed Phase F
agentic ingestion pipeline.

P1 implemented the versioned Turing RFLP Framework with three levels and twelve
stable mapping targets.

P2 implemented the persistent Project Workspace.

P3 implemented mandatory project assignment, immutable registered sources and
the explicit source roles `engineering_source` and `context_only`.

P4 implemented source-traceable Information Units, Framework Assignment
Candidates and exact Human Review Decisions.

P5 implemented project-local Processing Runs, immutable Event Histories,
Processing Decisions, artifact lifecycle, source disposition, recovery behavior
and source-level and project-level processing aggregation.

The existing artifacts establish traceable candidate evidence. They do not yet
provide a deterministic answer to the following project-level questions:

- which framework nodes have preliminary evidence
- which framework nodes remain uncovered
- which nodes contain ambiguous or conflicting evidence
- which framework levels are partially or completely covered
- which model scopes may be potentially supported by the available evidence
- whether a result is candidate evidence or reviewed candidate evidence
- whether Approved Generation Readiness is currently available

P6 introduces deterministic Preliminary Coverage and potential model-support
assessment.

P6 shall not perform:

- Approved Input Promotion
- Approved Generation Readiness assessment
- model-candidate generation
- internal model generation
- SysML v2 generation
- engineering approval
- automatic CATIA model mutation

Approved Input Promotion and Approved Generation Readiness remain assigned to
Phase G and later responsible phases.

Decision

## Assessment Authority

Preliminary Coverage is a derived, non-authoritative project assessment.

It is calculated from validated project records and may be deleted and
regenerated without loss of authoritative engineering or processing
information.

The assessment shall not replace or modify the authority of:

- the CATIA SysML v2 engineering model
- the Project Manifest
- registered Source Manifests
- Processing Run Manifests and Event Histories
- Information Unit Manifests
- Framework Assignment Candidates
- Human Review Decisions
- accepted architecture decisions

Implementation reality and derived coverage evidence do not automatically
become normative engineering requirements.

## Preliminary Coverage and Approved Readiness

P6 distinguishes strictly between:

```text
Preliminary Coverage
Approved Generation Readiness
```

Preliminary Coverage:

- is available in Phase P
- is based on eligible candidate evidence
- does not require Human Review confirmation
- may distinguish unreviewed and reviewed candidate evidence
- does not authorize Approved Input Promotion
- does not authorize model generation

Approved Generation Readiness:

- is not available in Phase P
- is not calculated by P6
- becomes available from Phase G or its responsible successor phase
- requires approved engineering input
- requires the responsible Human-in-the-Loop authority

P6 shall expose:

```text
approved_readiness_status = not_available
approved_readiness_available_from_phase = G
```

P6 shall not expose `approved_readiness = false`, because `false` could imply
that an available readiness assessment was executed and failed.

## Assessment Inputs

A project assessment may reference only validated project-local records.

Required input categories are:

```text
Framework Template
Preliminary Support Profile
registered Sources
effective Source Processing Dispositions
Processing Run and artifact lifecycle state
Framework Assignment Candidates
Human Review Decisions
```

Every referenced artifact shall remain bound through its stable identifier and
content fingerprint where the source contract provides one.

Cross-project references are invalid.

Unknown sources, framework nodes, candidates, decisions, runs or fingerprints
are invalid.

Blocking persistence or reference issues shall remain visible in the derived
assessment.

## Eligible Sources

A source may contribute Preliminary Coverage only when all of the following are
true:

1. the source belongs to the assessed project
2. the registered source role is `engineering_source`
3. the effective P5 Source Processing Disposition is `in_scope`
4. the source fingerprint matches the referenced processing and semantic
   records
5. the relevant Processing Run and artifact references are valid
6. the referenced evidence is not invalidated

A source shall not contribute Preliminary Coverage when its effective
disposition is:

```text
context_only
out_of_scope
```

A Processing Decision shall not elevate a source registered as `context_only`
into an engineering-coverage source.

Context-only and out-of-scope sources remain registered and auditable.

## Eligible Framework Assignment Evidence

One Framework Assignment Candidate may contribute Preliminary Coverage only
when all of the following are true:

1. `project_id` matches the assessed project
2. `source_id` references an eligible source
3. `information_unit_id` references the exact source-traceable Information Unit
4. the Framework Template identifier and version match the assessment template
5. `assignment_status` is `assigned`
6. every counted proposal references a valid mapping-target node
7. the candidate and its referenced artifacts are not invalidated
8. the latest exact Human Review Decision does not reject the candidate or
   request changes

The candidate confidence, consensus and variance values remain review evidence.

They shall not independently authorize:

- coverage
- publication
- Approved Input Promotion
- generation readiness
- model generation

Confidence, consensus and variance may be displayed as supporting evidence but
shall not be converted into a numeric maturity or readiness score.

## Non-covering Assignment States

Framework Assignment Candidates with these states shall not create coverage:

```text
unassigned
ambiguous
conflict
```

Their interpretation is:

```text
unassigned
→ explicit uncovered evidence

ambiguous
→ no coverage and attention evidence

conflict
→ no coverage and attention evidence
```

A node may have valid covering evidence and separate ambiguous or conflicting
evidence at the same time.

Coverage and attention are therefore separate dimensions.

## Human Review Resolution

Preliminary Coverage does not require Human Review confirmation.

The latest exact Human Review Decision bound to the candidate content and
reference-validation fingerprints determines the review state of that
candidate.

The effects are:

```text
no exact decision
→ unreviewed candidate evidence
→ may create candidate coverage

confirm
→ reviewed candidate evidence
→ may create reviewed candidate coverage

reject
→ excluded from coverage

request_changes
→ excluded from coverage
→ attention required
```

A confirmation in P6 confirms only the exact Framework Assignment Candidate.

It does not mean:

- Approved Input
- Engineering Approval
- Approved Generation Readiness
- model acceptance
- generation authorization

Stale decisions bound to an older candidate or validation fingerprint shall not
control current coverage.

## Framework Node Coverage

P6 assesses every mapping-target node defined by the active Framework Template.

The canonical node coverage states are:

```text
uncovered
candidate_covered
reviewed_candidate_covered
```

`uncovered` means that no eligible covering candidate evidence exists.

`candidate_covered` means that at least one eligible unreviewed Framework
Assignment Candidate maps an Information Unit to the node.

`reviewed_candidate_covered` means that at least one eligible Framework
Assignment Candidate with a latest exact `confirm` decision maps an Information
Unit to the node.

When both reviewed and unreviewed eligible evidence exist,
`reviewed_candidate_covered` is the displayed coverage state while all evidence
counts remain available.

Attention is represented separately:

```text
attention_required = true | false
```

Attention may result from:

- ambiguous assignment evidence
- conflicting assignment evidence
- `request_changes`
- invalid or stale references
- invalidated evidence
- blocking source, processing, assignment or review issues
- multiple incompatible current evidence chains

One node may therefore be covered and require attention simultaneously.

## Framework Node Coverage Record

The derived Framework Node Coverage record contains at least:

```text
framework_node_id
mapping_key
node_name
level_node_id
coverage_state
attention_required
eligible_source_count
information_unit_count
assignment_candidate_count
confirmed_candidate_count
unreviewed_candidate_count
rejected_candidate_count
ambiguous_candidate_count
conflicting_candidate_count
source_ids
information_unit_ids
framework_assignment_candidate_ids
human_review_decision_ids
issue_codes
```

Identifiers shall be unique and deterministically sorted.

Counts shall equal the corresponding unique reference sets.

Rejected or request-changes candidates remain auditable but shall not be counted
as covering candidates.

## Framework Level Coverage

The Framework Template defines three assessment levels:

```text
Stakeholder Level
System Level
Subsystem Level
```

Each level contains four mapping-target nodes.

The canonical level coverage states are:

```text
uncovered
partially_covered
covered
```

`uncovered` means that none of the level's mapping-target nodes has candidate or
reviewed candidate coverage.

`partially_covered` means that at least one but not all mapping-target nodes
have candidate or reviewed candidate coverage.

`covered` means that every mapping-target node in the level has candidate or
reviewed candidate coverage.

The level assessment contains at least:

```text
level_node_id
level_name
coverage_state
covered_node_count
total_node_count
candidate_covered_node_count
reviewed_candidate_covered_node_count
attention_node_count
covered_node_ids
uncovered_node_ids
attention_node_ids
```

P6 shall not calculate a weighted maturity or readiness percentage.

A UI may display exact ratios such as:

```text
3 of 4 framework nodes preliminarily covered
```

## Project Coverage

The project assessment aggregates all Framework Node and Framework Level
Coverage records.

The canonical project coverage states are:

```text
uncovered
partially_covered
covered
attention_required
```

The displayed project state follows this precedence:

1. `attention_required` when a blocking assessment issue exists
2. `covered` when every mapping-target node is covered and no blocking issue
   exists
3. `partially_covered` when at least one but not every node is covered
4. `uncovered` when no node is covered

Non-blocking node attention remains visible even when the overall project state
is `partially_covered` or `covered`.

The project assessment shall preserve exact counts and identifiers rather than
derive a synthetic maturity score.

## Preliminary Support Profile

Potential model support shall be defined by a separate versioned profile.

The initial profile is:

```text
context/frameworks/turing_preliminary_support_profile.json
```

The profile is not engineering-model authority.

It defines deterministic dependency rules for the derived P6 support
assessment.

The profile shall bind at least:

```text
schema_version
profile_id
profile_version
framework_template_id
framework_template_version
support_targets
```

Each support target contains at least:

```text
support_target_id
name
support_target_type
required_framework_node_ids
required_support_target_ids
```

Unknown or duplicate references are invalid.

A profile bound to another Framework Template identifier or version is invalid.

## Conservative Support Chain

The initial potential-support chain is:

```text
Stakeholder Model
→ System Model
→ Subsystem Model
```

### Stakeholder Model

Potential support requires coverage for:

```text
Stakeholders
User Needs
Stakeholder Requirements
Use Cases
```

### System Model

Potential support requires:

```text
potential Stakeholder Model support
System Requirements
System Functional
System Logical
System Physical
```

### Subsystem Model

Potential support requires:

```text
potential System Model support
Subsystem Requirements
Subsystem Functional
Subsystem Logical
Subsystem Physical
```

The dependency chain is conservative and monotonic.

A downstream support target shall not become potentially supported while an
upstream required support target is not potentially supported.

## Potential Support States

The canonical potential-support states are:

```text
not_supported
partially_supported
potentially_supported
attention_required
```

`not_supported` means that none of the directly required framework-node
coverage exists and no required upstream support target is satisfied.

`partially_supported` means that some but not all direct and upstream
requirements are satisfied.

`potentially_supported` means that every direct framework-node requirement and
every required upstream support target is preliminarily satisfied.

`attention_required` means that the support dependencies would otherwise be
satisfied but blocking or conflicting evidence prevents an unqualified support
indication.

`potentially_supported` shall always be interpreted as:

> The versioned Preliminary Support Profile requirements possess eligible
> Preliminary Coverage evidence.

It shall not be interpreted as:

- complete
- correct
- validated
- approved
- generation-ready
- accepted by the engineering authority

## Support Assessment Record

A derived potential-support record contains at least:

```text
support_target_id
name
support_target_type
support_state
required_framework_node_ids
covered_framework_node_ids
missing_framework_node_ids
required_support_target_ids
satisfied_support_target_ids
unsatisfied_support_target_ids
attention_required
issue_codes
```

All identifiers shall be unique and deterministically sorted.

## Issue Handling

P6 issue levels are:

```text
warning
blocking
```

Warnings remain visible but do not automatically suppress valid coverage.

Blocking issues prevent the affected evidence from contributing to coverage.

A project-wide blocking integrity or reference issue changes the displayed
project coverage state to `attention_required`.

Representative blocking issue categories include:

- mixed project references
- unknown source
- unknown Information Unit
- unknown Framework Assignment Candidate
- unknown Framework node
- Framework Template version mismatch
- Preliminary Support Profile mismatch
- source fingerprint mismatch
- invalidated current evidence
- stale exact-review binding
- inconsistent duplicate identities
- invalid support dependency graph
- support dependency cycle

## Determinism and Ordering

All derived records shall use stable ordering.

Unless a stricter domain order is defined:

- framework levels follow Framework Template order
- framework nodes follow parent level and node order
- sources follow `source_id`
- Information Units follow `information_unit_id`
- Framework Assignment Candidates follow their identifiers
- Human Review Decisions follow their identifiers
- support targets follow profile order
- issue codes and identifier sets are sorted deterministically

Equivalent validated inputs shall produce exactly equivalent derived
assessments.

## Assessment Fingerprint

Every project assessment shall contain an
`assessment_input_fingerprint`.

The fingerprint binds the canonical representation of at least:

```text
Framework Template identifier and version
Preliminary Support Profile identifier and version
eligible registered Source identities and fingerprints
effective Source Processing Dispositions
relevant Processing Run and artifact lifecycle identities
Framework Assignment Candidate identities and content fingerprints
latest exact Human Review Decision identities and fingerprints
assessment algorithm identifier and version
```

The fingerprint is evidence of reproducibility.

It is not a mutable project status and does not become an independent authority.

## Persistence

P6 shall not persist a mutable current Coverage state.

Coverage and potential-support assessments are calculated on demand by:

```text
ProjectCoverageService.assess_project(project_id)
```

A caller may serialize or cache a complete derived assessment for UI or report
purposes only when:

- the derived status is explicitly marked non-authoritative
- the assessment input fingerprint is preserved
- the cache may be deleted and regenerated
- stale caches are never treated as current authority

P7 may use the P6 assessment as a read-only dashboard projection.

## Module Structure

The initial P6 module structure is:

```text
modules/project_coverage/
├── __init__.py
├── errors.py
├── types.py
├── profile.py
├── evidence.py
├── coverage.py
├── support.py
└── service.py
```

Responsibilities are:

```text
errors.py
→ P6 validation, reference, integrity and assessment errors

types.py
→ immutable coverage, support, issue and assessment types

profile.py
→ versioned Preliminary Support Profile parsing and validation

evidence.py
→ candidate eligibility and latest exact Human Review resolution

coverage.py
→ node, level and project Preliminary Coverage

support.py
→ potential Model and SubModel support

service.py
→ project-local repository scans and complete assessment assembly
```

The public package API is added only after the internal contracts are stable.

## Implementation Sequence

P6 implementation follows:

```text
P6-A1  Architecture and ADR-013
P6-I1  Errors, types and Preliminary Support Profile
P6-I2  Evidence eligibility and Human Review resolution
P6-I3  Node, level and project Preliminary Coverage
P6-I4  Preliminary Model and SubModel support
P6-I5  Coverage Service and public API
P6-I6  Integration, regression and P6 acceptance
```

Implementation staging and commit may be deferred until the complete P6
implementation is ready, except that this accepted architecture decision shall
be recorded before implementation depends on it.

Consequences

Positive consequences:

- Preliminary Coverage becomes deterministic and reproducible.
- Coverage remains distinct from Approved Generation Readiness.
- Context-only and out-of-scope sources cannot silently create engineering
  evidence.
- Ambiguity and conflict remain visible without being misrepresented as
  coverage.
- Human Review improves evidence classification without becoming model
  generation authority.
- Model and SubModel support indications are based on an explicit versioned
  dependency profile.
- P7 receives a stable read-only data contract for dashboard visualization.
- Existing P1 through P5 authority boundaries remain unchanged.

Trade-offs:

- The conservative support chain may understate potential support.
- A single uncovered required node prevents `potentially_supported`.
- Coverage does not measure semantic completeness inside one framework node.
- The absence of weighted scoring provides less visual simplicity but avoids
  false precision.
- Recalculation requires scanning several validated project repositories.

Risks and mitigations:

- Risk: candidate evidence may be misunderstood as approved readiness.
  Mitigation: explicit state names and `approved_readiness_status =
  not_available`.

- Risk: stale Human Review decisions could affect current coverage.
  Mitigation: only the latest exact content- and validation-fingerprint binding
  is relevant.

- Risk: invalidated artifacts remain referenced by old candidates.
  Mitigation: P5 lifecycle validation excludes invalidated current evidence.

- Risk: support rules become hidden implementation logic.
  Mitigation: versioned Preliminary Support Profile.

- Risk: generated assessment caches become a second authority.
  Mitigation: derived, regenerable, fingerprint-bound and non-authoritative
  cache rules.

Rejected Alternatives

## Count only confirmed Framework Assignment Candidates

Rejected because Preliminary Coverage is explicitly available without Human
Approval.

This alternative would collapse Preliminary Coverage into a later approval
concept.

## Count every Framework Assignment Candidate

Rejected because `unassigned`, `ambiguous`, `conflict`, rejected and
request-changes candidates do not provide valid covering evidence.

## Confidence-weighted coverage score

Rejected because confidence is review evidence and not an authority boundary.

A weighted percentage would imply a precision and engineering maturity model
that has not been defined.

## Persist mutable Coverage status

Rejected because derived coverage could become stale and duplicate the
authority of existing project records.

## Calculate Approved Generation Readiness in P6

Rejected because Approved Generation Readiness is unavailable in Phase P and
belongs to Phase G or its responsible successor phase.

## Infer potential model support directly in the dashboard

Rejected because support dependencies would become hidden UI logic.

The rules belong in a versioned support profile and deterministic P6 service.

## Allow System or Subsystem support without upstream support

Rejected for the initial profile.

The accepted initial profile uses the conservative dependency chain:

```text
Stakeholder Model
→ System Model
→ Subsystem Model
```

Future profiles may define different support targets only through an explicit
versioned architecture and validation change.


\newpage


# ADR-014 — Project Dashboard Architecture and Evidence Navigation


Project Dashboard Architecture and Evidence Navigation

Status

Accepted

Date

2026-07-27

Amendments

2026-07-27 – First-Project Workspace Bootstrap

2026-07-27 – Project Creation Availability with Existing Workspaces

2026-07-27 – Phase-P P9 Project-bound Ingestion Integration Boundary

Context

Phase P introduces a project-oriented engineering workspace around the completed
Phase F agentic ingestion pipeline.

P1 implemented the versioned Turing RFLP Framework.

P2 implemented persistent Project Workspaces and six-digit project identities.

P3 implemented immutable registered Sources with explicit source roles.

P4 implemented source-traceable Information Units, semantic candidates,
Framework Assignment Candidates and exact Human Review Decisions.

P5 implemented immutable Processing Runs, Event Histories, Processing Decisions,
artifact lifecycle, source disposition and project processing aggregation.

P6 implemented deterministic Preliminary Coverage and potential model-support
assessment.

P7 introduces a read-only Project Dashboard for navigating the current project
state and its supporting evidence.

Phase P was subsequently extended with P9, Project-bound Agentic Ingestion
Integration. P9 follows the P8 Tests and Integration Readiness Review and
connects the existing Phase F ingestion capability to the project-oriented
contracts established by P2-P7. This amendment defines only the P7 integration
boundary. P9 requires its own architecture decision before implementation.

The existing Team Agentic Ingestion UI remains an execution-oriented interface.
It selects legacy inputs, configures ingestion runs, starts the pipeline and
browses produced artifacts. It is not the project-level dashboard introduced by
P7.

The dashboard must answer the following questions without creating a new source
of truth:

- which project is currently selected
- which Sources are registered and how they are classified
- what the current project and source processing states are
- which framework nodes and levels have Preliminary Coverage
- which model and submodel scopes are potentially supported
- which issues require attention
- which Human Review Decisions affect the displayed state
- which exact artifacts and documents support every displayed result
- how the user can open those supporting artifacts directly from the dashboard

The dashboard shall be visually concise and shall use color only to communicate
status.

Decision

## Dashboard Authority

The Project Dashboard is a derived, read-only presentation layer.

It shall not become an authority for:

- engineering requirements
- CATIA SysML v2 model content
- project identity
- Source registration
- Processing State
- Information Units
- semantic candidates
- Framework Assignment Candidates
- Human Review Decisions
- Preliminary Coverage
- potential model support
- Approved Generation Readiness

The dashboard may be deleted and regenerated without loss of authoritative
engineering, semantic or processing information.

The dashboard shall obtain its information exclusively through the existing
P2-P6 public services, repositories and immutable data types.

The UI shall not reimplement business rules already owned by P2-P6.

## Read-only Scope

The five Project Dashboard views shall provide navigation and presentation only.

The application shell may expose one constrained P2 Project Workspace creation
action. It is presented prominently when no valid Project Workspace exists and
remains available as a secondary action when existing Project Workspaces can be
selected.

P7 shall not:

- modify or delete an existing Project Workspace
- accept a manually chosen Project ID
- register or modify Sources
- start, retry, supersede or mutate Processing Runs
- create or modify Information Units
- create or modify semantic candidates
- create or modify Framework Assignment Candidates
- create or modify Human Review Decisions
- promote Approved Inputs
- calculate Approved Generation Readiness
- generate model candidates
- generate SysML v2
- mutate CATIA
- persist mutable dashboard state as project authority

The existing Team Agentic Ingestion UI remains separate and unchanged during P7.
Any adaptation that binds ingestion to a Project Workspace belongs to P9 and
shall be governed by a separate architecture decision.

The only P7 write exception is the Project Workspace creation action. It shall:

- call the existing P2 `ProjectWorkspace.create_project` contract
- require a human-readable display name
- allow an optional description
- generate the six-digit Project ID through P2
- pin the accepted Framework Template through the P2 Project Manifest
- remain available after one or more valid Project Workspaces exist
- create a new Project Workspace without modifying an existing one
- create no Sources, Processing Runs, semantic artifacts or review decisions

After successful Project Workspace creation, every Project Dashboard view remains
read-only.

Beginning with P9, the application shell may expose project-bound navigation
from the dashboard to a separately governed ingestion execution view and back.
The P7 dashboard views themselves remain read-only. Navigation does not authorize
the dashboard presenter or renderer to register Sources, start Processing Runs
or publish ingestion artifacts.

## Dashboard Composition

The initial Project Dashboard contains five primary views:

```text
Project Overview
Preliminary Coverage
Sources and Processing
Attention and Review
Traceability and Documents
```

The selected project remains visible across all views.

### Project Overview

The overview presents a compact project summary:

- Project display name
- six-digit Project ID
- Framework Template identifier and version
- Preliminary Support Profile identifier and version
- registered Source count
- project processing state
- project Preliminary Coverage state
- attention summary
- potential Stakeholder Model support
- potential System Model support
- potential Subsystem Model support
- explicit Approved Generation Readiness unavailability

The overview shall not display a synthetic maturity score.

Exact ratios may be displayed, for example:

```text
3 of 4 System framework nodes preliminarily covered
```

### Preliminary Coverage

The coverage view presents:

- Stakeholder, System and Subsystem framework levels
- all twelve stable framework mapping targets
- node coverage state
- level coverage state
- reviewed and unreviewed candidate counts
- eligible Source and Information Unit counts
- independent attention state
- related issue codes
- direct evidence navigation

Canonical node states remain owned by P6:

```text
uncovered
candidate_covered
reviewed_candidate_covered
```

Canonical level states remain owned by P6:

```text
uncovered
partially_covered
covered
```

Coverage and attention shall remain separate dimensions.

### Sources and Processing

The processing view presents each registered Source with:

- Source ID
- original filename
- source role
- content fingerprint
- effective processing disposition
- current Processing Run ID
- run state
- processing stage
- latest Attempt ID
- pending review state
- blocking issue codes
- failure issue codes
- superseded Run IDs
- invalidated artifact count
- direct Source Manifest and processing-history navigation

The dashboard shall not infer a processing state that is not supplied by P5.

### Attention and Review

The attention view presents:

- blocking and warning issues
- affected project, Source, Information Unit, candidate, node or support target
- Human Review target type
- Human Review decision
- decision identity
- exact target content fingerprint
- exact reference-validation fingerprint
- reviewer identity where available
- direct navigation to affected artifacts and decisions

A Human Review confirmation shown in P7 confirms only the exact reviewed target.

It shall not be presented as:

- Approved Input
- Engineering Approval
- Approved Generation Readiness
- model acceptance
- generation authorization

### Traceability and Documents

The traceability view presents the evidence chain behind dashboard results.

A typical chain is:

```text
Source
→ Processing Run and Events
→ Information Unit
→ Framework Assignment Candidate
→ Reference Validation
→ Human Review Decision
→ Preliminary Coverage
→ Potential Model Support
```

The view shall allow navigation in both directions where exact references exist.

## Smart Evidence Navigation

Every dashboard element that presents a traceable project fact shall be capable
of exposing the exact supporting artifacts.

Examples include:

- a project processing state
- a Source disposition
- a framework-node coverage state
- an attention indicator
- a support assessment
- a Human Review status
- an issue

The presenter layer shall bind such values to immutable Evidence References.

An Evidence Reference contains at least:

```text
reference_type
reference_id
display_label
repository_relative_path
content_fingerprint
media_type
source_role
relationship
```

Optional navigation metadata may include:

```text
section_anchor
line_start
line_end
json_pointer
table_row_key
```

The dashboard shall not construct arbitrary filesystem paths in the UI.

Evidence paths shall be resolved by trusted repository-aware resolvers.

Each resolved path must:

- remain within the configured repository or Project Workspace root
- reject symbolic links where the owning repository rejects them
- correspond to an existing authoritative or derived artifact
- preserve project isolation
- preserve the artifact identity and fingerprint where available

### One Supporting Artifact

When exactly one Evidence Reference supports a displayed value, activating the
navigation control shall open that artifact directly in the internal document
viewer.

### Multiple Supporting Artifacts

When multiple Evidence References support a displayed value, activating the
navigation control shall open a compact evidence chooser.

The chooser shall:

- identify the relationship of each artifact to the displayed value
- show artifact type, stable ID and concise label
- preserve deterministic ordering
- distinguish direct evidence from contextual evidence
- allow one artifact to be opened without leaving the dashboard context

The chooser shall not silently choose one artifact when several materially
contribute to the result.

### No Supporting Artifact

When a displayed value has no resolvable supporting artifact, the dashboard
shall show that evidence navigation is unavailable.

It shall not create or guess a path.

Missing expected evidence shall be visible as an issue where the responsible
P2-P6 contract classifies it as invalid or blocking.

## Internal Document Viewer

The dashboard shall use an internal document viewer rather than browser
`file://` links.

This avoids platform-specific file access, browser security restrictions and
uncontrolled navigation outside the application.

The viewer shall support at least:

```text
JSON
Markdown
plain text
CSV
```

Viewer behavior:

- JSON is displayed in formatted, readable form
- Markdown is rendered and may also be shown as source text
- plain text preserves line structure
- CSV may be displayed as a table and as raw text
- unsupported binary content shows metadata and an explicit file action
- large files use bounded previews and explicit expansion
- fingerprints and repository-relative paths remain visible
- opening a document never changes its review or approval state

Where optional navigation metadata is available, the viewer may highlight:

- a Markdown section
- a line range
- a JSON field or object
- a table row

Highlighting is a navigation aid only and does not create a new persisted
reference authority.

## Navigation Context

Opening evidence shall preserve the user's dashboard context.

The application shall retain at least:

```text
selected project
current dashboard view
selected node, source, issue or support target
opened evidence reference
```

The user shall be able to return to the previous dashboard location without
reconstructing filters manually.

Deep links may use application query parameters for navigation state.

Deep links shall contain stable project and artifact identities, not unrestricted
filesystem paths.

## Dashboard Presentation Model

A new `modules.project_dashboard` package shall separate data collection,
presentation and Streamlit rendering.

The planned package is:

```text
modules/project_dashboard/
├── __init__.py
├── errors.py
├── types.py
├── references.py
├── presenter.py
└── service.py
```

The Streamlit layer is planned as:

```text
app/project_dashboard_ui.py
app/project_dashboard_app.py
```

### Dashboard Service

The dashboard service coordinates read-only calls to P2-P6.

It shall return a complete immutable dashboard snapshot.

It shall not expose repository objects directly to the UI.

### Presenter

The presenter transforms domain records into display-ready immutable view
models.

The presenter may:

- define stable ordering
- format concise labels
- group related records
- bind Evidence References
- derive display-only counts from already validated records
- select status labels and status semantics

The presenter shall not:

- recalculate Preliminary Coverage
- recalculate potential model support
- change issue severity
- infer approval
- infer missing evidence
- change processing state

### Streamlit UI

The Streamlit UI renders the supplied view models.

The UI shall contain minimal domain logic.

UI event handlers may:

- change the selected project
- change the active dashboard view
- open an Evidence Reference
- choose among multiple Evidence References
- apply presentation filters
- request project-bound navigation to or from a P9 execution view

Dashboard-view event handlers shall not mutate P2-P6 project artifacts. The
constrained Project Workspace creation action remains the sole P7 write
exception. Any P9 execution action is owned by the separately governed P9
integration layer, not by the dashboard view or presenter.

## Project Selection and Project Creation

When no valid Project Workspace exists, the application shall present a prominent
first-project form instead of a terminal empty state.

When one or more valid Project Workspaces exist, the same creation capability
shall remain available as a secondary, collapsed action alongside the
deterministic project selector.

The form shall contain:

```text
Project name
Description (optional)
Create project
```

The user shall not enter the internal Project ID. P2 generates the six-digit identifier and persists the Project Manifest atomically.

After successful creation, the application shall select the new project, open the Overview and rerun the presentation layer.

The dashboard shall list existing Project Workspaces.

A project option shall display:

```text
<display name> · <six-digit Project ID>
```

The six-digit ID remains the stable internal project identity.

Project selection shall be deterministic.

An invalid or unsafe Project Workspace shall not be silently presented as a
valid project.

Project scan issues shall remain visible.

## P9 Navigation and Integration Boundary

P9 may connect the application shell to a separately defined project-bound
Agentic Ingestion execution view. The integration shall preserve the separation
between inspection and execution:

```text
Project Dashboard
→ read-only inspection and evidence navigation

Project-bound Agentic Ingestion
→ explicit execution workflow governed by P9
```

The application shell may expose navigation controls such as:

```text
Start ingestion for this project
Return to Project Dashboard
```

A navigation request shall carry only stable application identities and state,
at least:

```text
project_id
return_view
optional selected entity identity
```

It shall not carry unrestricted filesystem paths.

The selected six-digit Project ID is mandatory for project-bound ingestion. P9
shall not silently fall back to a global, unassigned or different project when a
project binding is unavailable or invalid.

The separately governed P9 execution layer may coordinate existing authoritative
contracts, including:

- P3 Project Source Registry for Source registration and Source role assignment
- P5 Processing operations for Processing Run and event persistence
- the existing Phase F Team Agentic Ingestion pipeline as an execution engine
- project-local publication of traceable reports and agent outputs

ADR-014 does not authorize or define those write operations. Their exact
transaction boundaries, failure behavior, artifact mapping and recovery rules
shall be specified in the P9 architecture decision.

After an ingestion execution returns to the dashboard, the application shall
discard any stale dashboard snapshot and regenerate the selected project's view
from the P2-P6 authorities. The dashboard shall not accept execution results
directly as presentation truth.

A completed ingestion run shall not create or imply Preliminary Coverage unless
the required valid P4 Information Units, Framework Assignment Candidates,
reference validations and Human Review Decisions actually exist. Processing
visibility and Preliminary Coverage remain separate states.

The intended demonstrator flow is:

```text
Create or select Project
→ start project-bound ingestion
→ register Source
→ execute ingestion
→ persist Processing evidence
→ return to Dashboard
→ regenerate and inspect project state
```

## Visual Design Principles

The dashboard shall be visually concise, calm and information-dense without
being cramped.

The default visual language shall use:

- neutral backgrounds
- neutral borders
- whitespace
- typography
- hierarchy
- alignment
- concise labels
- restrained icons

Color shall be used only to communicate status.

Color shall not be used merely for decoration, section identity, navigation or
branding emphasis.

Large decorative gradients, multicolored cards and unrelated accent colors are
out of scope.

### Status Semantics

The initial status families are:

```text
neutral
informational
candidate
reviewed
attention
blocking
unavailable
```

Status presentation shall combine:

```text
text label
icon or shape
color
```

Color shall never be the only carrier of meaning.

Suggested semantic intent:

```text
neutral or unavailable
→ gray

informational or unreviewed candidate
→ blue

reviewed candidate or covered
→ green

partial coverage or attention
→ amber

blocking, rejected or invalid
→ red
```

The exact color tokens belong to the UI implementation and tests.

Status colors shall be applied only to compact status-bearing elements such as:

- badges
- small indicators
- icons
- narrow status borders
- status text

Entire cards, pages or large table regions shall not be filled with status
colors.

The dashboard shall remain understandable in monochrome and for users with
color-vision deficiencies.

## Progressive Disclosure

The dashboard shall present summary before detail.

The initial view shows concise project-level status.

Detailed records are revealed through:

- expandable sections
- filtered tables
- evidence navigation
- the internal document viewer

The dashboard shall avoid displaying all identifiers and fingerprints in the
primary summary.

Exact identifiers, fingerprints and paths remain available in detailed views.

## Status Language

The dashboard shall preserve the exact meaning of P5 and P6 states.

The UI may provide concise explanatory text, but it shall not rename a state in
a way that changes its meaning.

In particular:

```text
potentially_supported
```

shall never be displayed as:

```text
ready
approved
complete
valid
generation-ready
```

The dashboard shall visibly expose:

```text
approved_readiness_status = not_available
approved_readiness_available_from_phase = G
```

The preferred user-facing explanation is:

```text
Approved Generation Readiness is not assessed in Phase P.
It becomes available from Phase G.
```

## Determinism

Equivalent project records shall produce equivalent dashboard snapshots.

Stable ordering shall be defined for:

- projects
- framework levels
- framework nodes
- Sources
- Processing Runs
- issues
- Human Review Decisions
- support targets
- Evidence References

The dashboard shall not depend on filesystem iteration order.

## Failure Behavior

The dashboard shall fail closed.

A failure to resolve one section shall not cause another section to invent data.

Where possible, the dashboard may render a partial snapshot with explicit
issues.

Examples:

```text
Coverage assessment unavailable
Source scan available
Processing scan available
```

A section failure shall show:

- affected section
- concise error
- relevant issue code
- available evidence navigation
- no fabricated status

Unexpected exceptions shall be converted into safe presentation errors before
reaching raw Streamlit output.

## Testing Strategy

P7 shall include tests for:

- immutable dashboard types
- project selection ordering
- presenter determinism
- status-label mapping
- status-color restriction
- project isolation
- Evidence Reference validation
- safe repository-relative path resolution
- single-evidence direct navigation
- multiple-evidence chooser behavior
- missing-evidence behavior
- internal viewer selection
- section and line navigation metadata
- source and processing presentation
- coverage and support presentation
- attention and Human Review presentation
- partial failure behavior
- public API exports
- Streamlit-independent rendering contracts

Core dashboard logic shall be testable without starting Streamlit.

Streamlit tests shall focus on thin integration behavior rather than duplicate
domain tests.

## Implementation Sequence

P7 is implemented in six steps:

```text
P7 Step 1 of 6
Architecture and ADR-014

P7 Step 2 of 6
Dashboard types, Evidence References and presenter foundation

P7 Step 3 of 6
Project selection and Project Overview

P7 Step 4 of 6
Processing, Coverage and Potential Support views

P7 Step 5 of 6
Attention, Human Review, traceability and document viewer

P7 Step 6 of 6
UI integration, tests, full regression and P7 acceptance
```

P8 performs the Tests and Integration Readiness Review for P1-P7. P9 then
implements Project-bound Agentic Ingestion Integration under its own ADR. This
extension of Phase P does not change the six-step P7 implementation sequence.

Consequences

Positive consequences:

- project status becomes accessible through one coherent interface
- every relevant displayed result can lead to its supporting artifacts
- multiple evidence chains remain explicit rather than hidden
- P2-P6 remain authoritative
- the existing ingestion UI remains stable during P7
- P7 provides a defined navigation seam for the later P9 integration
- P9 can update project state without moving execution logic into dashboard views
- the visual design communicates status without decorative noise
- the dashboard remains suitable for demonstration and technical inspection
- traceability becomes directly explorable
- core behavior remains testable without Streamlit

Negative consequences:

- evidence navigation requires additional immutable view-model types
- repository-aware path resolvers add implementation effort
- the internal viewer must safely handle multiple text formats
- deep-link and navigation context require explicit UI state handling
- the dashboard views cannot provide write actions during P7
- P9 requires a separate ADR and explicit adaptation of the existing ingestion flow
- the integrated demonstrator remains split between inspection and execution views
- some records may initially support only artifact-level rather than exact
  line-level navigation

Rejected Alternatives

### Merge P7 directly into the existing ingestion UI

Rejected because execution configuration and project-state inspection have
different responsibilities. Merging them would create a large UI with mixed
read and write semantics.

### Embed P9 execution controls inside dashboard views

Rejected because navigation from a dashboard view to an execution workflow is
not equivalent to making the view itself writable. Source registration,
Processing Run creation and ingestion execution belong to the separately
governed P9 execution layer.

### Treat navigation alone as project-bound ingestion integration

Rejected because a link between two screens would not bind Sources, Processing
Runs or generated artifacts to the selected Project Workspace. P9 must implement
the repository and lifecycle bridge, not only navigation.

### Use direct `file://` links

Rejected because browser restrictions, platform differences and uncontrolled
filesystem access make them unreliable and unsafe.

### Open only repository-relative paths as plain text labels

Rejected because the user must be able to navigate directly to supporting
documents rather than manually locate them.

### Automatically open the first artifact when multiple artifacts contribute

Rejected because this would hide material evidence and could imply that one
artifact alone supports the displayed result.

### Recalculate coverage in the dashboard

Rejected because P6 is the sole owner of Preliminary Coverage and potential
model-support assessment.

### Use decorative colors for dashboard sections

Rejected because color is reserved for status semantics.

### Add approval and review actions to P7

Rejected because P7 dashboard views are read-only. Write workflows require
their own explicit architecture and authority boundaries.

### Leave the initial empty state without a project bootstrap

Rejected because the application would have no valid starting action in a fresh repository. The constrained P2 bootstrap creates only the Project Workspace boundary and does not weaken the read-only status of dashboard views.

### Add full project lifecycle management to P7

Rejected because editing, deleting or arbitrarily managing existing projects would exceed the minimal bootstrap exception and mix project administration with evidence presentation.

Acceptance Criteria

ADR-014 is satisfied when:

1. the five dashboard views are read-only and the only write action is constrained P2 Project Workspace creation
2. P2-P6 remain the sole domain authorities
3. every traceable displayed value can expose Evidence References
4. one Evidence Reference opens directly
5. multiple Evidence References produce an explicit chooser
6. arbitrary filesystem paths cannot be opened
7. an internal viewer supports JSON, Markdown, text and CSV
8. project and navigation context are preserved
9. status color is used only for status-bearing elements
10. status meaning is also conveyed through text and icon or shape
11. the dashboard exposes Preliminary Coverage and potential support accurately
12. Approved Generation Readiness remains explicitly unavailable in Phase P
13. the existing Team Agentic Ingestion UI remains unchanged during P7, and any P9 adaptation is governed by a separate ADR
14. core dashboard behavior is testable without Streamlit
15. the complete repository regression remains green
16. a fresh repository can create its first Project Workspace without a manually entered Project ID
17. an additional Project Workspace can be created while existing Project Workspaces remain selectable
18. successful Project Workspace creation selects the new project and opens the Overview
19. beginning with P9, the application shell may navigate to a separately governed execution view while dashboard views remain read-only
20. P9 navigation carries a valid selected Project ID and no unrestricted filesystem path
21. returning from P9 regenerates the dashboard snapshot from P2-P6 authorities
22. ADR-014 does not authorize P9 Source registration, Processing Run creation or artifact publication


\newpage


# ADR-015 — Project-bound Agentic Ingestion Integration Architecture


Project-bound Agentic Ingestion Integration Architecture

Status

Accepted

Date

2026-07-27

## Context

Phase F implemented a working Team Agentic Ingestion pipeline and a standalone
execution-oriented Streamlit interface.

Phase P introduced the project-oriented engineering workspace:

- P1 implemented the versioned Turing RFLP Framework Template.
- P2 implemented persistent Project Workspaces and six-digit Project IDs.
- P3 implemented immutable registered Sources with mandatory Project assignment
  and explicit Source roles.
- P4 implemented project-local source projections, Information Units, semantic
  candidates, terminology mappings, Framework Assignment Candidates and Human
  Review Decisions.
- P5 implemented immutable Processing Runs, Processing Events, Processing
  Decisions, artifact lifecycle, retry, supersession, recovery diagnostics and
  project-level Processing aggregation.
- P6 implemented deterministic Preliminary Coverage and potential model-support
  assessment.
- P7 implemented the read-only Project Dashboard with evidence navigation and a
  constrained Project Workspace creation action.
- P8 verified the P1-P7 implementation baseline and confirmed that the existing
  public contracts can support a project-bound ingestion integration without a
  parallel project or processing architecture.

The existing Phase F ingestion pipeline currently operates with repository-global
execution paths and is not bound to a selected Project Workspace, registered
Source or P5 Processing Run.

A navigation link between the Project Dashboard and the existing ingestion UI
would not be sufficient. A real integration must bind the uploaded Source,
Processing Run, Attempts, generated reports and agent outputs to the selected
Project Workspace and make their current state visible through the existing P5
and P7 authorities.

The integration must preserve the following accepted boundaries:

- Project identity is owned by P2.
- Source registration and Source integrity are owned by P3.
- Processing identity, lifecycle, artifact references, retry, supersession and
  recovery are owned by P5.
- Preliminary Coverage and potential model support are owned by P6.
- Project presentation is owned by P7.
- Phase F remains the execution engine for Team Agentic Ingestion.
- CATIA remains the authoritative engineering model.
- A completed ingestion execution does not imply approved engineering knowledge,
  Preliminary Coverage, Approved Generation Readiness or model generation.

## Decision

### 1. Introduce a dedicated project-bound ingestion integration layer

P9 shall introduce a narrow orchestration and publication layer:

```text
modules/project_ingestion/
├── __init__.py
├── errors.py
├── types.py
├── configuration.py
├── publisher.py
└── service.py
```

The central public contract shall be exposed by:

```python
ProjectBoundIngestionService
```

Its primary execution operation shall conceptually provide:

```python
ProjectBoundIngestionService.execute(
    project_id,
    source_path,
    source_role,
    ingestion_configuration,
) -> ProjectBoundIngestionResult
```

The exact Python signature may be refined during implementation, but it shall
preserve the following responsibilities:

1. require one valid selected Project ID;
2. register the uploaded Source through P3;
3. use the registered project-local Source content as execution input;
4. create one P5 Processing Run;
5. create one Processing Attempt for project-bound agentic ingestion;
6. execute the existing Phase F Team Agentic Ingestion pipeline;
7. validate and fingerprint the complete publishable output set;
8. publish artifacts into the P5 run-owned artifact structure;
9. append immutable P5 Processing Events;
10. return a project-bound result containing stable identities and evidence
    references.

The integration layer shall not replace, fork or duplicate the P2, P3, P5, P6 or
P7 authorities.

### 2. Add a common Turing Generator application shell

P9 shall introduce:

```text
app/turing_generator_app.py
```

The application shell shall provide at least:

```text
Project Dashboard
Agentic Ingestion
```

The Project Dashboard remains the P7 read-only inspection interface.

The Agentic Ingestion view is the P9 execution interface.

Navigation shall carry stable application identities and state only:

```text
project_id
return_view
optional selected entity identity
```

Navigation shall not carry unrestricted filesystem paths.

The selected six-digit Project ID is mandatory for P9 execution. The application
shall fail closed when no valid project binding exists. It shall not silently use
a global, unassigned or different project.

### 3. Preserve the existing Phase F pipeline as the execution engine

The existing Team Agentic Ingestion implementation shall remain the execution
engine.

P9 may add a backward-compatible output-root or execution-root option to the
pipeline.

The default behavior shall remain unchanged:

```text
no project-bound execution root supplied
→ existing Phase F global demo behavior
```

Project-bound behavior shall be explicit:

```text
project-bound execution root supplied
→ execution occurs inside the selected P5 Processing Run work directory
```

Existing Phase F tests and the standalone Phase F demo shall remain operational.

The project-bound integration shall invoke the pipeline with the validated
project-local Source content path returned by P3. The original temporary upload
path shall not remain the authoritative execution input after registration.

### 4. Source registration boundary

The Source shall be registered through:

```python
ProjectSourceRegistry.register_source(...)
```

The registration result shall provide the authoritative:

```text
project_id
source_id
source_role
source_sha256
stored filename
registered content path
```

Source registration is durable and precedes Processing Run creation.

If Source registration succeeds but Processing Run creation does not, the Source
remains a valid registered Source and is presented as not started.

Duplicate Source content within the same project shall continue to be rejected by
the P3 duplicate-content contract.

P9 shall not bypass Source integrity, Source role validation or project-local
storage.

### 5. Processing Run contract

P9 shall create Processing Runs through the existing P5 public contracts.

A new run shall bind at least:

```text
project_id
processing_run_id
source_id
source_sha256
source_role_snapshot
workflow_profile
configuration_fingerprint
framework_template_id
framework_template_version
semantic_reference_versions
created_at
optional supersedes_run_id
```

The Processing Run Manifest shall remain immutable.

The configuration fingerprint shall cover all material execution settings that
affect reproducibility, including at least:

```text
recipe ID
provider
model
dry-run mode
team-member limit
runs per member
relevant pipeline configuration version
```

Secrets such as API keys shall never be persisted or included in fingerprints,
reports, events or result objects.

### 6. Add the agentic ingestion Processing Stage

P5 currently models the downstream engineering-processing stages.

P9 shall add:

```text
agentic_ingestion
```

to the canonical Processing Stage vocabulary.

This stage represents the existing Phase F team-based analysis and review-report
generation.

It shall not be mislabeled as:

```text
source_projection
semantic_extraction
semantic_consensus
terminology_mapping
framework_assignment
human_review
publication
```

A successful P9 execution shall initially end with:

```text
run_state:
awaiting_review

processing_stage:
agentic_ingestion
```

### 7. Extend run-owned artifact kinds

P5 currently supports run-owned Agent Outputs and Consensus Reports.

P9 shall extend the canonical run-owned artifact kinds to include:

```text
agent_outputs
consensus_reports
review_reports
run_summaries
```

The canonical structure shall be:

```text
data/projects/<project_id>/runs/<processing_run_id>/
├── run_manifest.json
├── events/
├── artifacts/
│   ├── agent_outputs/
│   │   └── agentic_ingestion/<attempt_id>/
│   ├── consensus_reports/
│   │   └── agentic_ingestion/<attempt_id>/
│   ├── review_reports/
│   │   └── agentic_ingestion/<attempt_id>/
│   └── run_summaries/
│       └── agentic_ingestion/<attempt_id>/
└── work/
```

The work directory is temporary and non-authoritative.

Published artifacts are immutable and shall be referenced by exact P5
`ProcessingArtifactReference` values containing:

```text
artifact_type
artifact_id
content_fingerprint
repository_relative_path
```

The artifact type shall reflect the actual artifact semantics. Review reports and
run summaries shall not be mislabeled as consensus reports or agent outputs.

### 8. Processing Event sequence

A normal first execution shall produce the following conceptual event sequence:

```text
EVT-000001
event_type: run_created
previous_state: null
next_state: created

EVT-000002
event_type: stage_started
previous_state: created
next_state: running
processing_stage: agentic_ingestion
attempt_id: ATT-000001

EVT-000003
event_type: artifact_published
previous_state: running
next_state: running
processing_stage: agentic_ingestion
attempt_id: ATT-000001
artifact_references:
  - Agent Outputs
  - Consensus Reports
  - Review Report
  - Run Summaries

EVT-000004
event_type: review_requested
previous_state: running
next_state: awaiting_review
processing_stage: agentic_ingestion
attempt_id: ATT-000001
```

Event IDs, sequences, fingerprints and previous-event fingerprints remain owned
by P5.

P9 shall not persist mutable current-state files. Current state shall continue to
be derived from the immutable Event History.

### 9. Output validation and publication transaction

Pipeline output generation and authoritative artifact publication are separate
steps.

The Phase F pipeline shall initially write into the run-owned temporary work
directory.

Before publication, P9 shall validate the complete required output set.

Validation shall include at least:

```text
all required files exist
all required files are regular files
no symbolic-link output is accepted
all output paths remain within the run work directory
all output files are readable
all published contents receive SHA-256 fingerprints
artifact identifiers are deterministic and valid
repository-relative target paths are safe
```

No `artifact_published` event shall be appended before the complete required
output set has been validated and copied into its final immutable artifact
directories.

If output generation is incomplete or validation fails:

```text
no generated output is treated as published evidence
```

Temporary work content may remain available for explicit recovery diagnostics,
but it shall not be interpreted by P5, P6 or P7 as active published evidence.

### 10. Failure and recovery behavior

The workflow cannot be one filesystem-atomic transaction because Source
registration, Processing Run persistence, external LLM execution and artifact
publication are separate durable operations.

The following behavior is required.

#### Source registered, Run not created

```text
Source remains registered
Source Processing State remains not_started
No Processing Run is inferred
```

#### Run created, ingestion execution fails

P9 shall append:

```text
event_type: run_failed
next_state: failed
reason_code: team_agentic_ingestion_failed
```

No unvalidated output shall be published.

#### Output validation fails

P9 shall append a failed or blocked transition with a stable reason code.

No `artifact_published` event shall be written.

#### Artifact files published, publication event fails

This is a recovery-requiring partial transaction.

P9 shall raise or persist a recovery-required condition. It shall not report
success.

The next scan shall expose the inconsistency as a blocking issue until recovery is
completed.

#### Publication event succeeds, review-request event fails

The artifacts remain published and traceable.

The run shall not be reported as successfully awaiting review.

A recovery path shall append the missing valid transition or explicitly fail the
run according to the accepted P5 transition rules.

#### Unexpected exception

Internal exception details shall not be exposed as authoritative user-facing
state.

The integration result and UI shall report a safe failure summary while retaining
technical diagnostics in appropriate developer logs or recovery evidence.

### 11. Retry and successor rules

An unchanged material run binding shall use a retry within the same Processing
Run.

A retry shall create a new attempt:

```text
ATT-000002
ATT-000003
...
```

and shall use the existing P5 retry operation.

Material changes require a successor Processing Run.

Material bindings include:

```text
source_id
source_sha256
source_role_snapshot
workflow_profile
configuration_fingerprint
framework_template_id
framework_template_version
semantic_reference_versions
```

Examples:

```text
same Source and same configuration
→ retry in existing Run

changed model, recipe, team scope or runs per member
→ successor Run

changed Source role
→ successor Run

changed Source content
→ newly registered Source and new Run

changed Framework Template or semantic-reference version
→ successor Run
```

P9 shall not create multiple concurrent current Runs for the same Source without
an explicit valid supersession relationship.

### 12. Human Review boundary

A successful P9 execution requests review but does not itself create an approved
engineering decision.

The P9 result remains:

```text
unreviewed
awaiting_review
```

P9 shall not automatically create:

```text
Approved Input
approved Human Review Decisions
approved terminology mappings
approved Framework Assignments
Approved Generation Readiness
model candidates
SysML v2
CATIA changes
```

Existing and later Human Review contracts remain authoritative.

### 13. Coverage boundary

P9 Processing visibility and P6 Preliminary Coverage are separate.

A successful project-bound ingestion run does not create Preliminary Coverage
unless valid P4 artifacts required by P6 actually exist, including the required
Information Units, Framework Assignment Candidates, reference validations and
Human Review Decisions.

The Project Dashboard may therefore legitimately show:

```text
Processing State:
awaiting_review

Preliminary Coverage:
uncovered

Potential Model Support:
not_supported
```

This is not an inconsistency.

### 14. Dashboard return and refresh

After returning from Agentic Ingestion, the application shall discard stale P7
presentation state for the affected project.

The dashboard shall regenerate its view from the P2-P6 authorities.

The P9 result object shall not become presentation truth.

The application may preserve stable navigation state such as:

```text
selected project
return view
selected Source or Processing Run identity
```

It shall clear open Evidence References that belong to a different project.

### 15. Project isolation and path safety

Every P9 operation shall be explicitly project-bound.

The integration shall reject:

```text
invalid Project IDs
unavailable Project Workspaces
cross-project Source references
cross-project Processing Run references
absolute published paths
parent traversal
symbolic-link escapes
repository escapes
artifact paths outside the selected project and run
```

No P9 operation may silently fall back to repository-global Phase F folders when
project-bound execution was requested.

### 16. Result contract

The project-bound result shall be immutable and contain stable identities and
safe evidence references.

It shall include at least:

```text
project_id
source_id
processing_run_id
attempt_id
run_state
processing_stage
dry_run
published artifact references
safe failure or recovery status
```

It shall not contain:

```text
API keys
unrestricted filesystem paths
mutable Streamlit objects
raw exception objects as persisted state
```

### 17. UI behavior

The P9 execution interface shall make the active Project visible.

The intended demonstrator flow is:

```text
Create or select Project
→ open Agentic Ingestion
→ upload legacy Source
→ select Source role
→ configure execution
→ register Source
→ start Processing Run
→ execute Team Agentic Ingestion
→ inspect execution result
→ return to Project Dashboard
→ inspect Source, Run, state and published evidence
```

Real LLM execution shall continue to require explicit human confirmation.

Dry-run mode shall remain available and shall be visibly distinguished from an
engineering assessment.

### 18. No Project Lifecycle Management in P9

P9 shall not add:

```text
Project display-name editing
Project description editing
Project deletion
Project archival
project-wide destructive lifecycle operations
```

These capabilities require a separate Project Lifecycle Management decision and
are not required for the P9 demonstrator.

## Implementation Sequence

P9 shall proceed in six steps:

```text
P9 Step 1 of 6
Integration Architecture and ADR-015

P9 Step 2 of 6
Common Turing Generator Navigation

P9 Step 3 of 6
Project-bound Source Upload and Source Registration

P9 Step 4 of 6
Bridge between Phase F Ingestion and P5 Processing Runs

P9 Step 5 of 6
Project-bound Artifacts, Dashboard Return and Refresh

P9 Step 6 of 6
End-to-End Demonstration, Full Regression and Phase-P Completion Review
```

Implementation shall not proceed by creating a second Project Manifest, Source
Registry, Processing State model or dashboard authority.

## Consequences

### Positive consequences

- The existing Phase F capability becomes demonstrable inside a real Project
  Workspace.
- P2-P7 remain authoritative.
- The integration is narrow and testable.
- Source, Run, Attempt and artifact identities become fully traceable.
- Existing Phase F behavior remains backward compatible.
- Failures remain visible rather than being converted into false success.
- The Project Dashboard can inspect project-bound ingestion evidence without
  becoming writable.
- Processing visibility remains semantically separate from engineering approval
  and model readiness.
- Retry and supersession use the existing P5 lifecycle.
- The implementation can later be extended toward P4 artifact production without
  replacing the P9 project-binding layer.

### Negative consequences

- The integration spans several durable operations and cannot be completely
  filesystem-atomic.
- Explicit recovery handling is required for partial publication failures.
- P5 Processing Stage and artifact-kind vocabularies must be extended.
- The Phase F pipeline requires a backward-compatible execution-root adaptation.
- A common application shell adds navigation and session-state complexity.
- The first P9 demonstrator may show successful ingestion while Preliminary
  Coverage remains uncovered.
- Project metadata editing and deletion remain unavailable.

## Rejected Alternatives

### Add only a link between the two existing Streamlit apps

Rejected because navigation alone would not bind Sources, Processing Runs,
Attempts or generated artifacts to the selected Project Workspace.

### Move all P5 Processing logic into the Streamlit UI

Rejected because UI code must not become the owner of Processing lifecycle,
persistence or recovery rules.

### Write project-bound artifacts directly into global Phase F folders

Rejected because global folders cannot provide project isolation or authoritative
P5 artifact ownership.

### Treat Phase F run directories as P5 Processing Runs

Rejected because Phase F run identity and P5 Processing Run identity have
different contracts and lifecycle semantics.

### Copy outputs directly into final artifact directories during pipeline execution

Rejected because partially generated output could appear as published evidence
before the complete output set has been validated.

### Mark a successful ingestion run as completed

Rejected because the generated reports and agent outputs remain unreviewed. The
correct initial terminal state is `awaiting_review`.

### Treat successful ingestion as Preliminary Coverage

Rejected because P6 requires specific valid P4 evidence and Human Review
contracts.

### Start a new Run for every technical retry

Rejected because unchanged material bindings are handled by P5 Attempts within
the same Run.

### Reuse the same Run after material configuration changes

Rejected because the immutable Run Manifest must preserve reproducibility.
Material changes require a successor Run.

### Store API keys in the Run Manifest or configuration fingerprint

Rejected because secrets must never be persisted as project evidence.

### Implement Project editing and deletion in P9

Rejected because Project Lifecycle Management is separate from project-bound
ingestion integration and destructive deletion requires its own safety,
transaction and audit decision.

## Acceptance Criteria

ADR-015 is satisfied when:

1. a common application shell exposes Project Dashboard and project-bound Agentic
   Ingestion views;
2. every P9 execution requires one valid selected six-digit Project ID;
3. uploaded content is registered through P3 before processing;
4. the registered project-local Source content is the authoritative pipeline
   input;
5. one P5 Processing Run is created with immutable material bindings;
6. `agentic_ingestion` is a canonical Processing Stage;
7. Agent Outputs, Consensus Reports, Review Reports and Run Summaries have
   distinct run-owned artifact kinds;
8. the Phase F pipeline remains backward compatible when no project execution
   root is supplied;
9. project-bound execution uses the selected Run work directory;
10. a complete first execution produces created, running, artifact-published and
    review-requested lifecycle evidence;
11. a successful execution ends in `awaiting_review`;
12. failed execution produces a visible P5 failure state;
13. incomplete or invalid output is never published;
14. every published artifact has an exact fingerprinted
    `ProcessingArtifactReference`;
15. partial publication failures produce explicit recovery behavior rather than
    false success;
16. unchanged material bindings use a retry Attempt in the same Run;
17. material binding changes use a valid successor Run;
18. P9 does not create Approved Input, Approved Generation Readiness, model
    candidates or SysML v2;
19. P9 execution does not imply Preliminary Coverage;
20. returning to the dashboard regenerates P7 views from P2-P6 authorities;
21. cross-project references and unsafe paths are rejected;
22. API keys and unrestricted filesystem paths are not persisted;
23. core integration behavior is testable without Streamlit;
24. the existing Phase F regression remains green;
25. the complete repository regression remains green;
26. an end-to-end dry-run demonstrator can execute the intended project-bound
    flow;
27. Project metadata editing and Project deletion remain outside P9.


\newpage


# ADR-016 — Human Review Workspace and Approved Input Promotion Architecture


Human Review Workspace and Approved Input Promotion Architecture

Status

Accepted

Date

2026-07-31

## Context

Phase P established the project-oriented processing and evidence architecture of
the Turing Generator.

The relevant existing capabilities are:

- project-isolated Source registration;
- immutable Processing Runs and Processing Events;
- immutable run-owned Agent Outputs, Consensus Reports, Review Reports and Run
  Summaries;
- source projections and independently reviewable Information Units;
- terminology and Framework Assignment Candidates;
- immutable Human Review Decisions bound to exact content and validation
  fingerprints;
- project-level Processing and evidence presentation;
- project-bound Agentic Ingestion ending in `awaiting_review`.

A completed Processing Run produces authoritative evidence of what the system
processed and generated. It does not produce approved engineering information.

The authoritative CATIA model assigns the required behavior to:

```text
SF_007 Support Human Review and Approval
```

and to the Logical Component:

```text
LC_05 Candidate and Review Governance
```

The primary applicable System Requirements include:

```text
SYSR_014 Provide Generated Interpretations for Expert Review
SYSR_015 Require Review Before Subsequent Engineering Use
SYSR_016 Support Human Resolution of Conflicting Interpretations
SYSR_017 Record Human Conflict Resolution
SYSR_038 Capture Human Review Decision
SYSR_095 Bind Review Decision to Reviewed Content
SYSR_096 Require New Review after Content Change
SYSR_097 Retain Review Decision Accountability
SYSR_098 Distinguish Engineering Information Authority State
SYSR_099 Promote Approved Engineering Information
SYSR_100 Prevent Unauthorized Engineering Use
SYSR_101 Prevent Automated Approval Substitution
```

The applicable SysML v2 representation requirements include:

```text
SYSR_024 Generate SysML v2 Textual Representation
SYSR_072 Validate SysML v2 Textual Conformance
```

The current Phase F/P review artifacts may contain dozens of detected engineering
items. Different agents may produce equivalent, competing, partially overlapping
or differently classified proposals.

A useful Human Review workflow therefore cannot treat a complete report as one
binary approval target.

The user must be able to:

- review all agent proposals for the same detected subject;
- accept one proposal with one click;
- automatically mark competing variants as not selected;
- edit a proposal inline;
- combine content from multiple proposals;
- reject one proposal or the complete detected subject;
- apply document-level classification decisions where a document is homogeneous;
- override document-level decisions for individual items;
- apply focused decisions to a visible filtered result set;
- review elements and relationships separately;
- resolve open questions without losing overview;
- inspect rejected content;
- compare the original machine-generated report with the human-reviewed version;
- continue editing until the review is explicitly finalized;
- reopen a finalized document only by creating a documented successor version.

The original Source, Agent Outputs, Consensus Reports and Review Reports must
remain immutable.

A human-reviewed document has higher engineering authority than its original
machine-generated report only after explicit finalization and an exact persisted
Human Review Decision.

Phase G must create the authoritative bridge:

```text
Processing Evidence
→ Human Review Workspace
→ Finalized Reviewed Document
→ Approved Input
```

Phase G shall not generate model candidates, an internal engineering model or
SysML v2 architecture artifacts.

## Decision

### 1. Implement Phase G as the concrete realization of SF_007

Phase G shall implement and decompose:

```text
SF_007 Support Human Review and Approval
```

inside the accepted responsibility of:

```text
LC_05 Candidate and Review Governance
```

No new top-level System Function or Logical Component is introduced by this
decision.

The Phase G implementation shall provide:

1. a versioned Human Review Workspace;
2. exact preservation of machine-generated review evidence;
3. document-, filtered-set- and item-level review operations;
4. explicit finalization of one reviewed document version;
5. exact Human Review Decision binding;
6. Approved Input promotion for accepted review items;
7. Approved Input invalidation, revocation and supersession;
8. a stable Approved Input read contract for Phase H.

### 2. Preserve an explicit authority hierarchy

The authority hierarchy for Phase G shall be:

```text
Original Source
    immutable source authority

Agent Outputs and Consensus Reports
    immutable processing evidence

Original Review Report
    immutable machine-generated review presentation

Draft Review Version
    human working state, not approved engineering information

Finalized Reviewed Document Version
    immutable human-reviewed result

Approved Input
    authoritative project-local input for subsequent engineering processing
```

No higher level may overwrite a lower level.

Human review shall create additional versioned artifacts and references. It
shall never modify the original Source, processing artifacts, prior finalized
review versions, prior Human Review Decisions, Approved Input manifests or
Approved Input lifecycle events.

### 3. Introduce Human Review Workspace identities

Phase G shall introduce the following project-local identities:

```text
RVD-000001  Review Document
RVV-000001  Review Document Version
RVR-000001  Review Revision
RIT-000001  Review Item
SRA-000001  Scoped Review Action
AIN-000001  Approved Input
AIE-000001  Approved Input Event
```

Identifiers are project-local, sequential, immutable and never reused.

#### Review Document

A Review Document identifies one review workspace created from one exact eligible
evidence set.

It binds at least:

```text
project_id
review_document_id
source_id
source_sha256
processing_run_id
attempt_id
primary_review_artifact_reference
supporting_artifact_references
framework_template_reference
semantic_reference_versions
created_at
content_fingerprint
```

#### Review Document Version

A Review Document Version is one human-review version of a Review Document.

It binds at least:

```text
review_document_version_id
review_document_id
version_number
predecessor_version_id
reopen_reason
opened_by
opened_at
version_state
head_revision_id
finalized_revision_id
finalized_at
finalization_decision_id
content_fingerprint
```

Allowed version states are:

```text
draft
finalized
```

A finalized version is immutable.

#### Review Revision

A Review Revision is one immutable saved snapshot of a draft Review Document
Version.

The user experience may present one continuously editable working copy. The
persistence model shall remain append-only:

```text
save draft
→ create next immutable Review Revision
→ preserve all previous revisions
```

The current draft state is the latest valid revision derived from repository
history. A mutable authoritative `current.json` file shall not be introduced.

#### Review Item

A Review Item represents one independently reviewable subject.

Allowed Review Item kinds are:

```text
element
relationship
open_question
```

`rejected_content` is a presentation view derived from item decisions. It is not
a separate source item kind.

One Review Item may contain multiple Agent Proposal References.

A Review Item shall preserve at least:

```text
review_item_id
review_item_kind
stable_subject_key
section
original_report_locator
proposal_references
source_evidence_references
consensus_evidence_references
current_human_content
current_classification
current_framework_assignment
current_terminology_assignment
current_source_assignments
current_relationship_representation
effective_review_outcome
item_content_fingerprint
```

The `stable_subject_key` supports continuity across document versions.

The system shall support explicit human correction when automatic grouping is
wrong:

```text
split Review Item
merge selected Review Items
```

Split and merge operations shall preserve all original proposal references and
shall be represented in Review Revision history.

#### Scoped Review Action

A Scoped Review Action represents one draft decision applied to more than one
Review Item or to one review dimension.

It shall bind:

```text
scoped_review_action_id
review_document_version_id
action_scope
decision_dimension
selected_value
filter_definition
materialized_review_item_ids
materialized_item_fingerprints
created_by
created_at
rationale
action_fingerprint
```

Allowed action scopes are:

```text
document_default
filtered_set
explicit_selection
```

A filtered action shall store both the filter definition shown to the user and
the exact Review Item IDs and fingerprints matched at action time.

It shall never remain a dynamic query that silently changes its effect when later
Review Items are added, removed or edited.

### 4. Create Review Documents from exact eligible evidence

Phase G shall support Review Documents derived from eligible P4 and P9 evidence.

#### P4 evidence

Eligible P4 review sources include:

```text
Information Units
Terminology Mapping Candidates
Framework Assignment Candidates
associated reference validations
associated Human Review evidence
```

An Information Unit remains the smallest independently understandable and
reviewable source-derived claim.

Terminology and Framework Assignment Candidates are supporting classifications.
They do not automatically become standalone Approved Inputs.

#### P9 evidence

Eligible P9 review sources include one exact active project-bound evidence set:

```text
primary deterministic Review Report
underlying Agent Outputs
Consensus Reports
Run Summary
Processing Artifact References
Processing Run and Attempt identity
```

The system shall construct Review Items from structured evidence. It shall not
depend on free-form Markdown parsing as the sole source of Review Item identity.

The original Markdown Review Report remains the immutable human-readable source
view.

The Review Workspace shall render structured Review Items inside a report-like
document view so that editing feels local to the relevant report section.

A complete report shall never become one Approved Input.

One accepted Review Item may become one Approved Input.

#### Non-promotable evidence

The following are evidence or review-control information and shall not directly
become Approved Input:

```text
Agent confidence
Consensus level
variance indication
model buildability assessment
general recommendation
gap
risk
ambiguity
unanswered review question
run summary
processing completion
artifact publication
```

An answer to an open question may become promotable only when the reviewer
explicitly converts it into a reviewed engineering statement.

### 5. Present all Agent proposals without losing evidence

For each Review Item, the user interface shall present all associated Agent
Proposal References.

The UI may visually consolidate materially equivalent variants. It shall retain
exact access to every original proposal.

Each proposal view shall expose at least:

```text
agent identity
persona identity
candidate identity
proposed content
proposed classification
proposed Framework Assignment
proposed source assignments
rationale
confidence
generation-readiness statement
supporting and missing evidence
artifact reference
content fingerprint
```

The following quick actions are required for each proposal:

```text
Accept proposal
Edit and accept
Reject proposal
```

The following item-level actions are required:

```text
Combine proposals
Reject all proposals for this Review Item
Defer Review Item
Mark Review Item out of scope
Split Review Item
Merge selected Review Items
```

Selecting one proposal shall mark competing proposals for the same Review Item
as:

```text
not_selected_due_to_human_selection
```

This state is distinct from a finding that a competing proposal is factually
incorrect.

Proposal-selection actions remain draft operations until document finalization.

### 6. Support inline editing through human revisions

The Review Workspace shall allow direct inline editing of the current reviewed
content.

The UI shall make the edited block appear in the position of the corresponding
Review Item in the report-like document view.

Technically, the system shall create human-authored content in a new Review
Revision. It shall not modify the original Agent Proposal.

Editable review dimensions include:

```text
name
engineering statement
description
classification
information type
modality
epistemic status
Framework Assignment
terminology assignment
source assignments
human rationale
human confidence assessment
relationship representation
```

Agent-generated fields remain immutable evidence. Human-edited values are
explicit overrides.

A human revision shall preserve:

```text
derived_from_proposal_ids
derived_from_review_item_ids
original content fingerprints
revised content fingerprint
changed fields
reviewer identity
change time
optional change rationale
```

A rationale is mandatory when:

- a finalized document is reopened;
- a proposal or complete Review Item is rejected;
- a previous Approved Input is revoked;
- a selected SysML v2 relationship construct differs materially from all
  proposed constructs;
- a blocking finding is overridden where an override is permitted.

### 7. Separate review dimensions and define precedence

Review decisions shall not collapse all aspects of one Review Item into one
undifferentiated state.

The system shall support independent review dimensions, including:

```text
content
classification
Framework Assignment
terminology assignment
source assignment
relationship representation
review outcome
```

A document-level decision may set a default for one dimension without approving
all content.

Example:

```text
Document default:
Framework Assignment = System Requirements
```

This does not automatically approve all Requirement statements.

Effective decision precedence is:

```text
item override
> explicit-selection or materialized filtered-set action
> document default
> selected Agent proposal
```

The UI shall show the origin of every effective value.

Recommended compact indicators are:

```text
E  item-level explicit decision
F  materialized filtered-set or explicit-selection decision
D  document default
A  Agent proposal
```

A scoped action shall not overwrite an existing higher-precedence decision
unless the user explicitly requests that overwrite and confirms a preview of the
affected Review Items.

### 8. Use filters as focused review tools

Required filter dimensions include at least:

```text
review status
Review Item kind
proposed classification
effective classification
proposed Framework Assignment
effective Framework Assignment
Agent identity
confidence
Consensus state
Agent disagreement
human modification state
Source identity
evidence sufficiency
relationship validation status
```

Every scoped action shall visibly state whether it applies to:

```text
the complete document
the currently materialized filtered result
an explicit manual selection
one Review Item
```

Before applying a scoped action, the UI shall show:

```text
number of affected Review Items
number with existing item overrides
number excluded because of higher-precedence decisions
number that would be overwritten after explicit confirmation
```

Dangerous bulk rejection shall not be implemented as an unqualified one-click
action.

A rejection affecting more than one Review Item requires a materialized target
list, an impact preview, explicit confirmation and a rationale.

### 9. Use four primary review sections

The Human Review Workspace shall use:

```text
Elements
Relationships
Open Questions
Rejected Content
```

#### Elements

The Elements section presents detected engineering-element proposals.

It shall support grouping and filtering by applicable model area, information
type and classification.

The section shall not imply that Phase G has generated final model elements. It
presents reviewed engineering information and modeling suggestions.

#### Relationships

The Relationships section presents only explicit relationship proposals already
contained in eligible evidence.

Phase G shall not invent missing relationships.

The section may be empty when the source evidence does not contain an explicit
relationship proposal.

Relationship proposals remain separately reviewable from element proposals.

#### Open Questions

The Open Questions section presents:

```text
gaps
ambiguities
risks requiring a decision
missing evidence
review questions
unresolved classifications
unresolved relationship constructs
```

An open question may be answered, closed as not relevant, deferred, marked out
of scope, linked to Review Items or converted into a human-authored engineering
statement.

#### Rejected Content

Rejected Content is an auditable view, not a deletion area.

It shall show the rejected proposal or item, original Agent and artifact
reference, rejection rationale, replacement where applicable, document version,
reviewer and time.

### 10. Require SysML v2 target-notation conformance for relationships

The Relationship review interface shall use only constructs defined by the
applicable versioned SysML v2 target-notation profile of the Turing Generator.

The UI shall not expose generic MBSE relationship labels or SysML v1 relationship
names as authoritative relationship types.

The allowed construct vocabulary shall be loaded from the selected versioned
target-notation profile. It shall not be maintained as an independent hard-coded
UI list.

A relationship proposal shall preserve at least:

```text
source Review Item or referenced element
target Review Item or referenced element
relationship semantic intent
selected SysML v2 construct
construct-specific properties
target-notation profile ID
target-notation profile version
textual-notation preview
supporting evidence
alternative proposed constructs
profile-validation status
profile-validation fingerprint
human review status
```

A textual preview shall use the applicable SysML v2 notation.

Example for an applicable dependency construct:

```sysml
dependency from 'Source Element' to 'Target Element';
```

The exact syntax shall be produced and validated by the applicable target
notation profile, not by free-form UI concatenation.

When no supported construct can be selected, the item state shall be:

```text
unresolved_relationship_candidate
```

An unresolved relationship candidate remains visible, may be edited, deferred,
marked out of scope or rejected, and shall not become Approved Input for
model-generation use.

This decision does not move SysML v2 artifact generation into Phase G.

Phase G reviews an existing relationship proposal and its intended applicable
construct. Phases H to J remain responsible for model candidate creation,
internal model representation and final SysML v2 artifact generation.

### 11. Keep draft actions reversible until explicit finalization

The following actions modify only the current draft version:

```text
accept proposal
edit proposal
combine proposals
reject proposal
reject Review Item
change classification
change Framework Assignment
change terminology assignment
change source assignment
change relationship representation
answer question
defer
mark out of scope
split
merge
apply document default
apply materialized filtered-set action
```

These actions shall be automatically saved through immutable Review Revisions.
They are reversible while the version remains `draft`.

They do not create Approved Input or an authoritative final Human Review
Decision.

### 12. Finalize one exact reviewed document version

The user finalizes a draft through an explicit operation conceptually labeled:

```text
Review abschließen und freigeben
```

Before finalization, the system shall present a summary including at least:

```text
total Review Items
accepted as generated
accepted with human modification
combined
rejected
deferred
out of scope
unresolved
relationship validation results
open questions
changed items compared with predecessor version
```

Finalization shall fail closed when:

- the Project, Source, Run or Artifact binding is invalid;
- an authoritative evidence fingerprint has changed;
- the selected Review Revision is not the current head revision;
- a Review Item has no explicit or effective review outcome;
- an unresolved item is not explicitly `deferred` or `out_of_scope`;
- a relationship selected for approval is not valid under the applicable SysML
  v2 target-notation profile;
- a blocking evidence or integrity issue exists;
- a cross-project reference exists;
- a required supporting artifact is unavailable, superseded or invalidated;
- a predecessor-version relation is inconsistent.

Finalization shall create:

```text
immutable Finalized Reviewed Document manifest
immutable rendered Reviewed Report
immutable effective decision set
exact content fingerprint
exact validation fingerprint
```

The rendered Reviewed Report is the primary human-readable reviewed artifact.
The effective decision set is the primary machine-readable record.

The finalization target shall be added to the Human Review target vocabulary:

```text
target_type: review_document_finalization
target_id: RVV-000001
```

Finalization requires an exact persisted decision:

```text
decision: confirm
review_mode: detailed_review
```

The Human Review Decision shall bind the exact Review Document Version content
fingerprint, validation fingerprint, reviewer identity, decision time and
outcome.

Consensus, confidence, processing completion and artifact publication cannot
substitute this decision.

### 13. Reopen finalized documents only through a successor version

A finalized Review Document Version shall never become editable again.

A user may reopen the Review Document only by creating a successor Review
Document Version.

Reopening requires a rationale and records:

```text
predecessor version
reopen reason
opened by
opened at
baseline item fingerprints
baseline effective decisions
```

The successor draft is initialized from the finalized predecessor.

The UI shall provide:

```text
Review Copy
Original Report
Changes to Original
Changes to Predecessor Version
Version History
```

A later finalized version shall not rewrite or delete its predecessor.

### 14. Define Approved Input as one approved Review Item

One Approved Input represents one independently reviewable and approved
engineering-information item.

Allowed initial Approved Input kinds are:

```text
element_statement
relationship_statement
human_clarification
```

An Approved Input Manifest shall bind at least:

```text
schema_version
project_id
approved_input_id
approved_input_kind
authority_state
canonical_content
selected_classification
selected_framework_assignment
selected_terminology_assignment
selected_source_assignments
selected_relationship_representation
review_document_id
review_document_version_id
review_revision_id
review_item_id
review_item_fingerprint
finalization_decision_id
finalization_decision_fingerprint
finalization_validation_fingerprint
source_id
source_sha256
processing_run_id
attempt_id
primary_artifact_reference
supporting_artifact_references
proposal_references
created_at
content_fingerprint
```

One finalized Reviewed Document Version may create zero, one or many Approved
Inputs.

No Approved Input is created for an item whose effective outcome is:

```text
rejected
deferred
out_of_scope
unresolved
```

An open question creates no Approved Input unless the reviewer explicitly
converts its answer into an approved clarification or engineering statement.

### 15. Define Approved Input authority and lifecycle

Approved Input manifests are immutable. Lifecycle changes are represented by
immutable Approved Input Events.

Allowed derived Authority States are:

```text
active
invalidated
revoked
superseded
```

#### Active

The Approved Input is valid for subsequent engineering use.

#### Invalidated

The Approved Input has lost technical or upstream integrity.

Examples include Source or artifact fingerprint mismatch, invalid Run binding,
invalid finalization decision binding, relationship-profile validation failure
or project-isolation failure.

Invalidation is system- or integrity-driven.

#### Revoked

A human-reviewed successor version explicitly withdraws the authority of a
previous Approved Input without approving a replacement.

Examples include a previously accepted item that is now rejected, out of scope
or explicitly withdrawn.

Revocation requires a rationale and reviewer accountability.

#### Superseded

A newer active Approved Input explicitly replaces an older Approved Input for the
same stable review subject.

The old Approved Input remains immutable and traceable.

`rejected` is not an Approved Input lifecycle state. Rejected Review Items do not
create Approved Inputs.

### 16. Reconcile successor versions item by item

When a successor Review Document Version is finalized, the system shall compare
stable Review Item identities and fingerprints with the predecessor version.

Required behavior:

```text
unchanged accepted item
→ retain existing active Approved Input

changed item accepted again
→ create new Approved Input
→ mark previous Approved Input superseded

previously accepted item now rejected
→ revoke previous Approved Input

previously accepted item now out of scope
→ revoke previous Approved Input

new accepted item
→ create new Approved Input

upstream integrity failure
→ invalidate affected Approved Input
```

Unchanged items shall not receive new Approved Input IDs merely because the
containing document received a new version.

### 17. Extend the existing Human Review repository

The existing Human Review Decision repository remains authoritative for final
Human Review Decisions.

Phase G shall extend it with:

```text
review_document_finalization
```

It shall not introduce a parallel incompatible decision repository.

Existing target types remain valid:

```text
information_unit_publication
terminology_mapping_candidate
framework_assignment_candidate
```

The existing exact-confirmation principle remains:

```text
target ID matches
target content fingerprint matches
validation fingerprint matches
latest exact decision is confirm
target validation is not invalid
```

A stale Human Review Decision cannot authorize finalization or promotion of
changed content.

### 18. Define promotion eligibility

A finalized Review Item is eligible for Approved Input promotion only when all of
the following are true:

```text
Project exists and matches all references
Source exists and exact SHA-256 matches
Processing Run exists and matches Source and Project
Processing Run is valid for promotion
primary artifact reference is active
supporting artifact references are valid
Review Document Version is finalized
exact finalization Human Review Decision exists
exact finalization fingerprints match
effective item outcome is accepted
Review Item fingerprint is included in finalized version
no blocking evidence issue applies
required Framework and terminology references are valid
relationship representation is profile-valid where applicable
no cross-project reference exists
no newer lifecycle event blocks the item
```

Eligibility shall be recalculated immediately before promotion.

A previously calculated eligibility result is advisory unless it is bound into
the finalization validation fingerprint and revalidated at promotion time.

### 19. Define project-local persistence

The conceptual project-local structure shall be:

```text
data/projects/<project_id>/
├── reviews/
│   └── RVD-000001/
│       ├── review_document_manifest.json
│       └── versions/
│           ├── RVV-000001/
│           │   ├── review_version_manifest.json
│           │   ├── revisions/
│           │   │   ├── RVR-000001.json
│           │   │   └── RVR-000002.json
│           │   ├── scoped_actions/
│           │   │   └── SRA-000001.json
│           │   └── finalized/
│           │       ├── reviewed_document.json
│           │       ├── effective_decisions.json
│           │       └── reviewed_report.md
│           └── RVV-000002/
│               └── ...
├── semantics/
│   └── human_reviews/
│       └── HRD-000001.json
└── approved_inputs/
    ├── manifests/
    │   └── AIN-000001.json
    └── events/
        └── AIN-000001/
            ├── AIE-000001.json
            └── AIE-000002.json
```

The exact internal file split may be refined during implementation.

Mandatory boundaries are:

- original run-owned artifacts stay under the Processing Run;
- review artifacts are project-local;
- Human Review Decisions stay in the existing repository;
- Approved Inputs have one dedicated project-local repository;
- persisted paths are repository-relative;
- symbolic links and path traversal are rejected;
- no operation may silently cross a Project boundary.

### 20. Define public application contracts

The Human Review Workspace shall expose conceptual public operations equivalent
to:

```python
ReviewWorkspaceService.open_review_document(...)
ReviewWorkspaceService.create_review_document(...)
ReviewWorkspaceService.save_revision(...)
ReviewWorkspaceService.apply_scoped_action(...)
ReviewWorkspaceService.split_review_item(...)
ReviewWorkspaceService.merge_review_items(...)
ReviewWorkspaceService.finalize_version(...)
ReviewWorkspaceService.reopen_finalized_version(...)
```

Repository operations shall conceptually include:

```python
ReviewWorkspaceRepository.load_document(...)
ReviewWorkspaceRepository.load_version(...)
ReviewWorkspaceRepository.load_revision(...)
ReviewWorkspaceRepository.list_versions(...)
ReviewWorkspaceRepository.scan(...)
```

Approved Input operations shall conceptually include:

```python
ApprovedInputPromotionService.assess_eligibility(...)
ApprovedInputPromotionService.promote_finalized_version(...)
ApprovedInputLifecycleService.invalidate(...)
ApprovedInputLifecycleService.revoke(...)
ApprovedInputLifecycleService.supersede(...)
```

The stable Phase H read contract shall be:

```python
ApprovedInputRepository.list_active_approved_inputs(
    project_id,
) -> tuple[ApprovedInputManifest, ...]
```

Phase H shall not read mutable draft Review Revisions, original Review Reports as
model-generation authority, Agent confidence or Consensus as approval authority,
Human Review UI state, or inactive Approved Inputs.

### 21. Define finalization and promotion failure behavior

Finalization and promotion cannot be one filesystem-atomic transaction across all
repositories.

The required sequence is:

```text
1. validate current Review Revision
2. persist immutable finalized Review Document artifacts
3. record exact Human Review Decision
4. revalidate promotion eligibility
5. create Approved Input manifests
6. append required lifecycle events
7. report complete or recovery-required status
```

No Approved Input may be created before the exact finalization Human Review
Decision exists.

If finalization artifacts exist but the Human Review Decision cannot be recorded,
the document is not treated as approved, no Approved Input is created and
recovery is required.

If the Human Review Decision exists but promotion is only partially completed,
existing promoted items remain traceable, the operation is not reported as fully
successful and deterministic recovery resumes idempotently.

Promotion idempotence shall be based on at least:

```text
project_id
review_document_version_id
review_item_id
review_item_fingerprint
finalization_decision_fingerprint
```

Equivalent duplicate promotion shall not create a second active Approved Input.

### 22. Define the required Human Review user experience

The Review Workspace is a critical engineering interface.

Its default layout shall preserve overview through:

```text
left:
document outline, item list, filters and status counts

center:
report-like reviewed document with inline item editing

right:
all Agent proposals, comparison, evidence and original content
```

The interface shall provide:

```text
Elements
Relationships
Open Questions
Rejected Content
```

The active Project, Source, Processing Run, Review Document and version shall
remain visible.

The UI shall provide:

```text
Review Copy
Original Report
Diff to Original
Diff to Predecessor
Version History
```

Required status counts include:

```text
open
accepted as generated
accepted with modification
combined
rejected
deferred
out of scope
unresolved
```

The UI shall offer one-click item operations where safe.

One-click draft actions shall not be confused with final document approval. The
final approval operation shall be visually distinct and shall always show a
complete impact summary.

### 23. Preserve review and rework loops

Human Review may determine that information must return to an earlier processing
capability.

The workspace shall support explicit outcomes conceptually equivalent to:

```text
request source-information rework
request semantic-governance rework
request candidate-regeneration
```

These outcomes remain traceable to the affected Review Items and do not create
Approved Input.

The exact Processing Run successor or retry behavior remains governed by the
accepted Processing architecture.

### 24. Define implementation sequence

Phase G shall proceed in the following steps:

```text
G1
ADR-016 and Phase G contract acceptance

G2
Human Review Workspace identifiers, immutable types, manifests and repositories

G3
P4/P9 evidence adapters and deterministic Review Item construction

G4
Draft revisions, scoped actions, version finalization and reopening

G5
Approved Input manifests, repository, eligibility, promotion and lifecycle

G6
Human Review Workspace UI with Elements, Relationships, Open Questions and
Rejected Content

G7
Project-bound integration, recovery behavior, manual acceptance audit and full
regression
```

The first executable implementation step after ADR-016 shall define contracts and
tests before UI implementation.

## Consequences

### Positive consequences

- The original machine-generated evidence remains permanently available.
- The user can edit a report naturally without weakening traceability.
- One large report can produce many independently reviewed Approved Inputs.
- All Agent proposals remain visible and attributable.
- One-click selection remains possible without making draft clicks immediately
  authoritative.
- Document-level decisions reduce repetitive review effort.
- Item-level overrides support mixed documents.
- Materialized filter actions provide focused review without dynamic-scope risk.
- Elements and relationships remain visually separated.
- Open questions remain visible instead of disappearing into report prose.
- Rejected content remains auditable.
- Finalized documents are immutable.
- Reopening creates an explicit successor version.
- Unchanged Approved Inputs remain stable across document versions.
- Changed Approved Inputs are superseded rather than overwritten.
- Human withdrawal is distinguishable from technical invalidation.
- Relationship review uses the applicable SysML v2 target notation instead of
  generic or obsolete relationship labels.
- Phase H receives one stable, project-local Approved Input contract.

### Negative consequences

- The Phase G persistence model is more complex than a mutable edited Markdown
  file.
- The UI requires structured Review Items and cannot rely only on Markdown.
- Proposal grouping, split and merge behavior require deterministic identities
  and careful testing.
- Document defaults and item overrides require transparent precedence rules.
- Version finalization and Approved Input promotion require recovery handling.
- SysML v2 relationship review depends on an available versioned target-notation
  profile.
- A complete review can produce many immutable manifests and events.

### Accepted complexity

The additional complexity is accepted because the Human Review Workspace is an
engineering authority boundary.

A simpler mutable-report design would not provide sufficient decision binding,
version accountability, item-level approval, Agent-evidence preservation,
project isolation, stale-decision prevention, relationship-notation conformance,
Approved Input lifecycle or downstream traceability.

## Rejected alternatives

### Edit the original Review Report in place

Rejected because it would destroy the immutable record of machine-generated
evidence and make exact decision reconstruction unreliable.

### Copy and edit one unstructured Markdown document only

Rejected because item identities, proposal variants, scoped decisions,
fingerprints and downstream Approved Input promotion would be unreliable.

### Approve or reject the complete report as one target

Rejected because reports may contain dozens of independently correct, incorrect
or incomplete items.

### Require one manual click for every classification in every document

Rejected because homogeneous documents require efficient document-level
decisions.

### Apply document-level decisions without item overrides

Rejected because mixed documents require explicit exceptions.

### Keep filter actions as dynamic saved queries

Rejected because later content changes could silently change the set affected by
an earlier decision.

### Treat unselected Agent proposals as factually rejected

Rejected because choosing one variant does not necessarily prove all alternatives
incorrect.

### Let Agent confidence or Consensus authorize approval

Rejected because automated evidence cannot replace explicit human approval.

### Store one mutable current review state

Rejected because it would weaken history reconstruction and recovery.

### Reopen and modify a finalized version directly

Rejected because it would invalidate the meaning of the original finalization
decision.

### Use generic relationship types in the UI

Rejected because relationship review must use the applicable SysML v2
target-notation profile and remain compatible with later validated textual
generation.

### Generate model candidates in Phase G

Rejected because model candidate generation belongs to Phase H and must consume
Approved Input through the stable read contract.

## Acceptance

The accepted decision comprises:

- immutable original reports;
- editable draft Review Copies;
- immutable finalized reviewed versions;
- documented reopening through successor versions;
- inline element editing;
- all Agent proposals in one item-centered review view;
- one-click proposal selection;
- automatic non-selection of competing variants;
- document-level and item-level decisions;
- focused materialized filter actions;
- separate Elements, Relationships, Open Questions and Rejected Content sections;
- dangerous bulk rejection only with preview, confirmation and rationale;
- versioned Approved Input promotion;
- invalidation, revocation and supersession behavior;
- mandatory use of applicable SysML v2 target notation for relationship review;
- the complete Phase G architecture documented in this ADR.


\newpage


# ADR-017 — Simple-by-Default Interaction and Progressive Disclosure


**Status:** Accepted
**Date:** 2026-08-12
**Decision scope:** System-wide user interaction architecture for the Turing Generator prototype

## Context

The Turing Generator intentionally preserves a rich internal architecture for
authority, evidence, traceability, immutable revision history, validation and
recovery.

Phase-G manual acceptance demonstrated that exposing this complete internal
detail simultaneously in the primary Streamlit workflow creates unnecessary
interaction cost.

The direct-prompt benchmark is relevant: a user can upload legacy engineering
content to a general LLM and quickly receive plausible SysML-like output.

The Turing Generator therefore creates value only when its additional
governance, reproducibility, traceability, Human Review authority and validation
are preserved **without requiring the user to operate the audit architecture as
the default interaction model**.

This decision does not reduce backend evidence, validation or authority.

## Decision

The system shall apply the following interaction principle:

```text
Simple by default.
Explainable on demand.
Fully traceable underneath.
```

The interaction architecture separates three presentation levels.

### Level 1 — Primary Task-oriented Workflow

Default views shall emphasize:

- engineering result
- material uncertainty or unresolved issue
- required human decision
- next action

The primary workflow shall not require the user to interpret internal IDs,
fingerprints, persistence paths or complete provenance graphs unless they are
material to the immediate task.

### Level 2 — Explanation on Demand

The user shall be able to inspect relevant explanation including:

- rationale
- alternative proposals
- relevant source evidence
- disagreement / consensus where useful
- confidence where useful
- validation reason
- material impact of a decision

Explanation is not authority by itself.

### Level 3 — Audit and Traceability

Complete technical evidence remains inspectable, including:

- Project / Source identity
- Processing Run / Attempt identity
- artifact references
- immutable revision lineage
- Human Review Decision binding
- fingerprints
- validation evidence
- Approved Input authority state
- provenance and lifecycle events

This information remains persisted according to the owning contracts even when
it is not shown by default.

## System-wide Scope

The principle applies to:

- Source registration and Agentic Ingestion
- semantic processing
- Human Review
- Approved Input promotion
- Architecture / Model Candidate interaction
- Internal Engineering Model generation
- validation
- SysML v2 generation and result presentation

## Interaction Boundary

Task-oriented interaction and audit-oriented inspection are separate concerns.

A user shall not need to navigate audit-oriented information merely to continue
a normal engineering workflow.

Human interaction should be exception-driven where possible.

Examples:

```text
normal:
result + next action

uncertain:
result + material uncertainty + required decision

audit:
explicitly opened evidence / provenance / fingerprint detail
```

## Human Review UX Consequences

The current Human Review implementation remains valid as a Phase-G authority
implementation.

A later guided-workflow pass should:

- place competing proposals side by side where useful
- make the selected engineering statement and required decision primary
- collapse detailed evidence by default
- expose actionable relationship-validation reasons
- provide clear write-action lifecycle feedback
- keep complete traceability available on demand

No Phase-G authority contract is weakened to achieve this.

## Agentic Ingestion UX Consequences

The default ingestion result should prefer:

- what was processed
- whether the run succeeded
- material findings / uncertainty
- whether Human Review is required
- the next action

Technical Run / Attempt / artifact detail remains available but need not dominate
the default view.

## Architecture / Model Proposal UX Consequences

Phase H and later proposal workflows should default to:

- proposed engineering content
- relationships and structural implications
- material alternatives
- why the proposal matters
- required human decision

Complete provenance remains expandable.

## SysML v2 Result UX Consequences

The final workflow should present:

- model result
- validation status
- publication status
- concise generation summary

Textual notation, full validation detail, fingerprints and traceability remain
available on demand.

## Technology Decision for the Demo

Streamlit remains the prototype UI technology through the 2026-08-18 product
demo.

The following are explicitly not part of the demo critical path:

- React rewrite
- Vue rewrite
- separate FastAPI backend migration solely for UI restructuring

The current application may continue to call Python application services
directly.

A future frontend/backend separation remains possible without changing the
domain/service architecture.

## Consequences

Positive:

- lower interaction burden
- clearer engineering decisions
- stronger contrast to direct ungoverned prompt-based generation
- auditability remains intact
- backend architecture remains reusable
- UX can evolve independently from authority contracts

Trade-offs:

- the prototype must maintain both concise default views and deeper inspection
- progressive disclosure requires deliberate information hierarchy
- some existing Streamlit pages remain information-dense until the later UX pass

## Non-goals

This ADR does not:

- remove persisted evidence
- remove fingerprints
- remove immutable history
- weaken Human Review
- make confidence or consensus authoritative
- replace CATIA authority
- redefine Phase-H Model Candidate architecture
- mandate a production frontend framework

## Presentation Implication

The architecture should be communicated as:

```text
simple interaction surface
over
explicit governed engineering services
over
complete persisted evidence and traceability
```

This supports the literature-derived Data / Process / Knowledge framing while
keeping Knowledge and governance cross-cutting rather than forcing them into a
single UI or deployment component.


\newpage


# ADR-018 — Model Candidate Layer and Structural Comparability


## Status

Accepted

## Date

2026-08-12

## Context

Phase G establishes the authoritative promotion boundary from reviewed project
information to immutable Approved Inputs.

Phase H consumes those Approved Inputs and creates a coherent proposal for the
target engineering model.

The Phase-H layer must support:

- joint interpretation of all active Approved Inputs of a project,
- candidate model-element derivation,
- candidate relationship derivation,
- explicit relationship semantics and alternatives,
- advisory relationship prioritization,
- structural comparability across related models,
- versioned structural modeling guidance,
- Human Review,
- complete provenance,
- deterministic downstream consumption by Phase I,
- and a comprehensible user-facing model proposal.

Phase H shall not generate SysML v2 textual notation and shall not create a new
engineering authority source.

The system-wide interaction principle defined by ADR-017 applies:

> Simple by default. Explainable on demand. Fully traceable underneath.

A user shall therefore primarily see a coherent model proposition rather than
individual technical candidate artifacts.

---

## Decision

### 1. Phase-H authority boundary

Phase H consumes engineering authority exclusively through:

`ApprovedInputRepository.list_active_approved_inputs(project_id)`

Draft Review State, Agent Confidence, Consensus data, inactive Approved Inputs,
UI state, and original Review Reports shall not act as direct Phase-H authority
sources.

Approved Inputs remain unchanged.

Phase H creates derived Model Candidates based on the exact active Approved-Input
snapshot.

Model Candidates are not equivalent to Approved Inputs and are not equivalent
to the authoritative CATIA engineering model.

Human acceptance of a Model Candidate authorizes its use for Phase-I model
assembly only.

---

### 2. Model Candidate Set

Each Phase-H execution creates an immutable Model Candidate Set.

Proposed identifier form:

`MCS-000001`

A Candidate Set represents a reproducible snapshot of the complete Phase-H model
proposal for one project.

It shall bind at least:

- project identity,
- Candidate Set identity,
- predecessor Candidate Set where applicable,
- regeneration reason where applicable,
- exact Approved-Input references and fingerprints,
- Approved-Input snapshot fingerprint,
- framework-template reference,
- Model Structure and Comparability Profile reference,
- derivation-rules reference,
- generation provenance,
- Element Candidate references,
- Relationship Candidate references,
- creation timestamp,
- content fingerprint.

There shall be no implicit "latest Candidate Set wins" behavior.

Downstream consumers must explicitly select a Candidate Set.

---

### 3. Element Candidates

Candidate model elements are represented as immutable Element Candidates.

Proposed identifier form:

`MCE-000001`

Element Candidate instance identity is distinct from semantic continuity.

Each Element Candidate shall support at least:

- immutable candidate identity,
- candidate subject key,
- comparison anchor where applicable,
- proposed name and description,
- model area,
- profile-defined element type,
- framework assignment,
- terminology assignment,
- attributes,
- exact Approved-Input provenance,
- derivation rationale,
- support level,
- assumptions,
- missing information,
- structural-profile conformance,
- predecessor candidate references,
- immutable content fingerprint.

Where possible, a one-to-one derivation should retain continuity with the
Approved Input stable subject key.

Aggregation or decomposition may create new candidate subject identities while
preserving all contributing provenance.

---

### 4. Relationship Candidates

Relationships are independent immutable Candidate artifacts.

Proposed identifier form:

`MCR-000001`

Relationships shall not be embedded implicitly in Element Candidates.

Every Relationship Candidate shall support at least:

- immutable relationship candidate identity,
- Candidate Set identity,
- relationship choice key where alternatives belong to the same decision,
- exact source Element Candidate reference,
- exact target Element Candidate reference,
- source and target semantic subject keys,
- relationship family,
- semantic intent,
- directionality,
- exact Approved-Input provenance,
- derivation rationale,
- supporting evidence,
- assumptions,
- missing information,
- priority assessment,
- structural-comparability assessment,
- structural-profile conformance,
- upstream Approved-Input relationship representation where applicable,
- predecessor candidate references,
- immutable content fingerprint.

Every relationship eligible for Phase I must resolve its source and target to
exact Element Candidates within the same Candidate Set.

Zero matching endpoints are unresolved.

More than one matching endpoint is ambiguous.

Exactly one matching endpoint is resolved.

---

### 5. Relationship semantics

Relationship family, engineering semantic intent, and eventual SysML v2
serialization are separate concepts.

Relationship families may include, among others:

- dependency,
- allocation,
- flow,
- refinement-related,
- derivation-related,
- framework-specific relationships.

The actual controlled vocabulary shall be defined by versioned configuration or
profile data and shall not be silently hardcoded into processing logic.

Distinct semantic meanings shall never be silently collapsed into a generic
relationship.

Where multiple materially different interpretations remain plausible, separate
Relationship Candidates shall be created and grouped through one relationship
choice key.

Existing Approved-Input relationship representations may be carried forward as
evidence.

Phase H shall not silently override an explicit Approved-Input relationship
interpretation.

Any changed interpretation requires explicit rationale and traceability.

Final semantic-to-SysML-v2 serialization belongs to Phase J.

Formal SysML-v2 validation belongs to Phase K.

---

### 6. Relationship prioritization

Automated relationship prioritization is advisory only.

No automated priority shall constitute engineering approval.

The default priority classes are:

- preferred,
- supported_alternative,
- exception_candidate.

Prioritization shall record machine-readable criterion results and a
human-readable rationale.

The prioritization criteria are evaluated in the following conceptual order:

1. evidence directness,
2. semantic fit,
3. endpoint certainty,
4. structural-profile preference,
5. structural-comparability impact,
6. assumption burden,
7. conformance.

An explicit Approved-Input relationship normally has stronger evidence
directness than a relationship inferred solely from separate Approved Inputs.

Human Review remains the authorization boundary.

---

### 7. Model Structure and Comparability Profile

Phase H introduces a versioned Model Structure and Comparability Profile.

This profile is separate from the SysML v2 Target Notation Profile.

The Model Structure and Comparability Profile defines, at minimum:

- preferred model areas,
- permitted element types,
- comparison anchors,
- canonical structural patterns,
- canonical relationship semantics,
- relationship preferences,
- permitted structural variation,
- prioritization criteria,
- exception rules,
- review requirements.

The purpose of the profile is to improve structural comparability between:

- related products,
- product variants,
- independently generated models,
- repeated model-generation executions.

The guiding principle is:

Where engineering meaning and modeling context are equivalent, the same
structural representation should be preferred.

Different engineering meaning may justify different structures.

Intentional deviations remain possible but require explicit rationale and Human
Review.

---

### 8. Structural-comparability assessment

Candidates may record structural-comparability effects including:

- improves,
- neutral,
- reduces,
- unknown.

The assessment may include:

- affected comparison anchors,
- canonical-pattern match,
- structural deviations,
- rationale.

Comparability findings are advisory evidence for Human Review and shall not
replace engineering judgment.

---

### 9. Human Review

Model Candidate content is immutable.

Human Review decisions are persisted separately and are bound to the exact
Candidate content fingerprint.

Supported decision concepts include:

- accepted,
- rejected,
- deferred,
- accepted_exception.

An accepted exception requires explicit rationale.

Human Review shall bind at least:

- project identity,
- Candidate Set identity,
- Candidate identity or explicitly defined Candidate Set selection scope,
- exact content fingerprint,
- reviewer identity,
- review timestamp,
- decision,
- rationale where required,
- structural-profile reference,
- relevant conformance fingerprint,
- Approved-Input snapshot fingerprint,
- immutable decision fingerprint.

Existing generic Human Review infrastructure shall be reused where compatible
rather than duplicated.

The exact review granularity may support review-by-exception, provided that every
authoritative Phase-I selection remains explicitly persisted and bound to exact
candidate content.

---

### 10. Lifecycle and regeneration

Candidate artifacts and review decisions remain immutable.

If any material Approved Input referenced by a Candidate Set later becomes
inactive, the historical Candidate Set remains preserved but becomes ineligible
for new Phase-I assembly.

A new analysis creates a new Candidate Set.

Regeneration shall:

- create a new Candidate Set identity,
- create new Candidate instance identities,
- preserve predecessor references,
- preserve semantic subject continuity where applicable,
- preserve complete provenance.

Historical Candidate Sets are never silently replaced.

---

### 11. Phase-I read contract

Phase I shall consume Phase-H output only through an explicit validated read
contract.

Conceptually:

`ModelCandidateReadService.load_phase_i_input(project_id, candidate_set_id)`

The read boundary shall verify at least:

- Candidate Set integrity,
- project isolation,
- immutable fingerprints,
- current activity of material Approved Inputs,
- exact Human Review decisions,
- absence of stale decisions,
- endpoint resolution,
- acceptance of required Element Candidates,
- acceptance of required Relationship Candidates,
- absence of blocking unresolved relationships,
- structural-profile conformance or an explicitly accepted exception.

The resulting Phase-I input shall contain only explicitly authorized Candidate
content and relevant provenance.

Phase I may assemble accepted Candidates into the Internal Engineering Model.

Phase I shall not invent new engineering semantics from unaccepted Phase-H
Candidates.

---

### 12. User-facing Model Proposal

The primary user-facing Phase-H result is a coherent Model Proposal rather than
a collection of technical manifests.

The Model Proposal is a deterministic projection of one explicit Candidate Set.

It is not an independent authority source.

Conceptually, the presentation contract is:

`ModelProposalView`

It shall support at least:

- Candidate Set identity,
- proposal summary,
- proposed model elements,
- proposed relationships,
- structural overview,
- relationship choice groups,
- structural-comparability summary,
- profile deviations,
- required Human Review decisions,
- summarized rationale,
- next action.

All displayed information must resolve back to immutable Candidate artifacts and
review evidence.

---

### 13. Visual model projections

The Model Proposal may and should use visual model projections where they
improve comprehension.

Examples include:

- structural overview diagrams,
- UML-like component views,
- SysML-like block views,
- requirement-to-function views,
- function-to-logical-component allocation views,
- relationship networks,
- alternative relationship visualizations.

These visualizations are explanatory projections of Candidate data.

They are not themselves the machine-readable model authority.

A visual relationship may therefore replace verbose technical relationship
descriptions in the primary UI while retaining the complete technical
relationship representation underneath.

The default user interaction shall prefer:

1. understandable graphical/model result,
2. material uncertainty,
3. required human decision,
4. next action.

Detailed rationale and alternatives shall be available on demand.

Complete provenance and audit information shall remain accessible underneath.

The Phase-H visualization does not have to constitute formally valid SysML v2
notation.

Formal model assembly belongs to Phase I.

Formal SysML v2 textual generation belongs to Phase J.

---

### 14. Optional Model Proposal report

A human-readable or exportable Model Proposal Summary may be generated from an
explicit Candidate Set.

Such a report may be retained as presentation, thesis, or audit evidence.

It shall remain a reproducible projection and shall never replace the Candidate
Set as the machine-readable source of truth.

---

### 15. Phase boundaries

Phase H:

- jointly interprets active Approved Inputs,
- proposes model elements,
- proposes relationships,
- represents alternatives,
- prioritizes relationships advisorially,
- assesses structural comparability,
- performs Candidate Human Review,
- exposes the Model Proposal View.

Phase I:

- assembles explicitly accepted Candidates into the Internal Engineering Model.

Phase J:

- generates SysML v2 textual notation from the Internal Engineering Model.

Phase K:

- validates syntax, semantics, structure, target-profile conformance, and
  applicable comparability constraints.

---

## Consequences

### Positive consequences

- Project information is synthesized into one coherent model proposal.
- Approved Inputs remain immutable and authoritative within their existing scope.
- Model derivation remains completely traceable.
- Alternative modeling interpretations remain explicit.
- Relationship semantics are not silently flattened.
- Structural comparability becomes a first-class modeling concern.
- Human Review remains the engineering authorization boundary.
- The default UX can remain simple despite detailed underlying traceability.
- Visual diagrams can communicate architecture considerably more efficiently
  than raw relationship manifests.
- Phase H remains independent from final SysML v2 textual serialization.
- Phase I receives a deterministic and validated input contract.

### Trade-offs

- Candidate Set lifecycle introduces additional persistent identities.
- Candidate review and regeneration require explicit lifecycle handling.
- Structural comparability requires a maintained versioned profile.
- Visual proposal rendering introduces a presentation layer that must remain
  strictly derived from machine-readable Candidate data.
- Relationship alternatives increase artifact count but prevent semantic
  information loss.

---

## Rejected alternatives

### Generate SysML v2 directly from Approved Inputs in Phase H

Rejected because this would collapse modeling synthesis, engineering-model
assembly, serialization, and validation into one stage and would bypass the
planned Phase-I architecture boundary.

### Treat the visual diagram as the authoritative model

Rejected because graphical layout is a presentation concern and must remain
reproducible from structured Candidate data.

### Require users to review every technical Candidate manifest directly

Rejected because this conflicts with ADR-017 and would create unnecessary UX
complexity.

### Collapse all relationship semantics into generic links

Rejected because semantic distinctions are relevant for engineering correctness,
structural comparability, and later SysML v2 generation.

### Automatically accept the highest-ranked relationship

Rejected because prioritization is advisory and may not substitute explicit
Human Review.

---

## Related decisions

- ADR-016 — Human Review Workspace and Approved Input Promotion Architecture
- ADR-017 — Simple-by-Default Interaction and Progressive Disclosure

---

## Implementation note

No Phase-H production implementation shall precede acceptance and persistence of
this ADR.

Implementation shall reuse existing repository, Human Review, validation,
identifier, fingerprint, and project-isolation contracts where applicable rather
than duplicating upstream logic.


\newpage


# ADR-019 — Internal Engineering Model Assembly Architecture


## Status

Accepted

## Date

2026-08-13

## Context

Phase H establishes the reviewed Model Candidate Layer and exposes the sole
validated Phase-H → Phase-I authority transfer through:

`ModelCandidateReadService.load_phase_i_input(project_id, candidate_set_id)`

The resulting `ModelCandidateAssemblyInput` contains only explicitly authorized
Element Candidates and Relationship Candidates together with Candidate-Set,
Approved-Input, Human-Review, exception, profile and generation provenance.

Phase I is responsible for assembling this authorized content into one coherent
Internal Engineering Model.

Phase I shall not repeat the semantic interpretation performed in Phase H.

Phase I shall not bypass Human Review.

Phase I shall not generate SysML v2 textual notation.

The Framework Template and Model Structure and Comparability Profile already
define the structural areas into which accepted Candidate content has been
classified.

The accepted Phase-H architecture requires Phase I to preserve Candidate
semantics exactly and to consume only the validated H→I read contract.

The implementation therefore requires a deterministic assembly layer that:

- materializes the selected framework hierarchy,
- maps accepted Element Candidates into that hierarchy,
- maps accepted Relationship Candidates to exact internal model elements,
- preserves Human Review and Approved-Input traceability,
- preserves accepted exceptions,
- validates internal assembly integrity,
- persists an immutable Internal Engineering Model snapshot,
- and provides an explicit read boundary for Phase J.

The CATIA engineering model describes architecture derivation/structuring and
architecture validation as related but distinct responsibilities. The
implementation roadmap separates these temporally:

- Phase I performs deterministic architecture assembly,
- Phase K performs broader model validation,
- Phase J performs SysML v2 textual serialization.

---

## Decision

### I-01 — Sole upstream authority

Phase I shall consume Phase-H engineering content exclusively through:

`ModelCandidateReadService.load_phase_i_input(project_id, candidate_set_id)`

Phase-I production assembly shall not independently read:

- raw Approved Inputs,
- Candidate Review persistence,
- unreviewed Candidate Sets,
- rejected Candidates,
- deferred Candidates,
- Model Proposal presentation state,
- or other upstream processing evidence

for the purpose of deciding which engineering content is assembled.

The H→I read contract remains the authorization boundary.

---

### I-02 — Handoff enrichment

`ModelCandidateAssemblyInput` shall be extended to include the exact:

- `framework_template_reference`
- `derivation_rules_reference`

already bound by the source Model Candidate Set.

Phase I shall not independently rediscover these references from unrelated
repository state.

This extension does not change Phase-H engineering semantics.

It strengthens the existing H→I authority transfer so Phase I receives all
required pinned assembly context through one validated contract.

---

### I-03 — Deterministic assembly

The normative Phase-I path shall be deterministic.

Phase I shall not require an LLM to reinterpret reviewed Candidate content.

The roadmap term "Model Generation Agent" is implemented architecturally as a
deterministic Internal Model Assembly capability.

Any optional future agent assistance shall remain non-authoritative and shall
not bypass this deterministic assembly contract.

---

### I-04 — Immutable Internal Engineering Model snapshot

Every materially distinct authorized assembly shall produce an immutable
Internal Engineering Model snapshot.

Identifier form:

`IEM-000001`

The Internal Engineering Model snapshot is a reproducible model-assembly
boundary.

Published IEM snapshots shall not be modified in place.

---

### I-05 — Exact authorization binding

Every IEM snapshot shall bind the exact Phase-I assembly input.

A deterministic `assembly_input_fingerprint` shall cover the authority-bearing
H→I input required to reproduce the assembly, including at least:

- project identity,
- Candidate Set identity and fingerprint,
- Approved-Input snapshot fingerprint,
- Framework Template reference,
- Model Structure Profile reference,
- derivation-rules reference,
- accepted Element Candidate identities and fingerprints,
- accepted Relationship Candidate identities and fingerprints,
- Candidate Review Decision references and fingerprints,
- accepted-exception references.

A later change in Human Review authorization therefore produces a different
assembly input even when the Candidate Set itself remains unchanged.

---

### I-06 — Idempotent exact reassembly

The combination of:

- identical `assembly_input_fingerprint`, and
- identical assembly-rule reference/version

shall be treated idempotently.

The system shall not silently create multiple semantically identical IEM
snapshots for the exact same authorized assembly state.

No implicit "latest IEM wins" behavior is permitted.

---

### I-07 — Separate Internal Model Element identity

Accepted Element Candidates shall be assembled as immutable Internal Model
Elements.

Identifier form:

`IME-000001`

The following identities remain distinct:

- Element Candidate identity (`MCE-...`)
- Internal Model Element identity (`IME-...`)
- semantic subject identity

An IME shall preserve explicit traceability to its source MCE.

---

### I-08 — Separate Internal Model Relationship identity

Accepted Relationship Candidates shall be assembled as immutable Internal Model
Relationships.

Identifier form:

`IMR-000001`

Every IMR shall:

- preserve its source MCR identity,
- reference exact source and target IMEs in the same IEM snapshot,
- preserve source and target semantic subject identities,
- preserve Approved-Input and Human-Review traceability.

---

### I-09 — Preserve accepted relationship semantics

Phase I shall preserve the accepted Candidate relationship semantics without
reinterpretation.

The following remain distinct and shall be retained:

- `relationship_family`
- `semantic_intent`
- `directionality`

Phase I shall not convert one accepted semantic intent into another.

The semantic-to-SysML-v2 serialization decision belongs to Phase J.

---

### I-10 — Template-derived structural skeleton

Phase I shall materialize the pinned Framework Template hierarchy as an
internal structural skeleton.

For the accepted Turing RFLP framework this includes:

```text
Stakeholder Level
├── Stakeholders
├── User Needs
├── Stakeholder Requirements
└── Use Cases

System Level
├── Requirements
├── Functional
├── Logical
└── Physical

Subsystem Level
├── Requirements
├── Functional
├── Logical
└── Physical
```

Internal structure nodes are organizational model structure.

They are not additional inferred engineering elements.

---

### I-11 — Deterministic containment

Every accepted IME shall be assigned to exactly one applicable Framework
Template mapping node.

The assignment shall be derived from the reviewed Candidate information already
established in Phase H, including:

- `model_area`
- `element_type`
- `framework_assignment`

Phase I shall verify this mapping against the pinned:

- Framework Template
- Model Structure and Comparability Profile

Phase I shall not semantically reclassify the element.

---

### I-12 — No invented engineering hierarchy

Phase I shall not infer additional engineering containment, decomposition or
ownership relationships merely from:

- element names,
- similarity,
- model area proximity,
- ordering,
- or structural convenience.

Any engineering hierarchy beyond the Framework Template structure requires
explicit reviewed Candidate semantics.

The Framework Template hierarchy itself may be materialized because it is
pinned configuration, not inferred engineering content.

---

### I-13 — Empty structural nodes allowed

The complete selected Framework Template structure may be materialized even
when one or more structural areas contain no accepted engineering elements.

Phase I shall not decide whether empty structural areas become explicit SysML v2
packages or other textual constructs.

That representation decision belongs downstream.

---

### I-14 — Accepted exceptions remain explicit

`accepted_exception` authorization shall remain explicit in the Internal
Engineering Model.

The affected IME or IMR shall preserve the exact associated Human Review
Decision reference.

Phase I shall not silently normalize an accepted exception into ordinary
conformance.

An accepted exception may authorize a reviewed structural deviation.

It shall not disable fundamental assembly-integrity requirements.

---

### I-15 — Fail closed on non-deterministically assemblable content

If accepted Candidate content cannot be assembled without introducing new
engineering semantics or making an unauthorized choice, Phase I shall fail
closed.

The system shall produce an explicit assembly finding/diagnostic rather than
inventing a solution.

The required resolution shall return to the applicable upstream authority,
which may include:

- Candidate interpretation,
- Human Review,
- Structure Profile,
- Framework Template,
- or architecture configuration.

---

### I-16 — Phase-I assembly-integrity validation

Phase I shall validate internal assembly integrity.

This validation includes at least:

- unique IEM / IME / IMR identities,
- project isolation,
- exact Candidate traceability,
- exact Human Review traceability,
- exact Approved-Input traceability,
- every IME assigned to a valid structural node,
- `element_type` compatibility with the selected model area/profile,
- every IMR endpoint resolving to an IME in the same IEM,
- no dangling Internal Model Relationships,
- no unauthorized Candidate content,
- preservation of accepted exceptions,
- no duplicate internal identities,
- deterministic snapshot fingerprint integrity.

Phase-I assembly validation shall not silently modify engineering content in
order to pass.

---

### I-17 — Phase I does not replace Phase K

Phase-I validation is limited to assembly and internal representation integrity.

Broader model validation remains Phase K, including applicable:

- architecture-constraint validation,
- structural-pattern validation,
- larger-context compatibility,
- interface consistency,
- relationship-semantic validation,
- target-model compatibility,
- and publication-blocking validation.

Phase I may report that content is not assemblable.

It shall not absorb the complete Phase-K validation responsibility.

---

### I-18 — No SysML v2 textual constructs in Phase I

The Internal Engineering Model remains representation-neutral with respect to
concrete SysML v2 textual syntax.

Phase I may retain engineering concepts such as:

- `system_requirement`
- `function`
- `logical_component`
- `allocated_to`
- `dependency`
- `flows_to`

Phase I shall not generate concrete textual constructs such as:

- `requirement`
- `part def`
- `action def`
- `dependency from ...`
- package serialization
- target-specific SysML v2 syntax

SysML v2 textual generation belongs to Phase J.

---

### I-19 — Explicit Phase-J read boundary

Phase J shall consume an explicitly selected Internal Engineering Model
snapshot through a validated read contract.

Conceptually:

```python
InternalModelReadService.load_phase_j_input(
    project_id,
    internal_engineering_model_id,
) -> InternalEngineeringModelSnapshot
```

There shall be no implicit latest-IEM selection.

The Phase-J read contract shall verify snapshot integrity and project isolation
before exposing the Internal Engineering Model downstream.

---

### I-20 — CATIA and logical-architecture alignment

The Internal Engineering Model corresponds conceptually to the architecture
result produced by the CATIA system behavior "Derive and Structure Architecture
Information".

The implementation separation is:

```text
LC_07 Architecture Synthesis and Validation
├── Phase I — synthesis / deterministic assembly
└── Phase K — broader architecture/model validation

LC_08 SysML v2 Artifact Generation
└── Phase J — SysML v2 textual generation
```

Implementation phases therefore do not map one-to-one to Logical Components.

This separation is intentional and shall be documented rather than treated as a
conflict between implementation and CATIA architecture.

---

## Internal Engineering Model artifacts

### Internal Engineering Model Manifest

The IEM manifest shall bind at least:

- schema version
- project identity
- IEM identity
- assembly-input fingerprint
- Candidate Set identity
- Candidate Set content fingerprint
- Approved-Input snapshot fingerprint
- Framework Template reference
- Model Structure Profile reference
- derivation-rules reference
- assembly-rules reference
- assembly provenance
- Internal Model Structure reference
- IME references
- IMR references
- Candidate Review Decision references
- accepted-exception references
- creation timestamp
- immutable content fingerprint

---

### Internal Model Element

Each IME shall support at least:

- schema version
- project identity
- IEM identity
- IME identity
- semantic subject identity
- source MCE identity
- source MCE fingerprint
- name
- description
- model area
- element type
- Framework assignment
- terminology assignment
- attributes
- comparison anchor where applicable
- Approved-Input references
- Human Review Decision reference
- accepted-exception reference where applicable
- immutable content fingerprint

---

### Internal Model Relationship

Each IMR shall support at least:

- schema version
- project identity
- IEM identity
- IMR identity
- source IME identity
- target IME identity
- source semantic subject identity
- target semantic subject identity
- relationship family
- semantic intent
- directionality
- source MCR identity
- source MCR fingerprint
- Approved-Input references
- Human Review Decision reference
- accepted-exception reference where applicable
- immutable content fingerprint

---

### Internal Model Structure

The Internal Model Structure shall contain:

- Framework Template identity
- deterministic materialized structure nodes
- parent/child structure-node relationships
- stable node ordering
- exact IME membership per structural node
- immutable structure fingerprint

The structure artifact shall not duplicate the complete engineering content of
the IME artifacts.

It represents model organization and containment.

---

## Persistence

The default project-local persistence layout is:

```text
data/projects/<project_id>/internal_models/
└── IEM-000001/
    ├── manifest.json
    ├── structure.json
    ├── elements/
    │   ├── IME-000001.json
    │   └── ...
    └── relationships/
        ├── IMR-000001.json
        └── ...
```

The complete IEM bundle shall be persisted atomically and immutably.

Partial publication shall not constitute a valid IEM snapshot.

Repository scanning shall fail closed on:

- corrupt manifests,
- missing referenced artifacts,
- unexpected artifact identities,
- cross-project references,
- fingerprint mismatch,
- symlink/path-safety violations,
- or interrupted temporary publication state.

---

## Assembly flow

The normative Phase-I flow is:

```text
ModelCandidateReadService
        │
        ▼
ModelCandidateAssemblyInput
        │
        ├── pinned Framework Template
        ├── pinned Model Structure Profile
        └── pinned assembly / derivation context
        │
        ▼
InternalModelAssemblyService
        │
        ├── materialize structural skeleton
        ├── MCE → IME
        ├── MCR → IMR
        ├── preserve exceptions
        ├── preserve traceability
        └── validate assembly integrity
        │
        ▼
Immutable InternalEngineeringModelSnapshot
        │
        ▼
InternalModelReadService
        │
        ▼
Phase J
```

---

## Human Review boundary

Phase I introduces no new normative Human Review layer for semantic
reinterpretation.

Human Review of engineering meaning occurs upstream.

If deterministic assembly cannot continue without a new engineering decision,
Phase I fails closed and surfaces the condition for upstream resolution.

Phase I shall not use an additional AI or Human Review step to silently modify
already accepted Candidate semantics.

---

## Phase boundaries

### Phase H

- interprets approved engineering information,
- proposes model elements and relationships,
- identifies alternatives,
- assesses comparability,
- performs Human Review,
- authorizes Candidate content for assembly.

### Phase I

- consumes only authorized Candidate content,
- materializes the framework structure,
- assembles Internal Model Elements,
- assembles Internal Model Relationships,
- preserves exact semantics and traceability,
- performs assembly-integrity validation,
- persists the immutable Internal Engineering Model.

### Phase J

- maps the Internal Engineering Model to supported SysML v2 constructs,
- applies the target-notation profile,
- generates SysML v2 textual notation.

### Phase K

- performs broader syntax, semantic, structural, integration, constraint,
  traceability and comparability validation as applicable.

---

## Consequences

### Positive consequences

- Phase-H Human Review remains authoritative.
- Model assembly is deterministic and reproducible.
- The Internal Engineering Model is independent of SysML v2 textual syntax.
- Framework hierarchy and engineering semantics remain separate concepts.
- Accepted exceptions remain visible.
- Every internal element and relationship retains exact upstream traceability.
- Phase J receives one coherent model rather than independent Candidate
  artifacts.
- Phase K remains a distinct validation responsibility.
- Repeated exact assembly is idempotent.
- Historical IEM snapshots remain reproducible.

### Trade-offs

- Phase I introduces IEM / IME / IMR identities.
- A separate internal structure artifact is required.
- H→I transfer must be enriched with pinned Framework Template and
  derivation-rules references.
- Repository and fingerprint validation add implementation work.
- Some accepted Candidate combinations may fail closed and require upstream
  resolution rather than automatic correction.

---

## Rejected alternatives

### Reinterpret accepted Candidates in Phase I

Rejected because this would bypass Phase-H Human Review and duplicate semantic
model derivation.

### Generate SysML v2 directly in Phase I

Rejected because model assembly and target-language serialization are separate
responsibilities and Phase J owns SysML v2 textual generation.

### Use Candidate IDs directly as final internal model identities

Rejected because Candidate identity, semantic continuity and assembled snapshot
identity have different lifecycle semantics.

### Treat the Framework Template as engineering content

Rejected because template structure is organizational configuration and does not
by itself create new engineering elements or engineering relationships.

### Automatically invent missing hierarchy

Rejected because additional engineering containment or decomposition requires
reviewed semantic evidence.

### Let Phase I perform all downstream model validation

Rejected because this would collapse assembly and validation and would conflict
with the planned Phase-K boundary.

### Select the latest Internal Engineering Model implicitly

Rejected because explicit snapshot identity is required for reproducibility and
traceability.

---

## Related decisions

- ADR-016 — Human Review Workspace and Approved Input Promotion Architecture
- ADR-017 — Simple-by-Default Interaction and Progressive Disclosure
- ADR-018 — Model Candidate Layer and Structural Comparability

---

## Initial implementation decomposition

After this ADR is committed, Phase I may proceed in the following internal
implementation slices:

```text
I1  identifiers + immutable Internal Model domain types
I2  manifests + fingerprints + H→I contract enrichment
I3  Framework/Profile resolution + structure materialization
I4  deterministic MCE/MCR → IME/IMR assembly
I5  repository + immutable persistence + assembly-integrity validation
I6  Phase-J read contract + regression + SSOT closeout
```

This decomposition is an implementation planning aid.

The official roadmap phase remains Phase I — Model Generation Agent / Internal
Engineering Model.

---

## Implementation note

No Phase-I production implementation shall precede acceptance and persistence of
this ADR.

Implementation shall reuse the existing:

- project-isolation rules,
- safe-path behavior,
- atomic-persistence patterns,
- identifier conventions,
- deterministic manifest/fingerprint patterns,
- Framework Template loader,
- Model Structure Profile loader,
- and H→I authorization contract

where applicable rather than duplicating upstream logic.


\newpage


# ADR-020 — Hybrid Target Projection and Coverage Architecture


## Status

Accepted

## Date

2026-08-13

## Context

Phase H derives target-model Candidates from reviewed Approved Inputs.

The existing `ProfileDrivenModelCandidateDeriver` is intentionally conservative
and deterministic. It maps reviewed information only when the selected Model
Structure Profile supports the mapping and fails closed when no supported
mapping exists or a mapping is ambiguous.

This behavior is valuable as a fast, reproducible projection path, but it is
not sufficient as the only target-projection strategy for heterogeneous
engineering information. A selected modeling framework must not force valid
engineering information into an unsuitable target-model shape, and information
that cannot be projected must not disappear silently.

The Phase-H orchestration already depends on the `ModelCandidateDeriver`
protocol rather than one concrete derivation implementation. This allows the
existing deterministic strategy to remain available while a second,
LLM-assisted strategy is introduced without changing the Phase-H → Phase-I
authority boundary.

Phase I remains responsible only for assembling human-authorized Model
Candidates into the Internal Engineering Model. Phase I shall not be reopened
for target-projection reasoning.

The executable prototype schedule requires a bounded extension. The existing
deterministic path shall therefore be preserved and reused as the first stage
of the hybrid path.

CATIA requirement/function reconciliation is intentionally deferred to Phase
N2, where capabilities and architecture decisions introduced during Phases
G–L are reconciled as one coherent model update.

---

## Decision

### H9-01 — Preserve strict deterministic projection

`ProfileDrivenModelCandidateDeriver` remains a supported target-projection
strategy.

It shall continue to:

- use only the pinned Framework Template, Model Structure Profile and
  derivation rules,
- derive only profile-supported Candidate semantics,
- fail closed on ambiguous or unsupported target mappings,
- and require no LLM execution.

This path is the fast / reproducible projection option and may be used for
quick checks, dry runs and inputs that are already sufficiently classified.

---

### H9-02 — Explicit projection dispositions

Every active Approved Input considered for target projection shall receive
exactly one projection disposition:

- `mapped`
- `ambiguous`
- `unmapped`
- `intentionally_not_projected`

`human_clarification` Approved Inputs remain reviewed context and are
`intentionally_not_projected` unless a later accepted architecture explicitly
changes that rule.

---

### H9-03 — Complete projection coverage

Projection coverage shall account for the complete active Approved-Input
snapshot.

The invariant is:

```text
mapped
+ ambiguous
+ unmapped
+ intentionally_not_projected
= total Approved Inputs considered
```

No Approved Input may disappear silently because it does not fit the selected
Framework or Model Structure Profile.

`unmapped` describes a limitation of the selected target projection, not a
rejection of the underlying approved engineering information.

---

### H9-04 — Shared deterministic profile resolver

The profile-matching logic shall be factored into one reusable deterministic
resolver.

Both strict deterministic projection and later LLM-assisted projection shall
use this resolver.

The system shall not maintain separate deterministic mapping semantics for the
two modes.

---

### H9-05 — Deterministic-first hybrid projection

The LLM-assisted strategy shall execute deterministic profile resolution first.

Inputs resolved uniquely by the deterministic resolver shall not be sent to
the LLM in the normal hybrid path.

Only inputs classified as `ambiguous` or `unmapped` are eligible for
LLM-assisted target-projection reasoning.

This limits token use, request rate and unnecessary semantic reinterpretation.

---

### H9-06 — LLM output remains Candidate-level modeling

The LLM-assisted mapper shall not generate normative SysML v2 text.

It may propose structured target-model semantics such as target model area,
element type, framework assignment, relationship semantic intent, rationale,
alternatives, or an explicit `unmapped` result.

Generated proposals remain Phase-H Candidate information.

---

### H9-07 — No forced mapping

The LLM-assisted path is not required to map every Approved Input.

If the selected Framework/Profile has no defensible representation, the result
shall remain explicitly `unmapped`.

The LLM shall not invent an engineering meaning merely to achieve target
coverage.

---

### H9-08 — Existing Human Review authority remains unchanged

LLM-assisted projections do not become approved engineering information merely
because a model produced them.

They shall pass the same Candidate validation and Human Review authority
boundary as deterministic Model Candidates.

The sole Phase-H → Phase-I boundary remains:

`ModelCandidateReadService.load_phase_i_input(project_id, candidate_set_id)`

No Phase-I contract change is required by H9.

---

### H9-09 — Projection provenance

The derivation path shall remain traceable through
`ModelCandidateGenerationProvenance`.

For LLM-assisted projection, provenance shall identify at least the applicable
derivation method, model reference, recipe and/or agent reference where
applicable, and context fingerprint.

Per-input LLM projection evidence shall remain attributable to the affected
Approved Input.

---

### H9-10 — Token and request-rate protection

The normal LLM-assisted path shall minimize model traffic by design.

It shall:

- perform deterministic resolution first,
- send only unresolved inputs,
- avoid resending complete source documents when bounded Approved-Input content
  is sufficient,
- send only relevant target-profile context,
- support bounded batching where appropriate,
- avoid reinterpreting uniquely mapped deterministic results,
- and avoid unbounded automatic retry loops.

Correctness and explicit uncertainty remain more important than forcing a
mapping.

---

### H9-11 — Phase J remains deterministic serialization

H9 changes target-model projection in Phase H.

It does not move semantic interpretation into Phase J.

Phase J continues to receive a validated Internal Engineering Model and
serialize already accepted model semantics to the selected SysML v2 target
notation.

---

### H9-12 — CATIA reconciliation is deferred to Phase N2

H9 may introduce capabilities that require requirement/function reconciliation,
including selectable deterministic and LLM-assisted target projection,
projection coverage assessment, explicit ambiguous/unmapped preservation,
deterministic-first LLM routing, and LLM projection provenance.

These are reconciliation candidates only.

No CATIA Requirement or Function shall be added or modified as part of the H9
implementation closeout. Final coverage assessment and model changes belong to
Phase N2.

---

## Initial implementation decomposition

```text
H9.1  Projection disposition and coverage model
H9.2  Shared deterministic profile resolver
H9.3  Strict deterministic deriver migrated to shared resolver
H9.4  Structured LLM projection contract
H9.5  Token-conscious HybridModelCandidateDeriver
H9.6  Merge, validation and provenance
H9.7  Human Review / Phase-I compatibility regression
H9.8  SSOT closeout and Phase-N2 reconciliation candidate recording
```

H9.1–H9.3 require no LLM request.

---

## Consequences

### Positive

- deterministic projection remains available and fast,
- framework limitations become visible instead of silently losing information,
- LLM usage is concentrated on cases that actually require semantic reasoning,
- Phase I remains stable,
- Human Review authority remains stable,
- the architecture supports future alternative Framework/Profile combinations,
- and Phase J can remain reproducible.

### Trade-offs

- Phase H gains an additional projection-coverage concept,
- hybrid execution will require explicit LLM response validation,
- unresolved information can legitimately remain unmapped,
- and final CATIA capability reconciliation is deferred until Phase N2.

---

## Acceptance


\newpage


# ADR-021 — SYSIDE-Compatible SysML v2 Generation Architecture


## Status

Accepted

## Date

2026-08-13

## Context

Phase H and the controlled H9 extension establish reviewed Model Candidates from
Approved Inputs.

Phase I assembles the exact human-authorized Candidate selection into one
immutable, representation-neutral Internal Engineering Model (IEM).

The current verified implementation baseline is:

`1cee49350dd2f24d6a1c80fb0aa1c0d2b5fd27fc`

Phase J now has one responsibility:

> deterministically serialize one explicitly selected, validated Internal
> Engineering Model snapshot into SysML v2 textual artifacts that are compatible
> with SYSIDE.

Phase J shall not repeat engineering interpretation.

Phase J shall not use an LLM to decide model meaning.

Phase J shall not use the CATIA model of the Turing Generator as a syntax
template for generated target models.

The Turing Generator's CATIA model describes the Turing Generator itself. It
remains the engineering authority for the product architecture and is relevant
to later Architecture-to-Requirements reconciliation, especially Phase N2. It
does not define the textual syntax that Phase J shall generate for arbitrary
target models.

The SysML v2 source/reference hierarchy for Phase J is instead:

```text
SysML v2 specification / release repository
        │
        │ primary language and syntax reference
        ▼
SYSIDE
        │
        │ target execution / validation environment
        ▼
validated local syntax experiments
        │
        │ implementation evidence for the supported subset
        ▼
Apollo 11 SysML v2 example project
        │
        │ non-normative structure / style reference only
        ▼
Turing Generator target-notation and generation profiles
```

The repository already records the local reference locations:

```text
external/sysml-v2-release/
external/apollo11-sysml-v2/
```

`context/sources/source_manifest.json` defines the SysML v2 Release repository
as the primary specification reference and Apollo 11 as a non-normative example
reference.

`context/sysml/sysml_v2_spec_reference.json` is a curated working reference. It
is intentionally not treated as the complete grammar and explicitly requires
consulting the local SysML v2 release repository and validating uncertain
syntax in the selected environment.

`context/examples/apollo11_structure_reference.md` records useful Apollo
organization and modeling patterns, but explicitly prevents Apollo from
overriding the SysML v2 specification or the Turing target notation.

The current target-notation artifact is:

`context/sysml/sysml_v2_target_notation.json`

Version:

`0.1.0`

It already constrains generation to a deliberately limited SysML v2 subset, but
its generation-state language predates the completed Phase-H/Phase-I
architecture. It still refers to `approved_model_data` as direct generation
input and associates generation directly with `data/output/`.

The accepted architecture is now:

```text
Approved Input
→ reviewed Model Candidates
→ Internal Engineering Model
→ Phase J generation
→ Phase K validation
→ Phase L publication
```

Therefore Phase J requires a clean serialization architecture that preserves
the IEM authority boundary, separates syntax policy from semantic mapping and
artifact organization, fails closed when a mapping is unsupported, and produces
an exact validation-ready artifact set for Phase K.

---

## Decision

### J-01 — Sole upstream authority

Phase J shall consume engineering-model content exclusively through:

```python
InternalModelReadService.load_phase_j_input(
    project_id,
    internal_engineering_model_id,
) -> InternalEngineeringModelSnapshot
```

The IEM shall be explicitly addressed.

No implicit "latest IEM" selection is allowed.

Phase-J generation shall not independently read Model Candidates, Approved
Inputs, Review persistence, raw ingestion artifacts or presentation state in
order to decide what model content is generated.

The Phase-I read boundary remains authoritative for the exact model snapshot
supplied to Phase J.

---

### J-02 — Deterministic serialization only

The normative Phase-J generation path shall be deterministic.

Phase J shall not use an LLM or agent to:

- reinterpret engineering meaning,
- choose between semantic alternatives,
- invent relationships,
- infer additional hierarchy,
- choose a target-model area,
- repair unsupported semantics,
- or generate free-form SysML v2 text.

Engineering interpretation occurs in Phase H/H9 and is authorized through Human
Review.

Phase I assembles that authorization without semantic reinterpretation.

Phase J is therefore a compiler/serializer from accepted internal semantics to a
controlled textual representation.

For identical:

- IEM content,
- target-notation reference,
- generation-profile reference,
- artifact-structure reference,
- and generator implementation/rule version,

the generated textual content shall be byte-identical.

---

### J-03 — Phase-J syntax and reference authority hierarchy

Phase J shall apply the following hierarchy when defining or extending generated
SysML v2 syntax:

1. local SysML v2 specification/release repository
2. successful validation in SYSIDE
3. intentionally maintained local syntax fixtures/experiments
4. Apollo 11 as a non-normative example reference
5. project-specific style preferences

The local SysML v2 release repository remains the primary language and syntax
reference.

SYSIDE is the target environment against which the generated subset shall be
validated.

Apollo 11 may inform:

- package organization,
- separation of definitions and usages,
- naming style,
- large-model structuring,
- and examples of valid modeling patterns.

Apollo 11 shall not override the specification reference or a SYSIDE validation
result.

---

### J-04 — CATIA remains separate from the Phase-J syntax authority

The CATIA model of the Turing Generator is the engineering model of the Turing
Generator product itself.

It may define or constrain what the Turing Generator shall be capable of doing.

It shall not be used as the normative textual syntax source for Phase J.

In particular:

```text
CATIA Turing Generator model
→ product requirements / functions / logical architecture
→ Phase-N2 reconciliation authority

SysML v2 specification + SYSIDE
→ generated language / syntax authority for Phase J
```

A future independent CATIA example model may be used as an interoperability test
case.

Such a model shall remain non-normative for the Phase-J syntax mapping unless a
later accepted architecture decision explicitly changes that rule.

---

### J-05 — Three separate versioned generation-policy artifacts

Phase J shall separate three concerns that are currently partially mixed.

#### Target Notation Profile

Path:

`context/sysml/sysml_v2_target_notation.json`

Responsibility:

> Which SysML v2 textual constructs are allowed to be generated?

The target notation defines the permitted language subset and syntax patterns.

It shall not decide which IEM semantic maps to which construct.

It shall not decide file/package organization.

The current `0.1.0` artifact shall be intentionally revised during J1 before it
is used as the production Phase-J target-notation contract.

The revision shall update stale pre-H/I generation-state language and preserve
the principle:

> Reduce MVP scope by limiting the generated SysML v2 subset, not by accepting
> invalid syntax.

#### Generation Profile

Proposed path:

`context/sysml/turing_sysml_v2_generation_profile.json`

Responsibility:

> How is each supported IEM semantic represented using allowed target-notation
> constructs?

It shall define explicit mappings for:

- IEM element types,
- model areas where required to disambiguate representation,
- relationship family,
- semantic intent,
- directionality,
- permitted attribute projections,
- documentation projection,
- and accepted-exception representation.

#### Artifact Structure Profile

Proposed path:

`context/sysml/turing_sysml_v2_artifact_structure.json`

Responsibility:

> How is one generated model organized into output units, packages, names and
> deterministic ordering?

It shall define at least:

- root-package policy,
- Framework-node → package projection,
- default output-unit strategy,
- package naming,
- deterministic ordering,
- relative output path rules,
- and future multi-file extensibility.

These three references shall be independently versioned and fingerprinted.

---

### J-06 — Exact generation context is pinned

Every Phase-J generation attempt shall bind the exact:

- source IEM identity and content fingerprint,
- Target Notation Profile identity/version/fingerprint,
- Generation Profile identity/version/fingerprint,
- Artifact Structure Profile identity/version/fingerprint,
- generator-rule/implementation reference where applicable.

Generation shall not silently load a newer profile after an artifact set has
been created.

The exact generation context shall be sufficient to reproduce the generated
content.

---

### J-07 — Preflight mapping completeness before rendering

Phase J shall perform deterministic generation preflight before producing a
successful artifact set.

The preflight shall verify at least:

- IEM snapshot integrity has already passed the I→J boundary,
- every materialized Framework node has a deterministic package projection,
- every used IEM `element_type` has one applicable explicit mapping rule,
- every used relationship semantic has one applicable explicit mapping rule,
- mapping rules reference only allowed target-notation constructs,
- relationship directionality is supported by the selected mapping,
- every IMR endpoint resolves to a generated IME symbol,
- generated symbols are unique,
- names and documentation can be safely serialized,
- accepted exceptions remain representable and traceable,
- and the requested artifact structure can be rendered without an implicit
  semantic choice.

Any missing or ambiguous required mapping is blocking.

Phase J shall not partially succeed by silently omitting unsupported IEM
content.

---

### J-08 — Unsupported semantics fail closed

An unsupported IEM semantic shall produce an explicit blocking generation
finding.

Examples include:

- unsupported element type,
- unsupported relationship semantic intent,
- unsupported directionality,
- target construct not allowed by the pinned Target Notation Profile,
- or a semantic mapping that would require a new engineering decision.

Phase J shall not apply a generic fallback such as:

```text
unknown relationship
→ dependency
```

merely to produce syntactically valid output.

Phase J shall not replace a required formal relationship with a documentation
note when doing so would change or discard the accepted engineering semantics.

Resolution belongs to the relevant upstream profile, modeling decision or target
notation extension.

---

### J-09 — Target-notation extension requires validated syntax evidence

A new SysML v2 construct shall not become generator output merely because a
plausible syntax pattern is known.

Before a previously unsupported construct is activated in the Target Notation
Profile, J1 shall:

1. identify the required semantic and construct,
2. inspect the local SysML v2 release/specification material,
3. create the smallest useful local syntax fixture,
4. validate the fixture in SYSIDE,
5. record the accepted syntax pattern,
6. add the construct to the Target Notation Profile,
7. add its mapping to the Generation Profile,
8. add automated regression tests.

This applies especially to currently unresolved Phase-J needs such as:

- Use Case representation,
- allocation,
- dependency variants,
- derivation,
- refinement,
- satisfaction,
- interaction,
- flow,
- and traceability relationships.

No construct shall be guessed from its English name.

---

### J-10 — Framework structure projects deterministically to package structure

The materialized `InternalModelStructure` is organizational structure already
authorized by Phase I.

Phase J may project that exact structure into SysML v2 packages without
inventing engineering hierarchy.

The default structure shall retain:

```text
Stakeholder Level
├── Stakeholders
├── User Needs
├── Stakeholder Requirements
└── Use Cases

System Level
├── Requirements
├── Functional
├── Logical
└── Physical

Subsystem Level
├── Requirements
├── Functional
├── Logical
└── Physical
```

Package names are a textual representation of the pinned Framework structure.

They are not additional engineering elements.

Phase J shall not infer additional containment from:

- names,
- IME ordering,
- element similarity,
- relationship proximity,
- or convenience.

---

### J-11 — Empty configured structure may remain explicit

The default artifact-structure profile may render the complete configured
Framework skeleton, including empty nodes, as packages.

This supports structural comparability across generated model snapshots.

An empty package shall not be interpreted as proof that the corresponding
engineering scope is complete.

If a later Artifact Structure Profile chooses to omit empty packages, that
behavior shall be explicit, versioned and deterministic.

---

### J-12 — Default MVP output uses one SysML v2 unit

The domain contract shall permit one or more generated units.

The default MVP Artifact Structure Profile shall initially generate one `.sysml`
text unit containing the complete package hierarchy.

Reason:

- minimizes fragile cross-file references,
- provides one simple validation target,
- keeps the first closed vertical slice small,
- and preserves future extensibility through the artifact-set abstraction.

The architecture therefore supports:

```text
GeneratedSysMLArtifactSet
├── Unit 1
├── Unit 2
└── ...
```

while the initial profile selects:

```text
GeneratedSysMLArtifactSet
└── generated_model.sysml
```

A later multi-file profile shall not require redesigning the IEM or generation
domain model.

---

### J-13 — Element mapping is explicit and profile-controlled

Every generated engineering element shall originate from exactly one IEM element
and one applicable Generation Profile rule.

The profile may distinguish identical `element_type` values by `model_area`.

This is required because, for example, `function` is used for both:

- `system.functional`
- `subsystem.functional`

The generator shall not infer representation from an element name.

Likely MVP families include:

```text
requirements         → requirement constructs
functions            → action constructs
logical components   → part constructs
physical components  → part constructs
```

However, exact mappings become normative only after their Target Notation
constructs and SYSIDE syntax patterns are validated and recorded.

Stakeholders, User Needs and Use Cases shall likewise receive explicit validated
rules before production generation.

---

### J-14 — Stable generated symbols are separate from engineering names

Each generated IEM element shall receive a deterministic machine-safe generated
symbol derived from immutable internal identity, not from a potentially mutable
or colliding display name.

Conceptually:

```text
IME-000042
→ IME_000042
```

The exact textual symbol pattern shall be specified in the Artifact Structure or
Generation Profile and validated against SYSIDE identifier rules.

The engineering `name` remains a separate human-readable property.

Phase J shall not make technical identity depend solely on:

- display-name uniqueness,
- capitalization,
- whitespace,
- punctuation,
- or automatic name normalization.

This preserves stable relationship endpoint references even when engineering
names are similar.

---

### J-15 — Untrusted text is escaped and never inserted raw

IEM names, descriptions and generic attributes may originate from heterogeneous
legacy information.

Phase J shall treat them as data, not as trusted SysML v2 syntax.

The renderer shall have explicit deterministic escaping/sanitization rules for:

- generated identifiers,
- quoted names where used,
- documentation blocks,
- line breaks,
- comment delimiters,
- and other syntax-sensitive characters.

No source-provided text may inject additional SysML v2 statements through raw
string concatenation.

If text cannot be represented under the selected rule set without loss or
ambiguity, generation shall fail closed with a finding.

---

### J-16 — Generic IEM attributes are not automatically formal SysML attributes

`InternalModelElement.attributes` contains generic name/value data.

Phase J shall not blindly transform every generic pair into formal SysML v2
attribute syntax.

The Generation Profile shall distinguish:

```text
explicitly supported formal attribute
→ formal target construct

generic metadata / trace information
→ documentation and/or machine-readable traceability

unsupported semantic attribute
→ blocking finding when omission would lose engineering meaning
```

This prevents arbitrary legacy metadata from silently becoming formal model
semantics.

---

### J-17 — Relationship serialization preserves accepted semantics exactly

Every generated relationship shall originate from exactly one IEM relationship.

The renderer shall preserve the accepted distinction between:

- `relationship_family`
- `semantic_intent`
- `directionality`

The Generation Profile shall map an exact supported tuple to an exact
Target-Notation construct.

Conceptually:

```text
(
    relationship_family,
    semantic_intent,
    directionality,
)
→ one explicit rendering rule
```

A different SysML construct may not be substituted merely because it looks
similar.

Phase J shall not alter relationship direction to simplify rendering.

---

### J-18 — Accepted exceptions remain explicit

An accepted exception in the IEM remains an accepted reviewed deviation.

Phase J shall not normalize it away.

The generated artifact set shall preserve exact traceability to the associated
Human Review Decision.

Where the selected target notation supports a faithful formal representation,
the content may be rendered normally while the exception remains explicit in
traceability.

If the accepted exception cannot be represented without semantic loss, Phase J
shall fail closed.

---

### J-19 — Deterministic ordering and formatting

Generated content shall have one canonical deterministic ordering.

The default ordering shall derive only from stable configured or internal
information such as:

1. Artifact Structure Profile order
2. Framework node order
3. IEM element identity
4. IEM relationship identity

The renderer shall not depend on:

- filesystem enumeration order,
- dictionary insertion from uncontrolled input,
- current timestamp,
- random values,
- LLM response order,
- or locale-specific sorting.

Formatting shall be canonical so that exact regeneration can be compared
byte-for-byte.

---

### J-20 — Machine-readable traceability accompanies the SysML v2 text

Phase J shall produce structured traceability together with textual SysML v2.

Each generated element/relationship trace entry shall support the chain:

```text
generated unit / symbol / location
→ IME or IMR
→ source MCE or MCR
→ Approved Input reference(s)
→ Candidate Human Review Decision
→ accepted exception where applicable
```

Traceability shall not rely only on human-readable `doc` text.

The generated SysML v2 text may contain concise traceability documentation when
allowed by the profile, but the machine-readable traceability artifact remains
the exact Phase-J evidence boundary.

Where practical, trace entries may include deterministic line ranges after
rendering.

---

### J-21 — Generated artifact set is a distinct immutable domain contract

A successful Phase-J generation returns one immutable
`GeneratedSysMLArtifactSet`.

Conceptually it shall contain at least:

```text
schema_version
project_id
source_internal_engineering_model_id
source_iem_content_fingerprint

target_notation_reference
generation_profile_reference
artifact_structure_reference

generation_input_fingerprint
generation_provenance

units[]
traceability_entries[]
nonblocking_diagnostics[]

content_fingerprint
```

Each generated unit shall contain at least:

```text
unit_id
relative_path
content
content_fingerprint
generated_symbol_ids
source_ime_ids
source_imr_ids
```

The artifact-set fingerprint shall bind the complete successful generated state.

Timestamps, temporary paths and other non-semantic runtime metadata shall not
affect deterministic generation identity.

---

### J-22 — Exact generation identity and idempotence

Phase J shall calculate a deterministic `generation_input_fingerprint` covering
at least:

- exact source IEM identity and fingerprint,
- exact Target Notation Profile reference/fingerprint,
- exact Generation Profile reference/fingerprint,
- exact Artifact Structure Profile reference/fingerprint,
- exact generator rules/implementation reference required for reproducibility.

The same exact generation identity shall produce byte-identical artifact
content and artifact fingerprints.

Phase J shall not silently select newer profiles under the same prior generation
identity.

---

### J-23 — Generation findings are explicit and typed

Phase J shall expose structured generation findings rather than embedding all
errors in exception strings.

A finding shall support at least:

```text
code
message
issue_level
target_type
target_id
profile_rule_id
blocking
```

Expected finding families include:

```text
UNSUPPORTED_ELEMENT_MAPPING
UNSUPPORTED_RELATIONSHIP_MAPPING
UNSUPPORTED_DIRECTIONALITY
TARGET_CONSTRUCT_NOT_ALLOWED
UNRENDERABLE_IDENTIFIER
UNSAFE_DOCUMENTATION_CONTENT
DUPLICATE_GENERATED_SYMBOL
UNRESOLVED_GENERATED_ENDPOINT
STRUCTURE_PROJECTION_ERROR
PROFILE_REFERENCE_MISMATCH
```

A blocking finding prevents a successful `GeneratedSysMLArtifactSet`.

---

### J-24 — Phase J validates generation integrity, not full SysML correctness

Phase J owns deterministic generation-integrity checks, including:

- complete mapping coverage,
- allowed-construct use,
- symbol uniqueness,
- endpoint resolvability,
- deterministic package membership,
- canonical formatting,
- and internal traceability completeness.

Phase J does not replace Phase K.

In particular, Phase J does not claim full validation merely because the
renderer emitted text matching an expected template.

---

### J-25 — Explicit Phase-J → Phase-K boundary

The successful output of Phase J is the immutable
`GeneratedSysMLArtifactSet`.

Conceptually Phase K consumes it through:

```python
SysMLValidationService.validate(
    artifact_set: GeneratedSysMLArtifactSet,
) -> SysMLValidationResult
```

The exact Phase-K service contract may be refined in the Phase-K architecture
decision, but Phase J shall expose all data required for:

- SYSIDE syntax/parser validation,
- reference resolution,
- Target Notation Profile conformance,
- structural validation,
- semantic validation where applicable,
- traceability validation,
- and publication-blocking findings.

Phase K shall not silently rewrite generated text to make validation pass.

---

### J-26 — SYSIDE validation belongs to Phase K, while J1 provides syntax evidence

There is an intentional distinction between:

#### J1 syntax experiments

Small controlled fixtures used to establish supported generator patterns before
a mapping is activated.

and:

#### Phase K product validation

Validation of an actual complete generated artifact set.

Therefore:

- J1 may use SYSIDE to validate candidate syntax patterns.
- Phase J production does not claim an artifact is valid merely because its
  individual templates were previously validated.
- Phase K validates the actual generated result.

---

### J-27 — Phase L alone performs final publication

Phase J shall not publish final generated output directly to `data/output/`.

Phase J creates the validation-ready artifact-set contract.

Phase K validates it.

Phase L owns final versioned publication of accepted generated output.

The existing Source Manifest rule that generated SysML v2 output belongs in:

`data/output/`

remains valid for the final published product artifact.

It does not require Phase J to bypass validation and publish directly.

The closed path is:

```text
IEM
→ Phase J GeneratedSysMLArtifactSet
→ Phase K SysMLValidationResult
→ Phase L versioned data/output/ publication
```

---

### J-28 — Generated output is not the Turing Generator architecture model

Generated customer/target-model output and the engineering model of the Turing
Generator are separate artifact classes.

Phase J shall never overwrite or treat as target output:

- the CATIA model of the Turing Generator,
- its textual export,
- or the temporary SYSIDE shadow model of the Turing Generator.

Generated model output belongs to the project/output publication boundary
defined downstream.

---

### J-29 — No hidden semantic fallback through documentation

Documentation blocks are allowed for:

- descriptions,
- rationale,
- traceability,
- assumptions,
- review references,
- and metadata explicitly designated as documentation.

Documentation shall not be used as a hidden fallback to claim that an
unsupported formal semantic has been preserved.

Example:

```text
accepted IMR = satisfies
```

If the active Generation Profile requires a formal `satisfies` representation
and no validated supported target construct exists, writing:

```text
doc /* A satisfies B */
```

does not count as successful semantic serialization.

The generation is blocked until the mapping is explicitly supported or the
upstream modeling decision changes.

---

### J-30 — Quality is reduced by subset, not by semantic loss

The Phase-J MVP may support only a controlled subset of SysML v2.

It may therefore block generation of an IEM that requires unsupported constructs.

It shall not claim success by:

- dropping accepted elements,
- dropping accepted relationships,
- weakening relationship types,
- flattening engineering semantics,
- inventing equivalent-looking constructs,
- or embedding unsupported model meaning only in prose.

A smaller valid and faithful supported subset is preferred over broader
apparently successful but semantically lossy output.

---

## Initial generation-domain contracts

The following contracts are proposed for Phase J.

### TargetNotationReference

Pinned identity of the active Target Notation Profile.

At least:

```text
context_id
version
content_fingerprint
```

### SysMLGenerationProfileReference

Pinned identity of the IEM → SysML semantic mapping profile.

At least:

```text
profile_id
profile_version
profile_fingerprint
```

### SysMLArtifactStructureReference

Pinned identity of the artifact/package layout profile.

At least:

```text
profile_id
profile_version
profile_fingerprint
```

### SysMLGenerationContext

At least:

```text
target_notation_reference
generation_profile_reference
artifact_structure_reference
generator_rules_reference
```

### SysMLGenerationFinding

At least:

```text
code
message
issue_level
target_type
target_id
profile_rule_id
blocking
```

### GeneratedSysMLUnit

At least:

```text
unit_id
relative_path
content
content_fingerprint
generated_symbol_ids
source_internal_model_element_ids
source_internal_model_relationship_ids
```

### GeneratedSysMLTraceabilityEntry

At least:

```text
generated_unit_id
generated_symbol_id
generated_location

source_internal_engineering_model_id
source_internal_model_element_id | null
source_internal_model_relationship_id | null

source_model_candidate_id
approved_input_references
review_decision_reference
accepted_exception_reference | null
```

### GeneratedSysMLArtifactSet

At least:

```text
schema_version
project_id

source_internal_engineering_model_id
source_iem_content_fingerprint

generation_context
generation_input_fingerprint
generation_provenance

units
traceability_entries
nonblocking_diagnostics

content_fingerprint
```

---

## Generation flow

The normative Phase-J production flow is:

```text
InternalModelReadService
        │
        ▼
validated explicit InternalEngineeringModelSnapshot
        │
        ▼
Generation Context Resolver
        │
        ├── Target Notation Profile
        ├── Generation Profile
        └── Artifact Structure Profile
        │
        ▼
Generation Preflight
        │
        ├── mapping completeness
        ├── construct allow-list
        ├── symbol planning
        ├── escaping/renderability
        └── endpoint planning
        │
        ▼
Package Projection
        │
        ▼
Element Rendering
        │
        ▼
Relationship Rendering
        │
        ▼
Traceability Projection
        │
        ▼
Canonical Unit Formatting
        │
        ▼
Immutable GeneratedSysMLArtifactSet
        │
        ▼
Phase K validation
```

No LLM exists in this production flow.

---

## Reference and validation flow for new syntax

When Phase J requires a construct not yet supported by the active target
notation:

```text
required IEM semantic
        │
        ▼
inspect local SysML v2 release repository
        │
        ▼
smallest useful syntax fixture
        │
        ▼
validate fixture in SYSIDE
        │
   ┌────┴────┐
   │         │
 valid     invalid
   │         │
   ▼         ▼
record      do not activate
pattern     mapping
   │
   ▼
extend Target Notation Profile
   │
   ▼
extend Generation Profile
   │
   ▼
automated generator regression
```

Apollo 11 may be consulted to find representative usage patterns, but it does
not replace the specification/SYSIDE validation steps.

---

## Default artifact structure

The initial MVP profile shall target conceptually:

```text
generated_model.sysml

package GeneratedModel {
    package StakeholderLevel {
        package Stakeholders { ... }
        package UserNeeds { ... }
        package StakeholderRequirements { ... }
        package UseCases { ... }
    }

    package SystemLevel {
        package Requirements { ... }
        package Functional { ... }
        package Logical { ... }
        package Physical { ... }
    }

    package SubsystemLevel {
        package Requirements { ... }
        package Functional { ... }
        package Logical { ... }
        package Physical { ... }
    }
}
```

The example is architectural and not yet an accepted literal syntax fixture.

Exact package identifier/quoting rules shall be established through the
Target-Notation and Artifact Structure Profiles.

---

## Relationship to existing context artifacts

### `context/sources/source_manifest.json`

Continues to define:

- SysML v2 release repository as the primary specification reference,
- Apollo 11 as non-normative example reference,
- generated output as a distinct artifact class.

### `context/sysml/sysml_v2_spec_reference.json`

Remains the compact working reference for routine implementation.

It is not the complete grammar.

Uncertain or newly required constructs trigger direct reference to the local
SysML v2 release repository.

### `context/sysml/sysml_v2_target_notation.json`

Becomes the Phase-J allowed-construct contract.

J1 shall revise the current `0.1.0` profile before production generation.

### `context/examples/apollo11_structure_reference.md`

Remains a non-normative structure/style reference.

No Apollo engineering content, CoSMA framework or package hierarchy becomes
Turing output policy merely because it appears in Apollo.

### CATIA Turing Generator model

Remains the engineering authority for the Turing Generator product.

It is not a Phase-J target-syntax reference.

Any capability gaps introduced or discovered during J–L are recorded for later
Phase-N2 Architecture-to-Requirements reconciliation.

---

## Persistence and publication boundary

Phase J creates a deterministic immutable artifact-set value.

A successful Phase-J result is not yet final published output.

Diagnostic implementations may serialize temporary artifacts for tests or
run-owned evidence, but such files do not become final generated product
artifacts merely because they exist on disk.

Final product publication remains Phase L after required Phase-K validation.

`data/output/` is therefore the final published generated-output location, not a
reason to bypass the validation boundary.

---

## Phase-J implementation slices

The accepted implementation decomposition after this ADR is intended to be:

```text
J1  Generation foundation
    - errors and identifiers
    - immutable generation domain contracts
    - profile references / fingerprints
    - Target Notation 0.2 cleanup
    - controlled syntax fixtures for currently required unsupported constructs

J2  Generation profiles and preflight
    - Generation Profile
    - Artifact Structure Profile
    - deterministic profile loaders
    - mapping completeness
    - construct allow-list validation
    - blocking generation findings

J3  Package, symbol and canonical ordering projection
    - Framework structure → packages
    - stable generated symbols
    - deterministic naming / escaping
    - canonical ordering

J4  Element rendering
    - explicit element mapping rules
    - safe documentation projection
    - supported attribute projection
    - element traceability

J5  Relationship rendering
    - explicit relationship semantic mapping
    - directionality
    - exact generated endpoint binding
    - no generic fallback

J6  Artifact-set and traceability assembly
    - GeneratedSysMLUnit
    - GeneratedSysMLTraceabilityEntry
    - GeneratedSysMLArtifactSet
    - deterministic generation fingerprints
    - exact regeneration/idempotence tests

J7  Phase-K boundary and completion
    - explicit validation handoff
    - H9/I→J compatibility regression
    - full repository regression
    - SSOT update
    - Phase-N2 reconciliation candidate recording
```

The slices may be internally subdivided without changing the accepted
architecture.

---

## Phase-J acceptance criteria

Phase J is complete only when:

- one explicit validated IEM can be generated without bypassing the I→J boundary,
- generation is deterministic and requires no LLM,
- every generated construct is allowed by the pinned Target Notation Profile,
- every rendered IEM semantic is covered by one explicit Generation Profile rule,
- unsupported required semantics fail closed,
- no accepted IEM element or relationship disappears silently,
- source-provided text cannot inject SysML syntax,
- generated relationships bind exact generated endpoints,
- machine-readable IEM → generated-artifact traceability is complete,
- repeated exact generation is byte-identical,
- the artifact set is suitable for Phase-K validation,
- J does not publish final output directly,
- representative supported syntax patterns have SYSIDE validation evidence,
- focused Phase-J tests pass,
- the complete repository regression passes,
- `git diff --check` passes,
- and SSOT is synchronized.

---

## Consequences

### Positive consequences

- Human-reviewed engineering semantics remain authoritative.
- Phase J is reproducible and testable.
- LLM variability cannot alter generated syntax or model meaning.
- SYSIDE compatibility becomes an explicit target rather than an assumed
  by-product.
- SysML v2 specification authority remains separate from example-project style.
- Apollo 11 remains useful without becoming normative.
- The CATIA Turing Generator model is no longer conflated with target syntax.
- Unsupported semantics are visible instead of silently weakened.
- Target notation, semantic mapping and artifact layout can evolve independently.
- The same IEM may later be serialized using a different accepted artifact
  structure without re-running semantic interpretation.
- The generated artifact set provides a precise input to Phase K.
- Final publication remains protected by Phase K and Phase L.

### Trade-offs

- Phase J introduces additional versioned profiles.
- Not every IEM may initially be generatable with the limited MVP subset.
- New semantic constructs require explicit syntax experiments and SYSIDE
  validation.
- One-file MVP output favors robustness over immediate large-project modularity.
- Stable generated symbols may be less visually elegant than display-name-based
  identifiers but provide stronger identity and endpoint stability.
- Maintaining machine-readable traceability adds artifact volume.
- Strict failure on unsupported semantics may require target-notation/profile
  extensions before a complete model can be generated.

These trade-offs are accepted because the objective is valid, faithful,
traceable SYSIDE-compatible SysML v2 generation rather than broad but lossy
text emission.

---

## Alternatives considered

### Alternative A — LLM generates SysML v2 directly from the IEM

Rejected.

Reason:

- creates a second semantic interpretation step after Human Review,
- reduces reproducibility,
- complicates validation,
- can silently alter accepted relationship meaning,
- increases token/request use,
- and makes exact regeneration difficult.

### Alternative B — Generate directly from Model Candidates

Rejected.

Reason:

- bypasses the accepted Phase-I authority boundary,
- duplicates internal-model assembly responsibilities,
- and risks generation from content outside the exact IEM snapshot.

### Alternative C — Use Apollo 11 as the syntax template

Rejected.

Reason:

- Apollo 11 is intentionally non-normative,
- contains domain- and methodology-specific patterns,
- and shall not override the SysML v2 specification or SYSIDE validation.

### Alternative D — Use the Turing Generator CATIA model as the generator syntax template

Rejected.

Reason:

- it models the Turing Generator product,
- it is not the normative source for target-model textual syntax,
- and conflating product architecture with serialization policy would create a
  false authority relationship.

### Alternative E — Emit generic dependencies for unsupported relationships

Rejected.

Reason:

- syntactic success would hide semantic loss,
- accepted Human Review meaning would be altered downstream,
- and generated output would no longer faithfully represent the IEM.

### Alternative F — Write directly to `data/output/` in Phase J

Rejected.

Reason:

- bypasses the planned validation/publication separation,
- conflates generated work product with accepted published artifact,
- and weakens the Phase-K/Phase-L gates.

### Alternative G — Start immediately with many separate `.sysml` files

Deferred.

Reason:

- increases cross-file reference complexity before the base mapping is proven,
- broadens the first SYSIDE validation surface,
- and is unnecessary for the initial closed vertical slice.

The artifact-set contract deliberately leaves multi-file generation open for a
later Artifact Structure Profile.

---

## CATIA / Phase-N2 reconciliation

This ADR makes an implementation-architecture decision for Phase J.

It does not modify the CATIA model.

Capabilities or constraints that may require CATIA Requirement/Function
reconciliation shall be recorded during implementation and reconciled in Phase
N2 together with the other G–L architecture changes.

Potential N2 reconciliation topics introduced or made explicit by ADR-021
include:

- deterministic generation from the validated Internal Engineering Model,
- explicit SYSIDE-compatible target-notation generation,
- versioned semantic-to-SysML generation profiles,
- fail-closed unsupported semantic behavior,
- machine-readable generated-artifact traceability,
- deterministic generation identity/idempotence,
- and separation of generation, validation and publication.

No SYSR, SF, Logical Component or allocation is created or changed by this ADR.

---

## Decision state after acceptance

After explicit acceptance of ADR-021:

```text
Phase H/H9: COMPLETE
Phase I:    COMPLETE
ADR-021:    ACCEPTED
Phase J:    architecture accepted
Next:       J1 — Generation foundation and validated syntax-fixture preparation
```

No Phase-J implementation begins before explicit acceptance of this ADR.


\newpage


# ADR-022 — SysML v2 Validation Layer Architecture


## Status

Accepted

## Date

2026-08-14

## Context

Phase J is completed and provides one deterministic, immutable validation-ready
`GeneratedSysMLArtifactSet`.

The accepted end-to-end boundary is:

```text
Internal Engineering Model
→ Phase J — deterministic SysML v2 generation
→ GeneratedSysMLArtifactSet
→ Phase K — validation
→ SysMLValidationResult
→ Phase L — versioned publication
```

Phase J already guarantees generation-time integrity including deterministic
serialization, exact generation-policy references, generated unit fingerprints,
artifact-set fingerprints, complete IME/IMR coverage, generated locations and
machine-readable traceability.

Phase K has a different responsibility.

It shall validate the complete generated artifact before publication without:

- regenerating SysML v2,
- changing generated text,
- reinterpreting engineering semantics,
- bypassing the Phase-J artifact contract,
- or publishing output itself.

The roadmap requires Phase K to cover:

- syntax validation,
- Target Notation validation,
- Artifact Structure validation,
- relationship and endpoint consistency,
- traceability,
- comparability/profile consistency,
- deterministic findings,
- and a fail-closed Phase-L publication gate.

The current Phase-J syntax evidence was established through small controlled
SYSIDE fixtures. Those checks authorize supported syntax patterns but do not
constitute validation of a complete generated target model.

Phase K therefore requires both deterministic Turing-specific validation and an
explicit external SysML v2 compatibility-validation boundary.

---

## Decision

### K-01 — Sole upstream validation subject

The sole normative Phase-K input is one explicit immutable:

`GeneratedSysMLArtifactSet`

The service boundary is:

```python
SysMLValidationService.validate(
    artifact_set: GeneratedSysMLArtifactSet,
) -> SysMLValidationResult
```

Phase K shall not independently reload:

- Approved Inputs,
- Model Candidates,
- Candidate Review persistence,
- raw Processing evidence,
- UI state,
- or the source IEM for semantic reinterpretation

in order to determine whether the generated model is valid.

There is no implicit latest-artifact selection.

The exact validation subject is bound by
`GeneratedSysMLArtifactSet.content_fingerprint`.

---

### K-02 — Validation is observational and never repairs

Phase K shall never silently modify generated content in order to make validation
pass.

Invalid content shall produce explicit findings and block publication.

Phase K shall not:

- rename generated symbols,
- reorder generated packages,
- alter relationship endpoints,
- substitute target constructs,
- normalize engineering descriptions,
- auto-format and replace generated content,
- or invoke Phase J to silently regenerate corrected output.

Resolution belongs to the phase, policy artifact or implementation responsible
for the finding.

---

### K-03 — Two-layer validation architecture

Phase K shall contain two intentionally separate validation layers.

#### Deterministic internal validation

Implemented inside the Turing Generator.

It validates Turing-specific generated-artifact invariants including:

- artifact identity and fingerprint integrity,
- generation-context integrity,
- Target Notation reference integrity,
- Generation Profile reference integrity,
- Artifact Structure reference integrity,
- Generator Rules reference integrity,
- generated-unit structure,
- target-subset conformance,
- package and artifact-layout rules,
- relationship and endpoint consistency,
- traceability integrity,
- accepted-exception preservation,
- and publication-policy invariants.

These checks require no external modeling tool.

#### External SysML v2 compatibility validation

Implemented through an explicit external-validator adapter.

The MVP target validator is the SYSIDE Modeler CLI using its non-mutating
validation/check capability.

External validation establishes parser/tool compatibility for the actual
complete generated artifact set.

Internal deterministic validation shall not attempt to become a complete SysML
v2 parser.

External validation shall not replace Turing-specific deterministic contract
validation.

Both layers are required for a publication-ready PASS.

---

### K-04 — Separate versioned Validation Profile

Phase K introduces:

```text
context/sysml/turing_sysml_v2_validation_profile.json
```

Initial identity:

```text
profile_id:      TURING_SYSML_V2_VALIDATION
profile_version: 1.0.0
```

The Validation Profile defines:

> Which validation checks are required, how findings are classified, which
> external validator is required, and what constitutes a Phase-K publication
> PASS.

It shall define at least:

- required internal validators,
- required external validator,
- external-validator availability policy,
- diagnostic severity policy,
- publication-blocking policy,
- diagnostic normalization policy,
- and validation-result fingerprint policy.

It shall not redefine:

- SysML v2 target syntax,
- IEM → SysML semantic mappings,
- package organization,
- generator formatting,
- or engineering semantics.

Those responsibilities remain with the existing Target Notation, Generation
Profile, Artifact Structure Profile, Generator Rules and Model Structure /
Comparability Profile.

---

### K-05 — Exact generation-policy resolution

`GeneratedSysMLArtifactSet.generation_context` contains exact references and
fingerprints for:

- Target Notation,
- Generation Profile,
- Artifact Structure Profile,
- Generator Rules.

Phase K shall resolve these exact referenced policy artifacts and require exact
fingerprint agreement.

Conceptually:

```text
artifact-set pinned reference
        │
        ▼
resolve exact policy artifact
        │
        ▼
validate policy artifact
        │
        ▼
recalculate fingerprint
        │
        ├── exact match → continue
        └── mismatch / unavailable → blocking finding
```

Phase K shall not silently substitute a newer policy version.

The Phase-J generation baseline is:

```text
CTX_SYSML_V2_TARGET_NOTATION        0.2.0
TURING_SYSML_V2_GENERATION         1.0.0
TURING_SYSML_V2_ARTIFACT_STRUCTURE 1.0.0
TURING_SYSML_V2_GENERATOR_RULES    1.0.0
```

A generated artifact whose exact generation policy cannot be resolved shall not
be publishable.

---

### K-06 — Standalone artifact-set boundary validation

Phase K shall independently validate the received Phase-J artifact contract
without requiring the original IEM snapshot.

Checks shall include at least:

- `GeneratedSysMLArtifactSet` content fingerprint,
- generated-unit content fingerprints,
- generation-input fingerprint,
- unit identity uniqueness,
- unit path uniqueness,
- safe relative paths,
- generated-symbol uniqueness,
- traceability-entry uniqueness,
- traceability unit references,
- traceability generated-symbol references,
- traceability line ranges,
- source-IEM identity consistency,
- element/relationship trace coverage consistency,
- controlled line-ending invariants,
- and textual-unit integrity.

This is a boundary check of received evidence.

It does not remove Phase J's responsibility to build correct artifacts.

---

### K-07 — Target Notation validation

Phase K shall confirm that generated output remains inside the exact permitted
Target Notation subset.

The internal validator shall validate the deliberately constrained Turing
Generator output forms rather than implement the complete SysML v2 grammar.

The MVP production subset currently includes generated forms for:

- Package,
- Documentation block,
- Requirement Usage,
- Use Case Definition,
- Action Usage,
- Part Usage,
- Dependency,
- Allocation,
- Satisfaction,
- and qualified references.

A generated construct outside the resolved Target Notation contract is blocking.

Full syntax/model interpretation remains the responsibility of external SYSIDE
validation.

---

### K-08 — Artifact Structure validation

The generated artifact set shall be checked against the exact resolved Artifact
Structure Profile.

For the current MVP this includes at least:

```text
one generated unit
unit id GSU-000001
relative path generated_model.sysml
root package GeneratedModel
complete configured Framework package hierarchy
configured empty packages retained
canonical Framework ordering
canonical element ordering
canonical relationship ordering
relationships placed at the configured root location
safe relative output paths
```

Phase K validates generated structure.

It shall not infer additional engineering containment.

---

### K-09 — Relationship and endpoint validation

Phase K shall validate generated relationship consistency independently of
Phase-J rendering.

For every generated relationship it shall validate, where deterministically
observable:

- the relationship construct is permitted,
- both referenced endpoints resolve,
- endpoint resolution is unambiguous,
- endpoint target constructs are compatible,
- endpoint element kinds are compatible,
- qualified endpoint references are valid,
- and required endpoint rendering/order is preserved.

The active production rules include:

```text
allocation
  source: Feature
  target: Feature

dependency / depends_on
  source: Feature or Definition
  target: Feature or Definition

satisfaction
  IEM semantic: source satisfies target
  generated form: satisfy TARGET by SOURCE
  source: PartUsage or ActionUsage Feature
  target: RequirementUsage Feature
```

Phase K validates generated-model consistency.

It shall not reinterpret the original IMR semantic intent.

The reviewed IEM semantic → target-construct mapping remains a Phase-J
generation responsibility.

---

### K-10 — Scope of semantic, constraint and comparability validation

The roadmap terms:

- relationship semantic validation,
- constraint validation,
- comparability-profile validation

shall be interpreted according to the accepted Phase-H/I/J authority boundaries.

Phase K validates publication-relevant generated-model consistency.

It shall not rerun Phase-H semantic interpretation or Human Review.

For the MVP:

#### Relationship semantic validation

Means confirming that the generated relationship is permitted by the pinned
Generation Profile and that endpoint roles are compatible with the target
construct.

#### Constraint validation

Means deterministic generated-artifact and target-policy constraints applicable
to publication.

It does not mean executing arbitrary product-design constraints or simulating
engineering behavior.

#### Comparability-profile validation

Means validating consistency of the pinned generation-policy chain with the
expected Model Structure / Comparability Profile and preservation of reviewed
exception traceability.

Candidate-level comparability decisions shall not be recomputed in Phase K.

---

### K-11 — Traceability validation

Every generated engineering representation must remain traceable.

Phase K shall validate that the transferred chain remains structurally complete:

```text
generated location
→ GeneratedSysMLTraceabilityEntry
→ source IEM
→ IME or IMR
→ Model Candidate
→ Approved Input
→ Human Review Decision
→ accepted exception where applicable
```

Every generated element and relationship representation shall have exactly one
applicable traceability entry.

Generated line locations remain one-based and inclusive.

A finding associated with a generated element or relationship shall reference
the existing Phase-J traceability key:

```text
generated_unit_id
+
generated_symbol_id
```

Findings applying to complete units or package structure may reference the unit
and generated location only.

---

### K-12 — Immutable normalized validation findings

Phase K shall produce immutable normalized findings.

Conceptually:

```python
SysMLValidationFinding(
    code=...,
    category=...,
    severity=...,
    blocking=...,
    message=...,
    generated_unit_id=...,
    generated_symbol_id=...,
    generated_location=...,
    validator_id=...,
    validator_rule_id=...,
)
```

Finding categories shall distinguish at least:

```text
artifact_integrity
validation_context
target_notation
artifact_structure
relationship_consistency
traceability
external_syntax
external_semantics
external_warning
validator_infrastructure
```

Severity shall use a controlled vocabulary:

```text
info
warning
error
```

`blocking` is explicit and controlled by the Validation Profile.

Findings shall have deterministic canonical ordering.

Recommended ordering:

```text
blocking first
→ category
→ generated unit
→ line
→ column where available
→ code
→ message
```

---

### K-13 — SYSIDE external-validator adapter

SYSIDE shall be integrated through a dedicated adapter rather than directly into
the Phase-K orchestration service.

Conceptually:

```text
SysMLExternalValidator
        ▲
        │
SysideCliValidator
```

The adapter shall receive the exact generated units and materialize an isolated
ephemeral validation workspace.

Generated units shall be written there byte-for-byte using their configured
relative paths.

The workspace shall contain no unrelated project models.

The adapter shall then execute a controlled, non-mutating SYSIDE validation
operation conceptually equivalent to:

```text
syside check
```

The concrete invocation shall use deterministic/noninteractive settings where
supported.

Temporary absolute filesystem paths shall not become part of deterministic
validation identity.

External diagnostics shall be normalized to generated-unit-relative locations.

---

### K-14 — External validator identity

Every external validation execution shall record the actual validator identity.

At least:

- validator ID,
- tool name,
- tool version,
- validation command contract,
- validator configuration fingerprint.

For SYSIDE, the installed version shall be discovered at execution time and
recorded.

The external-validator identity participates in the validation execution
fingerprint.

A different SYSIDE version therefore represents a materially different
validation environment.

---

### K-15 — Unavailable external validator is incomplete, never PASS

External validator infrastructure may be unavailable because, for example:

- SYSIDE CLI is not installed,
- required licensing is unavailable,
- the executable cannot start,
- the validator version is unsupported,
- execution crashes or times out,
- required standard-library context is unavailable.

Such states shall not be reported as `invalid`.

They shall also never be reported as `valid`.

Instead:

```text
validation_status = incomplete
publication_gate = blocked
```

with an explicit blocking `validator_infrastructure` finding.

This preserves the distinction between:

```text
invalid model
```

and:

```text
required validation could not be completed
```

while remaining fail-closed.

---

### K-16 — Three-state validation result

`SysMLValidationResult` shall support exactly three normative overall statuses:

```text
valid
invalid
incomplete
```

#### valid

All required internal checks completed.

All required external validators completed.

No blocking findings exist.

```text
publication_gate = passed
```

#### invalid

Validation completed sufficiently to establish one or more actual blocking
model/artifact defects.

```text
publication_gate = blocked
```

#### incomplete

A required validator or validation context could not be resolved or executed
sufficiently to establish validity.

```text
publication_gate = blocked
```

Only:

```text
validation_status == valid
AND
publication_gate == passed
```

is eligible for Phase L.

---

### K-17 — Warning policy

Internal and external warnings shall remain visible.

Warnings are not automatically equivalent to errors.

The Validation Profile determines whether a warning category or specific
validator rule blocks publication.

The MVP default policy is:

```text
SYSIDE warning
→ severity = warning
→ blocking = false
```

unless an explicit Turing validation rule elevates the finding.

Errors remain blocking.

The MVP shall therefore not globally use a
`warnings-as-errors` publication policy.

---

### K-18 — Deterministic validation identity and fingerprints

Phase K shall calculate an exact validation-input fingerprint from at least:

- `GeneratedSysMLArtifactSet.content_fingerprint`,
- Validation Profile reference and fingerprint,
- resolved external-validator identity/version,
- external-validator configuration fingerprint,
- validation command-contract identifier.

Conceptually:

```text
validation_input_fingerprint =
SHA256(
    artifact-set fingerprint
  + validation policy
  + resolved validator environment
)
```

If a required validator is unavailable, the resolved environment shall contain
an explicit unavailable state rather than omit validator identity.

The final `SysMLValidationResult.content_fingerprint` shall cover deterministic
result content including:

- validation-input fingerprint,
- validation status,
- publication gate,
- normalized findings,
- external-validator evidence,
- artifact-set reference,
- Validation Profile reference.

Deterministic validation identity shall not include:

- wall-clock timestamps,
- temporary workspace paths,
- execution duration,
- ANSI formatting,
- machine-specific absolute paths.

For identical artifact, validation policy, validator version/configuration and
normalized validation result, the same validation-result fingerprint shall be
produced.

---

### K-19 — No separate Phase-K persistence authority required for the MVP

Phase K does not require a separate mutable validation repository merely to
perform validation.

The normative Phase-K result is the immutable:

```text
SysMLValidationResult
```

Its cryptographic fingerprint is the validation-result identity transferred to
Phase L.

Phase L may persist the validation result/report together with the published
versioned output package.

A future validation-history repository may be added without changing the Phase-K
service boundary.

---

### K-20 — Deterministic human-readable validation report

The machine-readable `SysMLValidationResult` is the authoritative Phase-K
output.

A human-readable validation report may be projected deterministically from that
result.

It shall summarize at least:

- project identity,
- source IEM identity,
- source GeneratedSysMLArtifactSet fingerprint,
- Validation Profile identity,
- SYSIDE validator identity/version,
- internal validation status,
- external validation status,
- blocking findings,
- warnings,
- publication-gate result,
- traceability references.

The human-readable report is a projection.

It is not separate validation authority.

---

### K-21 — Exact fingerprint-bound Phase-L publication gate

Phase L shall accept only the exact artifact set covered by the successful
validation result.

Conceptually:

```python
OutputWriter.publish(
    artifact_set: GeneratedSysMLArtifactSet,
    validation_result: SysMLValidationResult,
)
```

Phase L must verify:

```text
validation_result.source_artifact_set_fingerprint
==
artifact_set.content_fingerprint
```

and:

```text
validation_result.validation_status == "valid"
validation_result.publication_gate == "passed"
```

A validation result for another artifact set shall never authorize publication.

Any modification of generated content invalidates the publication gate through
the artifact-set fingerprint.

---

### K-22 — Findings report likely resolution ownership

Validation findings may identify their likely resolution boundary without
automatically changing that boundary.

Examples:

```text
artifact fingerprint mismatch
→ corrupted/invalid Phase-J artifact transfer

Target Notation reference mismatch
→ generation-policy/reference issue

unsupported generated target form
→ Target Notation / Generation Profile / generator issue

package-layout mismatch
→ Artifact Structure / generator issue

unresolved relationship endpoint
→ generator / generated-artifact consistency issue

SYSIDE syntax error
→ generator or Target Notation syntax issue

SYSIDE semantic/type error
→ Generation Profile / endpoint mapping / generator issue

traceability mismatch
→ Phase-J artifact assembly issue

SYSIDE unavailable
→ validation infrastructure issue
```

Phase K reports the issue.

It does not repair engineering content.

---

### K-23 — Controlled larger-context compatibility

The MVP external validation workspace shall validate exactly the generated
artifact set plus the validator's controlled SysML standard-library context.

No unrelated repository model shall be loaded implicitly.

Future compatibility validation against additional target-model libraries or
larger engineering contexts may be added through versioned Validation Profile
configuration.

Additional context shall be explicitly pinned and fingerprinted.

Ambient filesystem discovery is not permitted as hidden validation authority.

---

### K-24 — No LLM in the normative validation path

The normative Phase-K path is deterministic/tool-based.

No LLM is required to:

- decide whether syntax is valid,
- determine endpoint compatibility,
- decide publication eligibility,
- repair invalid models,
- or reinterpret validation findings.

An LLM may later explain findings in a UI.

Such explanation remains non-authoritative and cannot alter the validation
result or publication gate.

---

## Phase-K Output Contract

Conceptually:

```python
@dataclass(frozen=True, slots=True)
class SysMLValidationResult:
    schema_version: str

    project_id: str
    source_internal_engineering_model_id: str
    source_artifact_set_fingerprint: str

    validation_profile_reference: SysMLValidationProfileReference
    validation_input_fingerprint: str

    external_validator_evidence: (
        tuple[SysMLExternalValidationEvidence, ...]
    )

    findings: tuple[SysMLValidationFinding, ...]

    validation_status: str
    publication_gate: str

    content_fingerprint: str
```

A wall-clock timestamp is not required in the deterministic domain contract.

Operational publication/run metadata may record time independently without
changing validation identity.

---

## Normative Phase-K Flow

```text
GeneratedSysMLArtifactSet
        │
        ▼
Artifact-set boundary integrity
        │
        ▼
Resolve exact generation policy references
        │
        ▼
Resolve Validation Profile
        │
        ▼
Deterministic internal validation
        │
        ├── target notation
        ├── artifact structure
        ├── relationship/endpoints
        └── traceability
        │
        ▼
Materialize isolated temporary validation workspace
        │
        ▼
SYSIDE CLI adapter
        │
        ▼
Normalize external diagnostics
        │
        ▼
Merge + canonical-order findings
        │
        ▼
validation status
        │
        ├── valid
        ├── invalid
        └── incomplete
        │
        ▼
publication gate
        │
        ├── passed
        └── blocked
        │
        ▼
Immutable SysMLValidationResult
        │
        ▼
Phase L
```

---

## Initial Implementation Decomposition

After acceptance, the initial implementation decomposition is:

```text
K1  Validation domain foundation + Validation Profile
K2  Artifact/context/Target-Notation/Structure/Traceability validators
K3  Relationship + endpoint consistency validator
K4  SYSIDE CLI adapter + deterministic diagnostic normalization
K5  SysMLValidationService + status/gate/fingerprint assembly
K6  J→K→L boundary regression + Phase-K closeout
```

The SYSIDE integration path shall be tested separately from the fast
deterministic unit-test suite.

---

## Phase Boundaries

### Phase J owns

- IEM semantic → SysML construct mapping,
- rendering,
- escaping,
- package projection,
- canonical textual generation,
- generated traceability,
- `GeneratedSysMLArtifactSet` assembly.

### Phase K owns

- received artifact integrity,
- target-policy conformance,
- generated-model consistency,
- external SYSIDE compatibility,
- validation findings,
- validation identity,
- publication gate.

### Phase L owns

- version allocation,
- `data/output/` persistence,
- published package manifest,
- persisted validation evidence,
- immutable output references,
- downloadable/inspectable packaging.

---

## Explicit Non-Goals

Phase K shall not:

- reload Candidates to reinterpret them,
- change Human Review authority,
- mutate the IEM,
- regenerate SysML v2,
- repair generated text,
- run formatting that replaces generated content,
- invent missing model semantics,
- publish files,
- create implicit latest-artifact selection,
- silently skip external validation,
- treat validator unavailability as success,
- load ambient unrelated model context,
- replace SYSIDE with an ad-hoc regex implementation of the full SysML grammar.

---

## Consequences

### Positive

- Phase J, K and L remain cleanly separated.
- Validation is reproducible and fail-closed.
- External validator unavailability cannot accidentally authorize publication.
- Turing-specific invariants remain deterministic.
- Complete generated models receive actual SYSIDE compatibility validation.
- Validation findings remain exactly traceable to generated engineering
  evidence.
- Warning policy remains explicit rather than tool-dependent.
- The K→L gate is cryptographically bound to the exact validated artifact set.
- No semantic Human Review authority is reopened during validation.
- A future alternative external validator can be introduced behind the same
  adapter contract.

### Trade-offs

- Phase K gains its own versioned Validation Profile.
- External validation depends on available SYSIDE infrastructure.
- A valid internal result is insufficient for publication when required external
  validation cannot run.
- Validator versions become part of reproducibility evidence.
- Diagnostic normalization requires an explicit adapter layer.

---

## Acceptance

The accepted key decisions are:

1. `GeneratedSysMLArtifactSet` is the sole normative Phase-K input.
2. Phase K combines deterministic internal checks with external SYSIDE
   validation.
3. Phase K introduces a separate versioned Validation Profile.
4. SYSIDE CLI is the MVP external-validation boundary.
5. Validation has `valid`, `invalid` and `incomplete` overall states.
6. Required-validator unavailability means `incomplete` and blocks publication.
7. Warnings remain visible but are not automatically publication-blocking.
8. Phase K does not reinterpret source-IEM or Candidate semantics.
9. Phase-L publication authority is fingerprint-bound to the exact successfully
   validated artifact set.
10. No separate Phase-K persistence layer is required for the MVP.


\newpage


# ADR-023 — Final Model Review and Output Publication Architecture


## Status

Accepted

## Date

2026-08-14

## Context

Phase J and Phase K are completed and provide an explicit generated-model and
validation boundary:

```text
Internal Engineering Model
→ Phase J — deterministic SysML v2 generation
→ GeneratedSysMLArtifactSet
→ Phase K — deterministic + external validation
→ SysMLValidationResult
```

Phase J produces concrete `.sysml` units together with deterministic generation
identity, fingerprints and machine-readable traceability.

Phase K validates the exact generated artifact without modifying it. It produces
an immutable `SysMLValidationResult` with the normative states:

```text
valid
invalid
incomplete
```

and the publication gate:

```text
passed
blocked
```

ADR-022 already defines the exact fingerprint-bound K→L publication condition
and Phase K implements:

```python
validate_phase_l_handoff(
    artifact_set: GeneratedSysMLArtifactSet,
    validation_result: SysMLValidationResult,
) -> None
```

That gate correctly prevents publication unless the validation result:

- belongs to the same Project,
- belongs to the same source IEM,
- covers the exact generated artifact fingerprint,
- has `validation_status == "valid"`,
- and has `publication_gate == "passed"`.

However, automated validation alone does not constitute final engineering
release authority.

The generated SysML v2 is the first point in the workflow where a human can
inspect the complete resulting model representation as:

- generated SysML v2 code,
- structural/model diagrams,
- generated relationships,
- validation findings,
- traceability,
- upstream model-candidate evidence,
- generation rationales,
- and available agent/personality proposals.

A final Human-in-the-Loop review is therefore required before generated output
becomes an authoritative published project output.

The user shall be able to:

- inspect the generated model directly in the UI,
- inspect the exact generated `.sysml` code,
- inspect a diagrammatic model projection,
- navigate between model elements and generated code,
- inspect validation findings and their locations,
- inspect upstream agent/personality proposals and rationales where available,
- accept or reject the generated result,
- request changes,
- propose changes through code or diagram interaction,
- provide structured feedback,
- optionally request another bounded LLM/agent proposal loop,
- and explicitly approve one exact reviewed revision for final publication.

Generated-but-not-approved model results must therefore be persisted as
project-local review evidence before final publication.

Only after explicit Human approval may an output become a final versioned
project result under `data/output/`.

This ADR refines the original Phase-L concept from a pure "Output Writer" into
a controlled:

```text
Final Model Review
+
Human Release Gate
+
Output Publication
```

architecture.

---

## Decision

### L-01 — Phase-L responsibility

Phase L owns the final model review and controlled publication boundary.

The accepted end-to-end flow is:

```text
Approved Input
→ Model Candidates
→ Candidate Human Review
→ Internal Engineering Model
→ deterministic SysML v2 generation
→ automated validation
→ Final Model Human Review
→ explicit Human release approval
→ immutable versioned publication
```

Phase L shall not replace or weaken the earlier Human Review gate in Phase H.

The two review gates have different purposes:

```text
Phase H
candidate-level engineering approval
before authoritative Internal Engineering Model assembly

Phase L
complete generated-model review and release approval
before final project publication
```

Both are required.

---

### L-02 — Review entry is distinct from publication eligibility

A Phase-L review workspace may be created for an exact:

```text
GeneratedSysMLArtifactSet
+
SysMLValidationResult
```

even when the K result is:

```text
invalid / blocked
```

or:

```text
incomplete / blocked
```

This is intentional because validation findings are useful Human Review
evidence and may drive a correction loop.

Review entry shall still require exact integrity and binding:

- artifact-set integrity,
- validation-result integrity,
- same Project,
- same source IEM,
- exact artifact-set fingerprint binding.

The existing:

```python
validate_phase_l_handoff(...)
```

remains the normative **final publication gate**.

It shall not be weakened merely to allow review of invalid or incomplete
results.

A separate Phase-L review-subject boundary may validate exact subject binding
without requiring `valid + passed`.

---

### L-03 — Generated-but-not-approved output is review evidence, not published output

Generated `.sysml` files that have not received final Human release approval
shall not be treated as final published model output.

They shall be persisted in a project-local Final Model Review workspace.

Conceptually:

```text
data/projects/<project_id>/
└── final_model_reviews/
    └── FMR-000001/
        ├── manifest.json
        ├── revisions/
        │   ├── FRV-000001/
        │   └── FRV-000002/
        └── decisions/
```

These artifacts are:

- project-bound,
- inspectable,
- immutable once a review revision is created,
- traceable,
- and non-authoritative for final publication.

The legacy global principle:

```text
generated SysML v2 output → data/output/
```

is refined as follows:

```text
generated SysML under review
→ project-local Final Model Review workspace

Human-approved published SysML
→ data/output/<project_id>/OUT-xxxxxx/
```

This refinement preserves `data/output/` as the location of final generated
output while preventing unreleased drafts from being mistaken for published
project results.

---

### L-04 — Stable project-local Final Model Review identities

Phase L shall introduce explicit project-local identities using the established
six-digit identifier policy.

Initial identity families are:

```text
FMR-000001  Final Model Review
FRV-000001  Final Model Review Revision
FRI-000001  Final Model Review Item
FRD-000001  Final Model Review Decision
OUT-000001  Published Output Package
```

Identifiers are:

- project-local,
- monotonically increasing,
- immutable,
- never gap-reused after publication/persistence,
- and independent from display names.

Exact identifier modules and allocation mechanics belong to implementation but
shall follow the existing repository-wide deterministic identifier pattern.

---

### L-05 — Immutable review revisions

A Final Model Review is a long-lived review container.

Each concrete review subject is an immutable Final Model Review Revision.

One revision binds exactly:

- Project ID,
- source IEM ID,
- `GeneratedSysMLArtifactSet.content_fingerprint`,
- `SysMLValidationResult.content_fingerprint`,
- generated units and their fingerprints,
- generation-policy references,
- Validation Profile reference,
- validation status and publication gate,
- traceability references,
- applicable upstream Candidate/Review evidence references,
- applicable agent/personality proposal evidence,
- and the deterministic review-view fingerprint.

A revision shall never be edited in place.

If generated content or validation evidence changes, a new revision is created.

Conceptually:

```text
FMR-000001
├── FRV-000001  generated + reviewed, changes requested
├── FRV-000002  regenerated + revalidated, changes requested
└── FRV-000003  regenerated + revalidated, approved
```

Earlier revisions remain immutable evidence.

---

### L-06 — Final Model Review UI projection

Phase L shall provide a deterministic non-authoritative review projection for
the focused UI.

Conceptually:

```python
FinalModelReviewView
```

It shall present at least:

- review identity and revision,
- current review state,
- model summary,
- diagrammatic structural view,
- exact generated SysML v2 code,
- generated-unit selection where multiple units exist,
- generated symbols,
- relationship views,
- validation status,
- blocking findings,
- warnings,
- traceability,
- upstream Candidate decisions,
- generation rationale,
- available agent/personality proposals,
- unresolved review items,
- required Human decisions,
- and the next action.

The view is a projection over immutable evidence.

It shall not itself become engineering or publication authority.

---

### L-07 — Diagram and SysML code are linked review surfaces

The UI shall support both:

```text
diagram / model view
```

and:

```text
exact generated `.sysml` code
```

as first-class review surfaces.

Where possible, selection shall be cross-linked through existing generated
identity and traceability:

```text
diagram element / relationship
→ generated_symbol_id
→ GeneratedSysMLTraceabilityEntry
→ generated unit + line location
→ exact SysML code
```

and conversely:

```text
generated code location
→ generated symbol
→ model element / relationship
→ upstream traceability
```

The UI shall not reconstruct engineering authority by reverse-parsing the
generated SysML.

Diagrammatic views shall preferentially use the authoritative upstream model
structure and existing traceability while the code view displays the exact
Phase-J output.

---

### L-08 — Direct UI edits create change proposals, not silent authoritative mutations

The user may interactively modify or annotate:

- generated SysML code,
- diagram relationships,
- model structure,
- attributes represented in the review UI,
- and review comments.

However, an edit shall not silently mutate:

- the source IEM,
- the existing `GeneratedSysMLArtifactSet`,
- the existing `SysMLValidationResult`,
- or a published output.

A UI edit shall create explicit review evidence such as a change proposal or
structured change request bound to the current review revision.

For code editing, the UI may provide an editor experience conceptually similar
to:

```text
edit generated_model.sysml
→ inspect diff
→ save as change proposal
```

The saved proposal shall preserve at least:

- exact base artifact fingerprint,
- affected unit,
- affected generated symbols where resolvable,
- original content or location reference,
- proposed content/diff,
- reviewer identity,
- reviewer rationale,
- and change classification.

A proposed edit is not publishable generated output.

---

### L-09 — Change requests are routed to the owning authority boundary

Phase L shall not use manual final-code editing to bypass the Internal
Engineering Model.

A requested change shall be classified by its likely resolution boundary.

At minimum:

```text
engineering_semantics
→ Phase H / Candidate Review / IEM revision path

generated_representation
→ Phase J generation policy / generator correction

validation_policy_or_tool
→ Phase K validation policy / validator correction

review_presentation_only
→ Phase-L UI/read-model correction without changing model authority
```

Examples:

```text
"Function A belongs to another Logical Component."
→ engineering_semantics

"The correct accepted relationship should be satisfaction, not allocation."
→ engineering_semantics

"The model is correct but the generator emitted the wrong target syntax."
→ generated_representation

"SYSIDE rejects a construct that the Turing validator considered valid."
→ generation / Target Notation / validation investigation

"The diagram label is misleading but the underlying model is correct."
→ review_presentation_only
```

The correction shall occur at the owning layer and then re-enter the downstream
pipeline.

Phase L shall not invent a hidden third engineering authority in edited SysML
text.

---

### L-10 — Optional bounded LLM/agent revision loop

A Human reviewer may request another agent/LLM-assisted model proposal cycle
from Final Model Review.

The request shall be explicit and shall preserve the Human feedback that
triggered it.

Conceptually:

```text
Final Model Review
→ Human change request / feedback
→ optional bounded agent/LLM re-proposal
→ proposed Candidate changes / alternatives
→ existing Candidate Human Review
→ new authoritative IEM
→ deterministic J generation
→ K validation
→ new Final Model Review Revision
```

LLM or agent execution may:

- propose alternative model elements,
- propose alternative relationships,
- explain tradeoffs,
- identify gaps,
- compare candidate structures,
- and respond to reviewer feedback.

It shall not:

- directly rewrite final published SysML,
- approve its own proposals,
- bypass Candidate Human Review,
- bypass IEM assembly,
- bypass deterministic Phase J,
- bypass Phase K,
- or create final release authority.

The normative chain remains:

```text
agent/LLM proposal
→ Human decision
→ authoritative model state
→ deterministic generation
→ validation
→ final Human release approval
```

---

### L-11 — Agent/personality evidence is visible but non-authoritative

Final Model Review shall surface available proposal evidence from different
agent roles or modeling personalities where such evidence exists.

The review projection should preserve, where available:

- agent/personality identity,
- proposal,
- rationale,
- confidence/support information,
- alternatives,
- semantic uncertainty,
- source evidence,
- generation provenance,
- and prior Human decision.

The UI may present multiple perspectives side by side.

Example:

```text
Current accepted model relation:
allocated_to

Systems Engineer perspective:
allocated_to
rationale: ...

Architecture Reviewer perspective:
dependency
rationale: ...

Human options:
keep current
request change
request another proposal cycle
inspect upstream evidence
```

Selecting or favoring an alternative does not directly modify the final model.

It creates or contributes to a Human-reviewed upstream change request.

Agent agreement shall remain review evidence, not approval authority.

---

### L-12 — Every material revision must be regenerated and revalidated

Any material change affecting engineering content or generated SysML requires a
new downstream artifact chain.

The required loop is:

```text
change accepted at owning authority boundary
→ new/updated authoritative model state
→ deterministic Phase-J generation
→ new GeneratedSysMLArtifactSet fingerprint
→ Phase-K validation
→ new SysMLValidationResult fingerprint
→ new Final Model Review Revision
```

A prior K result never authorizes changed content.

A prior Final Model Review approval never authorizes changed content.

No "validation carry-over" is permitted across a changed artifact fingerprint.

---

### L-13 — Final Human release approval is mandatory

Final publication requires an explicit immutable Human decision bound to one
exact Final Model Review Revision.

Conceptually:

```python
FinalModelReviewDecision(
    final_model_review_id=...,
    final_model_review_revision_id=...,
    decision="approved_for_publication",
    generated_artifact_set_fingerprint=...,
    validation_result_fingerprint=...,
    reviewer_identity=...,
    rationale=...,
    content_fingerprint=...,
)
```

Final approval shall be possible only if:

- the review revision is internally complete,
- the exact artifact-set integrity is valid,
- the exact validation-result integrity is valid,
- `validation_status == "valid"`,
- `publication_gate == "passed"`,
- the validation result covers the exact artifact-set fingerprint,
- no mandatory review item remains unresolved,
- no accepted change request is waiting for regeneration,
- and the reviewer explicitly approves the revision for publication.

Final release approval is a Human authority action.

It shall not be inferred from:

- absence of findings,
- agent consensus,
- LLM confidence,
- SYSIDE success alone,
- UI navigation state,
- or elapsed time.

---

### L-14 — Final approval becomes stale on any subject change

A Final Model Review Decision is valid only for the exact fingerprints it
authorizes.

If any of the following changes:

- generated unit content,
- artifact-set fingerprint,
- validation-result fingerprint,
- required validation evidence,
- review revision subject,
- or an approval-relevant policy reference,

the previous approval shall not authorize publication of the changed result.

No post-approval content mutation is permitted.

---

### L-15 — Final publication service boundary

Only after successful automated validation and explicit Human release approval
may publication occur.

The Phase-L publication service boundary becomes conceptually:

```python
OutputWriter.publish(
    artifact_set: GeneratedSysMLArtifactSet,
    validation_result: SysMLValidationResult,
    final_review_decision: FinalModelReviewDecision,
) -> PublishedOutputPackage
```

Before writing any final output, the service shall:

1. validate artifact-set integrity,
2. validate validation-result integrity,
3. execute the existing `validate_phase_l_handoff(...)`,
4. validate Final Model Review Decision integrity,
5. confirm exact Project binding,
6. confirm exact review-revision binding,
7. confirm exact artifact-set fingerprint binding,
8. confirm exact validation-result fingerprint binding,
9. require `approved_for_publication`,
10. and reject stale or mismatched Human approval.

Phase L shall never implicitly select:

- latest artifact,
- latest validation result,
- latest review revision,
- or latest Human decision.

All publication inputs are explicit.

---

### L-16 — Final output location

Final Human-approved output shall be published under:

```text
data/output/<project_id>/<output_package_id>/
```

Example:

```text
data/output/
└── 000001/
    └── OUT-000001/
        ├── manifest.json
        ├── generated_model.sysml
        ├── generation_summary.json
        ├── validation_result.json
        ├── validation_report.md
        └── traceability.json
```

If Phase J later produces multiple generated units, their configured relative
paths shall be preserved within the output package.

Only final approved packages belong in this publication namespace.

---

### L-17 — Published output identity and version model

Final outputs use project-local immutable sequential IDs:

```text
OUT-000001
OUT-000002
OUT-000003
```

The OUT sequence represents publication identity and ordering.

It is not semantic versioning.

A later OUT ID does not inherently mean a semantic major/minor/patch change.

Engineering and validation identity remains fingerprint-based.

OUT IDs shall be:

- project-local,
- sequential,
- immutable,
- never reused,
- and stable after publication.

---

### L-18 — Versioned Output Publication Profile

Phase L shall introduce a small versioned publication policy artifact:

```text
context/sysml/turing_sysml_v2_output_profile.json
```

Initial identity:

```text
profile_id:      TURING_SYSML_V2_OUTPUT
profile_version: 1.0.0
```

The Output Profile shall define publication concerns only, including:

- output root,
- required package file roles,
- unit placement rules,
- manifest schema expectations,
- fingerprint policy,
- idempotence policy,
- and derived archive/download policy.

It shall not redefine:

- engineering semantics,
- Candidate Review rules,
- IEM assembly,
- Target Notation,
- Generation Profile mappings,
- validation semantics,
- or Human release authority.

Human approval remains an architectural gate, not a configurable bypass.

---

### L-19 — Authoritative published package contents

The MVP authoritative output package shall contain at least:

```text
manifest.json
<all GeneratedSysMLUnit relative paths>
generation_summary.json
validation_result.json
validation_report.md
traceability.json
```

#### Generated SysML units

Published `.sysml` units shall be byte-for-byte identical to the exact
Human-approved `GeneratedSysMLArtifactSet`.

Phase L shall not:

- reformat,
- rewrite,
- normalize,
- reorder,
- rename,
- repair,
- or regenerate

the SysML content during publication.

#### validation_result.json

This is the machine-readable authoritative Phase-K validation evidence.

#### validation_report.md

This is a deterministic Human-readable projection of the validation result.

It is not independent validation authority.

#### traceability.json

This preserves the generated-output traceability needed to navigate from final
output back through:

```text
generated symbol / location
→ IEM element or relationship
→ Model Candidate
→ Approved Input
→ Human Review Decision
→ accepted exception where applicable
```

#### generation_summary.json

This records deterministic generation context and provenance required to
understand the published artifact, without creating new engineering semantics.

#### manifest.json

This is the authoritative package index and integrity boundary.

---

### L-20 — Published output manifest

Conceptually:

```python
PublishedOutputManifest(
    schema_version=...,
    project_id=...,
    output_package_id=...,
    source_internal_engineering_model_id=...,
    source_artifact_set_fingerprint=...,
    validation_result_fingerprint=...,
    final_model_review_id=...,
    final_model_review_revision_id=...,
    final_review_decision_id=...,
    final_review_decision_fingerprint=...,
    output_profile_reference=...,
    publication_input_fingerprint=...,
    files=(...),
    content_fingerprint=...,
)
```

Every persisted package file shall have:

- relative path,
- controlled file role,
- content fingerprint,
- and source/generated reference where applicable.

The complete output package shall have one deterministic content fingerprint.

---

### L-21 — Publication input fingerprint and idempotence

Phase L shall calculate a deterministic publication-input fingerprint covering
at least:

- exact `GeneratedSysMLArtifactSet.content_fingerprint`,
- exact `SysMLValidationResult.content_fingerprint`,
- exact Final Model Review Decision fingerprint,
- exact Final Model Review Revision fingerprint,
- exact Output Profile reference and fingerprint.

Conceptually:

```text
publication_input_fingerprint =
SHA256(
    artifact-set identity
  + validation identity
  + Human release identity
  + publication policy identity
)
```

Publishing the exact same authorized input repeatedly shall return the same
existing published output package rather than allocate new OUT IDs.

Example:

```text
same exact authorized publication input
→ OUT-000001

same exact authorized publication input again
→ OUT-000001
```

A materially different approved publication input receives a new OUT ID.

---

### L-22 — Atomic immutable publication

Final publication shall be fail-closed and atomic.

Conceptually:

```text
allocate OUT-000001
→ create hidden temporary package
→ write all files
→ verify file fingerprints
→ verify manifest
→ verify complete package integrity
→ atomic rename
→ OUT-000001 becomes visible
```

A visible final output directory shall always represent a complete validated
publication.

Interrupted or incomplete temporary publication state shall never be treated as
a valid published output.

---

### L-23 — Recovery and integrity scanning

The Output Repository shall detect at least:

- incomplete temporary publication directories,
- malformed OUT identities,
- missing manifest,
- missing required files,
- unexpected files where prohibited by profile,
- file fingerprint mismatch,
- manifest fingerprint mismatch,
- unsafe paths,
- symlink/path escape,
- duplicate publication-input fingerprints,
- and project/OUT identity mismatch.

Recovery diagnostics shall be explicit and fail-closed.

Interrupted publication evidence shall not be silently deleted during normal
reads.

---

### L-24 — Directory package is publication authority; archive is derived

The authoritative final output is the immutable directory package plus its
manifest.

A ZIP or other archive may be produced for download convenience.

Such an archive is a derived transport representation.

It shall not replace the directory package as publication authority.

Archive metadata such as timestamps or compression differences shall not alter
the identity of the authoritative publication.

---

### L-25 — Final project-output association occurs only after approval and publication

Final Model Review artifacts are project-local review evidence but shall not be
listed as released project outputs.

Only successfully published:

```text
OUT-xxxxxx
```

packages become final project outputs.

The Project Dashboard / Guided Workflow UI shall distinguish at least:

```text
Generated / under review
Changes requested
Validation blocked
Ready for Human approval
Approved for publication
Published
```

A generated model shall not appear as a final project model merely because J or
K completed successfully.

---

### L-26 — Read boundary for UI and downstream presentation

Publication persistence shall expose explicit read services conceptually like:

```python
OutputRepository.list_outputs(
    project_id,
) -> tuple[PublishedOutputManifest, ...]
```

and:

```python
OutputRepository.load_output(
    project_id,
    output_package_id,
) -> PublishedOutputPackage
```

Final Model Review shall expose separate project-bound review read services.

The later Guided Workflow UI shall consume these read boundaries.

The UI shall not infer publication authority directly from filesystem presence
or session state.

---

### L-27 — SYSIDE unavailability blocks approval/publication, not review

The current verification workstation may not have the SYSIDE CLI available.

This does not prevent:

- Phase-L architecture,
- review-workspace implementation,
- review UI implementation,
- review of generated code,
- review of diagrams,
- review of agent proposals,
- change requests,
- or deterministic unit testing.

It does prevent final Human release approval and final publication of a real
model because the required K state cannot become:

```text
valid / passed
```

without required external validation.

An `incomplete / blocked` result may be reviewed, but shall not be approved for
publication.

---

### L-28 — Human authority is not delegated to LLMs or agents

No LLM or agent is normative final release authority.

Agents may support:

- proposal generation,
- alternative generation,
- completeness review,
- relationship analysis,
- rationale generation,
- and response to Human feedback.

Only an explicit Human decision may authorize final publication.

The final gate is therefore:

```text
automated validation PASS
+
explicit Human release approval
=
eligible for publication
```

not:

```text
agent consensus
=
publication
```

---

### L-29 — Complete revision loop

The normative Phase-L correction loop is:

```text
J GeneratedSysMLArtifactSet
        ↓
K SysMLValidationResult
        ↓
Final Model Review Revision
        ↓
Human inspection:
  diagram
  code
  validation
  traceability
  agent/personality evidence
        ↓
        ├── approve
        │      ↓
        │   Human release decision
        │      ↓
        │   OutputWriter.publish(...)
        │      ↓
        │   OUT-xxxxxx
        │
        └── request changes
               ↓
            explicit change proposal
               ↓
            owning authority boundary
               ↓
        optional bounded LLM/agent proposal
               ↓
            Human review/acceptance
               ↓
          new authoritative model state
               ↓
        deterministic regeneration
               ↓
             validation
               ↓
       new Final Model Review Revision
```

Every loop iteration remains traceable.

---

### L-30 — Non-goals and prohibited shortcuts

Phase L shall not:

- treat generated SysML as final before Human release approval,
- mutate a prior immutable review revision,
- silently change validated generated content,
- publish `invalid` or `incomplete` results,
- publish without exact K fingerprint binding,
- publish without exact Human approval binding,
- use manually edited final SysML as a second engineering authority,
- let an LLM directly rewrite and publish final output,
- bypass Candidate Human Review for semantic changes,
- bypass IEM assembly,
- bypass deterministic Phase-J generation,
- carry old validation across changed content,
- carry old Human approval across changed content,
- infer publication from "latest" artifacts,
- or collapse review evidence and final published output into one storage state.

---

## Final Model Review state model

The initial controlled review lifecycle shall support at least:

```text
generated
validation_blocked
review_pending
changes_requested
regeneration_required
ready_for_approval
approved_for_publication
published
```

These are workflow/read-model states.

Normative authority remains in immutable artifacts and Human decisions rather
than in a mutable status string alone.

A review is `ready_for_approval` only when the exact review revision has:

- complete subject integrity,
- `valid / passed` K evidence,
- no unresolved mandatory review items,
- and no outstanding accepted change request.

---

## Publication package authority chain

A final published package shall be able to prove:

```text
OUT-xxxxxx
→ PublishedOutputManifest
→ exact Final Model Review Decision
→ exact Final Model Review Revision
→ exact SysMLValidationResult
→ exact GeneratedSysMLArtifactSet
→ exact IEM
→ reviewed Model Candidates
→ Approved Input
→ Human Review evidence
```

This chain is required for auditability and for later Project Dashboard /
Guided Workflow presentation.

---

## UI interaction principle

The established interaction principle remains:

```text
Simple by default.
Explainable on demand.
Fully traceable underneath.
```

The default Final Model Review UI should emphasize:

1. what model was generated,
2. whether automated validation passed,
3. what requires Human attention,
4. relevant alternative proposals,
5. the current diagram/model structure,
6. the exact SysML code on demand,
7. and the next Human action.

Detailed IDs, fingerprints, policy references, evidence chains and diagnostics
remain available through progressive disclosure.

The UI shall support direct review interaction without weakening the immutable
authority model underneath.

---

## Implementation decomposition

Phase L shall be implemented in the following controlled slices.

### L1 — Final Model Review domain foundation

- identifiers,
- immutable review/revision/item/decision types,
- validation,
- fingerprints,
- lifecycle vocabulary.

### L2 — Final Model Review repository

- project-local review workspace,
- immutable revisions,
- decision persistence,
- safe paths,
- integrity,
- scanning,
- recovery.

### L3 — Final Model Review read model and UI projection

- deterministic `FinalModelReviewView`,
- diagram/model projection,
- exact SysML code projection,
- validation findings,
- traceability,
- upstream Candidate evidence,
- agent/personality proposal evidence,
- required decisions,
- next action.

### L4 — Change proposal and revision loop

- code-edit proposals,
- diagram/model change proposals,
- structured Human feedback,
- change classification,
- owning-boundary routing,
- optional bounded LLM/agent re-proposal,
- regeneration/revalidation handoff,
- successor review revision creation.

### L5 — Final Human release gate

- exact revision binding,
- exact artifact fingerprint binding,
- exact validation fingerprint binding,
- unresolved-item checks,
- `approved_for_publication`,
- stale approval rejection.

### L6 — Output publication repository and OutputWriter

- Output Profile,
- OUT identity,
- deterministic package projections,
- publication-input fingerprint,
- idempotence,
- atomic publication,
- bundle integrity,
- read service,
- derived download/archive support.

### L7 — End-to-end integration and acceptance

- J→K→Final Review→Publication integration,
- real SYSIDE validation environment,
- real `valid / passed` result,
- Human Review acceptance,
- real `OUT-000001` publication,
- review/change loop evidence,
- focused regression,
- complete repository regression,
- `git diff --check`,
- manual acceptance,
- SSOT closeout.

No implementation slice may weaken the Human Review, fingerprint, project
isolation, deterministic generation, validation or publication boundaries
defined by this ADR.

---

## Consequences

### Positive

- the final generated model receives an explicit Human release gate,
- Human Review can evaluate the actual generated `.sysml` result rather than
  only upstream abstract Candidates,
- validation results become visible review evidence,
- generated code and model diagrams can be reviewed together,
- agent/personality alternatives remain visible and comparable,
- Human feedback can trigger controlled revision loops,
- LLM assistance remains useful without becoming authority,
- direct UI editing remains possible without creating a hidden engineering
  source of truth,
- every revision remains auditable,
- final output is clearly separated from generated-but-unreleased work,
- only fully validated and explicitly approved results enter `data/output/`,
- and the final Project output can be traced through the complete engineering
  workflow.

### Tradeoffs

- Phase L becomes larger than a simple filesystem Output Writer,
- corrections may require returning to earlier phases rather than patching final
  code directly,
- every material revision requires regeneration and validation,
- the UI needs clear distinction between proposed edits and authoritative
  changes,
- and a live external validator remains necessary before real publication.

These costs are accepted because they preserve engineering authority,
traceability and Human control.

---

## Relationship to prior ADRs

This ADR complements and does not replace:

- ADR-016 — Human Review Workspace and Approved Input Promotion Architecture,
- ADR-017 — Simple-by-Default Interaction and Progressive Disclosure,
- ADR-018 — Model Candidate Layer and Structural Comparability,
- ADR-019 — Internal Engineering Model Assembly Architecture,
- ADR-020 — Hybrid Target Projection and Coverage Architecture,
- ADR-021 — SYSIDE-Compatible SysML v2 Generation Architecture,
- ADR-022 — SysML v2 Validation Layer Architecture.

ADR-022 K-21 remains normative for final publication.

This ADR clarifies that the K→L `valid + passed` gate is a **publication gate**,
not a prohibition against reviewing invalid or incomplete generated models.

This ADR also refines the previous workspace rule for generated SysML:

```text
review-stage generated SysML
→ project-local Final Model Review workspace

final Human-approved generated SysML
→ data/output/
```

CATIA remains authoritative for Turing Generator engineering knowledge and is
not replaced by generated project output.

---

## Decision summary

The accepted final prototype path is:

```text
Source
→ Processing Run
→ Human Review
→ Approved Input
→ Model Candidates
→ Candidate Human Review
→ Internal Engineering Model
→ deterministic SysML v2 generation
→ automated validation
→ Final Model Human Review
→ optional controlled revision / LLM-agent loop
→ explicit Human release approval
→ immutable versioned Published Output Package
```

The core release rule is:

```text
Generated
≠ Final

Validated
≠ Final

Human-approved exact validated revision
= eligible for final publication
```

Phase L shall implement this rule without weakening the established deterministic
generation, validation, traceability or Human Review architecture.


\newpage


# ADR-024 — Guided Engineering Workflow and UX Projection Architecture


## Status

Accepted

## Date

2026-08-16

## Context

The Turing Generator has reached a functionally complete prototype baseline through
Phase L.

Baseline implementation reference:

```text
0fce9928a04e047e4b39484b421632fb64ed905f
Complete Phase L final model review and output publication
```

At this baseline, the implemented workflow covers:

```text
Source
→ Processing
→ Human Review
→ Approved Input
→ Model Candidates
→ Candidate Human Review
→ Internal Engineering Model
→ SysML v2 Generation
→ Validation
→ Final Model Human Review
→ Human Release Approval
→ Published Output
```

The technical architecture provides immutable evidence, explicit Human authority,
deterministic read models, validation gates and end-to-end traceability.

However, formative usability evaluation of the functional prototype showed that
the existing user interface remains too strongly oriented around technical
artifacts, workflow metadata and implementation structure.

The functionality is available, but the engineer has to spend unnecessary effort
finding:

- what engineering information was ingested,
- which processing result is relevant,
- where Human action is required,
- where multiple Agent / Persona results agree,
- where they differ,
- which alternatives are available,
- why an Agent proposed a result,
- and what the next engineering decision should be.

The issue is therefore not missing processing capability.

The issue is the mapping between the implemented processing architecture and the
daily engineering task.

This ADR defines the UX architecture for WP-09 through WP-12.

---

## Decision

### UX-01 — Engineer-centered interaction

The primary UI shall be organized around the engineer's work rather than the
internal implementation structure.

The default questions answered by the UI are:

```text
What was provided?
What did the system derive?
Do the independent perspectives agree?
Where do I need to decide?
What happens next?
```

Technical metadata shall remain available but shall not dominate the primary
working surface.

---

### UX-02 — Guided Workflow is a projection, not authority

The Guided Engineering Workflow shall not create another authoritative workflow
state machine.

It is a deterministic projection of existing persisted authoritative state.

Conceptually:

```text
authoritative repositories / read services
                ↓
      GuidedWorkflowReadService
                ↓
        GuidedWorkflowView
                ↓
             Streamlit
```

The UI may maintain transient navigation state.

It shall not duplicate engineering, review, validation, release or publication
authority inside Streamlit session state.

---

### UX-03 — Engineering Content before Metadata

Primary views shall present engineering content first.

Examples include:

- source content,
- extracted engineering statements,
- proposed elements,
- proposed relationships,
- engineering classifications,
- model structure,
- validation findings,
- generated SysML.

Implementation metadata such as:

- internal IDs,
- hashes,
- fingerprints,
- artifact paths,
- processing-run identifiers,
- manifest versions,

shall remain available through progressive disclosure for traceability and audit.

The intended hierarchy is:

```text
Primary layer
→ What is this?

Decision layer
→ What do I need to decide?

Explanation layer
→ Why was this proposed?

Traceability layer
→ Where exactly did it come from?
```

---

### UX-04 — Decision-Centered Interaction

Open Human decisions are first-class UI objects.

The Guided Workflow shall prioritize:

```text
action required
→ relevant engineering content
→ alternatives
→ supporting rationale
→ Human decision
```

The engineer shall not have to inspect technical processing state to discover
that a decision is required.

The project entry view should therefore prioritize work such as:

```text
Your work

4 decisions required
2 results contain relevant variance
11 results are confirmed / ready

Next action
→ Review extracted engineering information
```

The exact counts are derived from authoritative persisted state.

---

### UX-05 — Variance is first-class engineering information

Whenever a processing step contains redundant LLM / Agent / Persona execution,
the UI shall expose the resulting variance explicitly.

Existing consensus concepts shall be reused.

Examples include:

```text
unanimous
majority
single
none
incomparable
incomplete
```

and:

```text
low
medium
high
```

variance.

Persona stability and incomplete Agent evidence shall remain distinguishable from
inter-Persona disagreement.

Repeated executions of one Persona shall not be presented as additional
independent votes.

---

### UX-06 — Side-by-Side before Aggregation

When multiple Agent / Persona results exist for the same engineering subject,
the preferred presentation is side-by-side comparison.

Conceptually:

```text
┌────────────────┬────────────────┬────────────────┐
│ Persona A      │ Persona B      │ Persona C      │
│                │                │                │
│ Proposal A     │ Proposal A     │ Proposal B     │
│ rationale ▾    │ rationale ▾    │ rationale ▾    │
└────────────────┴────────────────┴────────────────┘

2 / 3 → Proposal A
1 / 3 → Proposal B

Human decision required
```

Consensus summaries complement this comparison.

They shall not hide the individual Agent / Persona results.

---

### UX-07 — Visual consensus language

Agreement and variance shall be visually recognizable at a glance.

The semantic presentation shall follow:

```text
GREEN
unanimous / low variance

AMBER
majority / medium variance

RED
high variance / no consensus / blocking Human decision

NEUTRAL
incomplete / unavailable / not yet processed
```

Color alone shall never carry the meaning.

Every visual state shall also contain an explicit textual label, for example:

```text
Unanimous · 3 / 3 Personas agree
Majority · 2 / 3 Personas agree
High variance · Human decision required
```

Green means agreement between independent perspectives.

Green does not mean Human approval.

---

### UX-08 — Recurring interaction pattern

The same interaction pattern shall be reused across processing stages:

```text
INPUT
↓
PROPOSED RESULT(S)
↓
VARIANCE / CONFIDENCE
↓
HUMAN DECISION — where required
↓
ACCEPTED RESULT
```

This applies to:

- ingestion interpretation,
- semantic extraction,
- classification,
- Human Review,
- Model Candidate review,
- architecture / Model Proposal review,
- Final Model Review.

This consistency is intended to reduce cognitive switching between phases.

---

### UX-09 — Guided workflow stages

The engineer-facing workflow shall expose the following conceptual stages:

```text
1  Project & Sources
2  Processing
3  Human Review & Approved Input
4  Model Proposal & Candidate Review
5  Final Model Review
6  Published Output
```

These stages are presentation concepts.

They do not replace existing Phase/domain authority.

---

### UX-10 — Simple by default, explainable on demand

ADR-017 remains the primary presentation principle:

```text
Simple by default.
Explainable on demand.
Fully traceable underneath.
```

Default views shall show only information necessary for the current engineering
task.

Rationale, evidence, technical metadata and complete traceability remain reachable
without changing authority or losing auditability.

---

### UX-11 — No implicit latest authority

The UI may identify useful display defaults.

It shall not silently select a latest artifact as authority for a write action.

Candidate decisions, review decisions, change proposals, release decisions and
publication actions must remain bound to explicit immutable artifact identities.

Display convenience shall never weaken authority binding.

---

### UX-12 — Existing services remain normative

The Guided Workflow shall delegate write actions to the already established
domain services.

Examples include:

```text
ReviewApprovalWorkflowService
ModelCandidateReviewRepository
FinalModelReviewChangeService
FinalModelReviewReleaseService
OutputWriter
```

The UI shall not reproduce their validation or authority rules.

---

### UX-13 — Dual-layer presentation

Existing functional views shall not be discarded merely because their current
presentation is technically dense.

Where useful, engineer-facing workflow surfaces shall support two presentation
depths over the same authoritative state:

```text
Focused View
→ engineering content
→ Agent / Persona alternatives
→ consensus and variance
→ open Human decisions
→ accepted / approved engineering results
→ next engineering action

Technical View
→ processing identities
→ run / attempt state
→ artifact references
→ fingerprints
→ provenance
→ traceability
→ diagnostic and lifecycle information
```

The default presentation shall be the Focused View.

The engineer may explicitly enable technical details when deeper analysis,
diagnosis or auditability is required.

Conceptually:

```text
same authoritative state
          │
          ├── Focused presentation
          │     └── task-oriented engineering information
          │
          └── Technical presentation
                └── deeper processing and traceability information
```

The presentation-depth setting is UI state only.

It may be stored transiently in Streamlit session state because switching the
presentation depth does not modify:

- engineering content,
- Agent evidence,
- Human decisions,
- Candidate authority,
- validation state,
- Final Model Review state,
- publication eligibility,
- or published output.

The following distinction is normative:

```text
Focused ≠ reduced evidence
Focused = reduced visible complexity

Technical ≠ separate workflow
Technical = deeper presentation of the same workflow
```

Views that already provide useful functionality, including Project Dashboard,
Processing and Human Review, shall therefore be adapted progressively to this
dual-layer interaction model rather than replaced unnecessarily.

The Project Dashboard retains the distinct engineering purpose:

```text
Which engineering / model information is already available?
```

The Guided Engineering Workspace retains the distinct purpose:

```text
What requires my attention or decision now?
```

Individual LLM-backed processing steps may remain separate working surfaces when
this improves clarity. They shall reuse the same content-first, variance-aware and
Human-decision-centered interaction language.

---

### UX-14 — Global application context

Project selection and presentation depth are application-level context.

They shall therefore remain directly accessible independently of the currently
selected working surface.

The common application shell shall expose:

```text
Project
→ explicit currently selected Project

Technical details
→ Focused / Technical presentation depth
```

Conceptually:

```text
APPLICATION CONTEXT
Project: [ selected Project ]
Technical details: [ on / off ]

        ↓ shared by all views

Engineering Workspace
Project Dashboard
Processing
Human Review
Model Proposal
Final Model Review
Published Output
```

The engineer shall not have to navigate to the Project Dashboard merely to change
the current Project.

Likewise, the engineer shall not have to return to the Engineering Workspace to
change presentation depth.

Project selection changes UI working context.

It shall not modify persisted engineering state.

Changing the selected Project shall clear stale entity-level UI navigation from
the previously selected Project.

No Project shall be silently selected merely because it is the newest or only
recently used Project when no valid explicit selection exists.

Project creation remains a Project Workspace management function but shall be
directly accessible from the global application context.

The Project selector and the Project creation action shall therefore be colocated
in the common application shell:

```text
Project
[ Selected Project ▼ ]
+ Create new project
```

Project creation is an explicit action and shall not be represented as a
pseudo-Project inside the Project selection list.

After successful creation, the newly created Project becomes the explicitly
selected Project context.

The Project Dashboard shall not duplicate the creation control when it is rendered
inside the common application shell.

The presentation-depth control shall use a persistent application-level toggle
and shall affect all engineer-facing views progressively as WP-09 through WP-11
adopt the dual-layer presentation architecture.

---

### UX-15 — Authority-preserving write interaction

The Guided Engineering Workflow may initiate authoritative actions.

It shall never become engineering or workflow authority itself.

Every write initiated from an engineer-facing UI surface shall follow:

```text
explicit immutable target
+
explicit Human action
+
existing domain authority service
+
immutable authoritative persistence
+
read-side reconstruction after the write
```

Conceptually:

```text
Streamlit interaction
        ↓
GuidedWorkflowWriteService
        ↓
existing normative domain service
        ↓
authoritative immutable persistence
        ↓
existing read service / repository
        ↓
reconstructed UI state
```

`GuidedWorkflowWriteService` is an application-level delegation boundary only.

It shall not:

- reproduce Candidate Review rules,
- reproduce Final Model Review routing rules,
- reproduce release-gate logic,
- infer engineering approval,
- infer a latest authoritative target,
- mutate generated SysML directly,
- or store authoritative decisions in Streamlit session state.

The following domain services remain normative:

```text
ModelCandidateReviewRepository
FinalModelReviewChangeService
FinalModelReviewReleaseService
OutputWriter
```

Candidate Review writes shall require an explicit:

```text
Project
Candidate Set
Candidate type
Candidate identity
Human decision
Reviewer identity
```

Final Model Review change requests shall require an explicit:

```text
Project
Final Model Review
Final Model Review revision
affected review surface
change classification
Human feedback
reviewer identity
```

Final release approval shall require an explicit:

```text
Project
Final Model Review
Final Model Review revision
reviewer identity
```

The UI may simplify labels and interaction controls.

It shall not simplify or weaken the underlying authority contract.

After a successful write, the UI shall discard transient assumptions and
reconstruct the visible state from persisted authoritative evidence.

---

### UX-16 — Processing and Human Review projection

WP-10 shall adapt Processing and Human Review to the same deterministic
dual-layer presentation architecture established by this ADR.

The default Processing projection shall prioritize:

```text
Source filename / readable Source identity
→ current Processing status
→ relevant result / attention state
→ next engineering action
```

The default Processing surface shall not require the engineer to interpret
Source IDs, Processing Run IDs, Attempt IDs, configuration fingerprints,
artifact fingerprints or SHA-256 values to perform normal work. Those facts
remain available in Technical View.

The default Human Review projection shall prioritize:

```text
engineering statement
→ independent Persona proposals
→ consensus / material variance
→ required Human decision
→ accepted / approved result
```

All Persona proposals remain individually inspectable. Repeated executions
associated with one Persona remain grouped beneath that Persona and shall not be
displayed as additional independent votes.

Consensus and variance shall be projected only from existing persisted consensus
evidence. The presentation layer shall not infer semantic agreement when
authoritative consensus evidence is absent. Relationship Review Items for which
the existing Consensus Analyzer provides no explicit relationship consensus
shall therefore present consensus as unavailable rather than inventing an
agreement state.

The following distinction is normative:

```text
Focused Processing
→ filename / Source role
→ Processing status
→ result or required attention
→ next action

Focused Human Review
→ engineering content
→ Persona alternatives
→ consensus / variance
→ Human decision
→ review / approval lifecycle state

Technical View
→ Source / Run / Attempt identities
→ Review Document / Version / Revision identities
→ artifact references
→ fingerprints
→ evidence locators
→ provenance and lifecycle diagnostics
```

Processing and Human Review projection adapters are read-side presentation
components only. They shall not own Source Registry authority, Processing Run
state, Review Revision state, Human Review Decision authority, Approved Input
authority, consensus authority or write validation.

Existing services remain normative, including:

```text
ProjectBoundIngestionService
ReviewApprovalWorkflowService
```

Human write actions continue to operate on exact immutable targets and shall be
followed by read-side reconstruction.

---

### UX-17 — Architecture / Model Proposal projection

WP-11 shall adapt Model Proposal and Candidate Review to the same deterministic,
dual-layer presentation architecture established by this ADR.

The default Model Proposal projection shall prioritize:

```text
proposed architecture / model structure
→ material alternatives and structural variance
→ required Human Candidate decisions
→ Candidate Review state
→ Phase-I readiness
→ next engineering action
```

The engineer shall not have to reconstruct the proposed architecture mentally
from Candidate IDs, repository state or independent technical tables.

The Focused Model Proposal shall therefore expose an engineer-readable structural
projection of the exact selected Candidate Set, including:

```text
model elements
→ readable name
→ model area / type
→ support state
→ Human review state

model relationships
→ source
→ semantic intent
→ target
→ resolution / priority state
→ Human review state

relationship choice groups
→ all alternatives
→ preferred alternatives where persisted
→ accepted alternatives where persisted
→ whether Human review remains required

structural / profile deviations
→ affected Candidate
→ conformance / comparability state
→ whether Human review remains required
```

Relationship Choice Groups are alternatives, not independent Agent votes.
The presentation layer shall not translate Candidate alternatives into Persona
consensus or invent agreement evidence.

Phase-I readiness shall be projected only from the existing Model Proposal /
Phase-I gate state. The UI shall not infer that a Candidate Set is ready merely
because all currently visible controls appear resolved.

The following distinction is normative:

```text
Focused Model Proposal
→ architecture / proposed model content
→ alternatives and material deviations
→ Human decisions
→ review progress
→ Phase-I readiness
→ next action

Technical View
→ Candidate Set identity / fingerprint
→ Candidate identities
→ Approved Input references
→ structure-profile conformance
→ comparison anchors / deviation identities
→ decision identities and exact traceability
```

Model Proposal presentation adapters are read-side components only. They shall
not own Candidate authority, Approved Input authority, Candidate Review authority
or Internal Engineering Model assembly authority.

Existing services remain normative, including:

```text
ModelProposalReadService
ModelCandidateReviewRepository
ModelCandidateReadService / Phase-I gate
```

Every Candidate Review write remains bound to the explicit immutable Candidate
Set and Candidate identity currently shown. After a write, the visible proposal
shall be reconstructed from persisted authoritative state.

No graph or diagram rendering technology is made normative by this decision.
WP-11 may use the existing structural overview to improve architecture
readability without introducing a second model representation or model authority.

---

## Formative usability evaluation

The UX redesign follows a formative evaluation of the functionally complete
prototype.

The evaluation is treated as formative engineering feedback, not as a controlled
quantitative usability study unless separate evidence supports such a claim.

Observed findings and resulting design responses are maintained in:

```text
collaboration/ux/usability_findings.md
```

The implemented baseline and redesigned UI are intentionally retained as separate
development stages for later thesis documentation.

---

## Thesis evidence

The UX iteration shall be documented so that the development process can later be
reconstructed as:

```text
technical feasibility
→ functionally complete prototype
→ formative usability evaluation
→ identified usability findings
→ UX requirements
→ wireframes
→ redesigned Guided Engineering Workflow
→ final demonstrator
```

Stable wireframe identifiers shall be maintained under:

```text
collaboration/ux/wireframes/
```

The intended initial set is:

```text
WF-01  Engineer Home / Your Work
WF-02  Ingested Engineering Content
WF-03  Unanimous Persona Results
WF-04  Variant Persona Results
WF-05  Human Decision
WF-06  Model Proposal Review
WF-07  Final Model Review
```

Final thesis figures may be derived from these documented wireframes and from
before/after screenshots of the actual prototype.

---

## Work-package mapping

### WP-09 — Guided Workflow UI

Establish:

- Guided Engineering Workflow projection,
- shared navigation,
- shared Decision presentation,
- shared Variance presentation,
- progressive disclosure,
- reusable side-by-side result comparison.

### WP-10 — Ingestion + Human Review UX Simplification

Apply the UX architecture to:

- Source presentation,
- processing results,
- redundant Agent / Persona outputs,
- Human Review decisions.

### WP-11 — Architecture / Model Proposal UX

Apply the same interaction model to:

- Model Candidate proposals,
- structural comparison,
- architecture/model representation,
- candidate Human decisions.

### WP-12 — End-to-End Demo Hardening

Integrate and harden the complete engineer-facing workflow without weakening any
existing authority, validation or traceability contract.

---

## Consequences

Positive:

- the UI is organized around daily engineering work,
- Human decisions become immediately visible,
- Agent variance becomes useful information instead of hidden processing detail,
- redundant Agent results can be compared quickly,
- existing functional views can be retained and progressively improved,
- focused and technical work can use the same authoritative backend,
- technical traceability remains fully available,
- the same interaction language is reused across phases,
- UX evolution is reproducibly documented for the thesis.

Trade-offs:

- presentation read models become more important,
- some technical metadata moves behind additional interaction,
- visual consensus representations require careful accessibility semantics,
- responsive side-by-side layouts require deliberate UI design.

These trade-offs are accepted.

---

## Prohibited shortcuts

The following are explicitly prohibited:

- replacing engineering authority with UI state,
- treating consensus as Human approval,
- hiding dissenting Agent / Persona results behind an aggregate score,
- counting repeated runs of one Persona as independent votes,
- presenting color without textual meaning,
- direct mutation of generated SysML from the UI,
- implicit latest-artifact authority,
- bypassing Human Review,
- bypassing Validation,
- bypassing Final Human release approval,
- removing traceability merely to simplify the visible UI,
- maintaining separate engineering authority for Focused and Technical views,
- changing engineering state merely by switching presentation depth.

---

## Final principle

The UI shall minimize the engineer's effort to understand and decide.

It shall not minimize the engineering evidence available to justify that decision.


\newpage


# ADR-025 — Semantic Proposal Consolidation and Persona-Aware Consensus


## Status

Accepted

## Date

2026-08-17

## Context

The WP-12 controlled multi-document dry run exposed a structural mismatch between
the ingestion proposal layer and the Human Review experience.

The current review projection can materialize several independent Review Items for
Agent proposals that refer to the same engineering concept but use different
wording or a different proposed element classification. This creates excessive
Human decisions and can hide the most useful form of variance: different Persona
interpretations of one semantic subject.

The same problem exists for relationships. Syntactically different predicates can
express the same engineering relation, while current identity and consensus paths
do not provide a stable semantic relationship subject.

This is not a reason to make Agent output contract-perfect before Human Review.
Reviewable semantic uncertainty remains legitimate processing output. The Human
Review boundary exists to resolve modeling ambiguity, classification variance,
relationship ambiguity, and other engineering uncertainty. Hard authority and
integrity failures remain fail-closed.

The architecture therefore needs a derived, immutable layer that answers one
limited question before Human Review:

> Which exact existing Agent proposals appear to refer to the same engineering
> subject?

That layer must never invent a new engineering statement, silently discard source
evidence, convert agreement into Human approval, or make uncertain equivalence
authoritative.

## Decision

Introduce a **Semantic Proposal Consolidation** layer between structured derivation
proposals and the Persona-aware consensus / Human Review projection.

The target flow is:

```text
Exact immutable Agent Proposals
+ exact Source Evidence
        ↓
Semantic Consolidation
        │
        ├─ Elements
        │   group by engineering meaning,
        │   not by exact name or element_type
        │
        ├─ Relationships
        │   group by consolidated endpoint subjects
        │   and semantic predicate meaning
        │
        └─ comparison outcomes
            equivalent | distinct | uncertain
        ↓
Immutable Semantic Consolidation Artifact
        ↓
Persona-aware Consensus Projection
        ↓
one Engineering Review subject per semantic subject
        ↓
Human Review
```

Semantic consolidation executes during Processing, after structured derivation
proposals exist and before the Processing Run reaches `awaiting_review`.
Opening Human Review reconstructs persisted results and does not initiate a new
semantic-model call.

## Authority boundary

The Semantic Consolidation Artifact is **derived processing evidence**. It is not
an Approved Input, Model Candidate, Internal Engineering Model, or Human approval.

The consolidator may only classify relations among already-existing exact
proposals. It must not:

- create a new engineering claim;
- overwrite an Agent proposal;
- mutate source evidence;
- silently select an element classification;
- silently resolve an uncertain relationship endpoint;
- turn Persona agreement into Human approval;
- infer that two proposals are equivalent when the comparison result is
  `uncertain` or unavailable.

Every semantic subject remains traceable to the exact immutable proposal,
Agent/Persona/run, upstream artifact fingerprint, and source-evidence references
from which it was derived.

## Merge authority

A semantic merge is permitted only when explicit consolidation evidence classifies
the relevant proposals as `equivalent`.

The contract is fail-closed **against merge authority**:

```text
equivalent
→ may support one semantic subject

distinct
→ must remain separate

uncertain
→ must remain separate and remain visible for Human reasoning

missing / malformed comparison
→ cannot authorize a merge
```

A multi-proposal semantic subject must therefore be connected by explicit
`equivalent` comparison evidence. A `distinct` or `uncertain` comparison inside
one semantic subject is an integrity failure. An `equivalent` comparison across
two different subjects is also an integrity failure.

This rule allows safe degradation toward singleton semantic subjects without
inventing equivalence.

## Element identity

`element_type` is explicitly **not** part of semantic-subject identity.

Different Personas may recognize the same engineering concept while proposing
different modeling classifications. Such disagreement must become visible
classification variance on one Review subject.

Example:

```text
"separate client application"

Persona A → interface
Persona B → system
Persona C → system

Recognition:
3 / 3 Personas

Classification:
2 × system
1 × interface

→ one semantic subject
→ visible variance
→ explicit Human decision
```

## Relationship identity

Relationship consolidation is based on:

1. already consolidated semantic source subject;
2. already consolidated semantic target subject; and
3. semantic meaning of the relationship predicate.

Surface wording alone is not relationship identity. For example, `retain`,
`retains`, and `should retain` may represent one semantic relationship when
comparison evidence supports equivalence.

Relationship consolidation must not silently repair unresolved endpoints.
Reviewable endpoint uncertainty remains eligible for Human Review under the
existing Human-in-the-Loop boundary.

## Persona-aware consensus

Consensus is counted by **Persona perspective**, not by raw Agent run.

Multiple runs of one Persona do not create multiple votes:

```text
Persona A / Run 1 ─┐
Persona A / Run 2 ─┴→ one Persona perspective

Persona B / Run 1 ─┐
Persona B / Run 2 ─┴→ one Persona perspective
```

Divergence between repeated runs of one Persona is retained as
**intra-Persona instability**. It is evidence, not additional voting weight.

Full Persona agreement remains a processing observation only and never constitutes
Human approval.

## Semantic comparator

A later implementation slice may use a semantic model for comparisons. The
comparison context must remain bounded to the minimum information needed for
semantic equivalence assessment, for example:

- exact proposal reference;
- candidate / relationship wording;
- proposed classification where relevant;
- concise proposal description;
- exact source-evidence reference / statement.

The entire legacy document, unrelated Agent rationale, or unrelated project
context must not be resent by default.

Deterministic pre-filtering may reduce the number of semantic comparisons, but it
must not authorize a semantic merge unless the resulting consolidation contract
contains explicit valid equivalence evidence.

## C1 — Immutable consolidation contract

The first implementation slice introduces only the artifact contract and its
architecture tests. C1 performs no LLM call and no production clustering.

The artifact binds:

- exact Project ID;
- exact Processing Run ID;
- immutable upstream artifact references and fingerprints;
- exact proposal references;
- proposal kind;
- Agent ID;
- Persona ID;
- run index;
- exact source-evidence references;
- semantic subjects and their exact member proposal references;
- explicit pairwise comparison outcomes and comparison provenance;
- an input-set fingerprint; and
- an artifact fingerprint.

The C1 contract requires:

- deterministic ordering;
- unique upstream artifact references;
- unique proposal references;
- every proposal to belong to exactly one semantic subject;
- no proposal to occur in more than one semantic subject;
- proposal kind to match semantic-subject kind;
- unique unordered comparison pairs;
- no self-comparison;
- comparison references to resolve to known proposals of the same kind;
- multi-member subjects to have a connected graph of explicit `equivalent`
  comparisons;
- no `distinct` or `uncertain` comparison within one subject;
- no `equivalent` comparison across different subjects;
- exact fingerprint validation on deserialization.

Repeated proposals from the same Persona with different run indexes are valid
input. C1 deliberately does not count them as independent votes.

## Implementation slices

```text
C1  SemanticConsolidationArtifact + fail-closed contracts
C2  Element semantic clustering + Persona-aware consensus
C3  Relationship semantic clustering + consensus
C4  Review projection/cards + empirical before/after test
```

C2–C4 must be reviewed against the persisted C1 contract and existing Human
Review authority before integration.

## Acceptance criteria

The completed architecture is accepted when:

- semantically equivalent wordings become one Review subject;
- different element classifications of the same concept remain one subject with
  visible variance;
- repeated runs of one Persona do not add votes;
- truly distinct engineering concepts remain separate;
- uncertain equivalence is never silently merged;
- relationships receive semantic consolidation and Persona-aware consensus;
- every original proposal and its source evidence remain inspectable;
- the Human can split an incorrect semantic cluster before approval; and
- full semantic agreement never implies Human approval.

No fixed target number of Review decisions is an acceptance criterion. The
solution must not be optimized to the current WP-12 synthetic dataset.

## WP-12 evaluation use

The existing WP-12 retest state with 68 Human decisions is retained as immutable
before-evidence. After C1–C4 are accepted, a new isolated Processing Run shall use
the same source / Persona / run configuration for an empirical before/after
comparison.

No additional live LLM test is required for C1.

## Consequences

### Positive

- Human Review operates on engineering subjects rather than duplicated strings.
- Classification variance becomes visible instead of fragmenting identity.
- Persona consensus becomes independent of repeated-run count.
- Relationship consensus gains a defined semantic basis.
- Semantic grouping remains traceable, reversible, and subordinate to Human
  authority.
- The architecture can degrade safely toward non-merge instead of inventing
  equivalence.

### Negative / cost

- Processing gains an additional derived artifact and later a semantic comparison
  step.
- Semantic comparison can add latency and token cost when a model is used.
- The UI must later expose grouping evidence and support a Human split action.
- Stability of semantic grouping becomes an additional property that requires
  testing.

## Rejected alternatives

### Exact normalized name + element type as identity

Rejected because it fragments one engineering concept when Personas disagree on
classification and therefore hides useful variance.

### Exact normalized name only

Rejected because wording variation still creates duplicates and homonyms may be
incorrectly merged.

### Fuzzy string matching as merge authority

Rejected because lexical similarity is not sufficient engineering evidence for
semantic identity and may silently merge distinct concepts.

### Make the Agent output contract-perfect before Human Review

Rejected because semantic uncertainty is legitimate Human-in-the-Loop input.
Only hard authority and integrity corruption should block review construction.

### Run semantic consolidation when Human Review is opened

Rejected because review opening would incur new model cost, could produce
different grouping on repeated opens, and would make Human Review availability
depend on a live external call.


\newpage


# ADR-026 — Source-Anchored Multi-Persona Interpretation and Cross-Unit Semantic Synthesis


## Status

Accepted

## Date

2026-08-18

## Context

WP12-E2E-DRY-001 exposed a structural limitation in the current Phase-F
multi-agent interpretation and Human Review path.

The original BLK-003 finding showed that semantically equivalent Agent proposals
could become separate Human Review subjects because identity and grouping were
primarily derived after independent broad-context Agent execution.

ADR-025 introduced semantic proposal consolidation, persona-aware consensus,
relationship consolidation, and semantic Human Review projection.

The subsequent BLK-003.1 corrections improved processing robustness:

- exact source-evidence statements no longer collide solely because they share
  one Agent-local source-information identifier,
- unresolved or ambiguous relationship endpoints are preserved for Human Review
  instead of terminating Processing,
- non-recoverable semantic processing errors are classified more precisely at
  the Project Ingestion service boundary.

These corrections resolved the observed hard Processing failure, but the
controlled POST-BLK-003.1 empirical retest exposed a deeper architectural
problem.

### WP12 empirical evidence

Controlled retest:

- Project: `887027`
- Processing Run: `RUN-000001`
- Source: `01_product_overview.md`
- Personas: 3
- Runs per Persona: 2
- Processing result: `awaiting_review`

Observed semantic consolidation result:

- 66 raw Element proposals,
- 66 Element semantic subjects,
- Element consolidation degraded to singletons,
- warning: `semantic_comparator_unavailable`,
- 46 raw Relationship proposals,
- 46 Relationship semantic subjects,
- Relationship consolidation degraded to singletons,
- warning: `relationship_semantic_comparator_invalid`,
- 112 resulting Human Review items.

The LLM calls themselves completed, but their global consolidation outputs were
not safely consumable:

- the Element comparator response was syntactically invalid and could not be
  parsed under the strict contract,
- the Relationship comparator response violated the required complete,
  non-overlapping proposal partition,
- safe fallback behavior therefore retained every proposal as an independent
  singleton subject.

The fallback preserved evidence and authority boundaries, but the resulting
Human Review workload was unacceptable.

### Architectural finding

The live Phase-F workflow currently gives a team-wide stage input to all Agent
personas. Each persona independently derives candidates from broad source
context, and semantic identity is established only afterwards.

Source references therefore provide provenance, but they do not necessarily
provide an explicit orchestration contract that says:

> These Persona outputs are independent interpretations of the same canonical
> source analysis unit.

This turns semantic consolidation into a large global clustering and partitioning
problem.

For the controlled retest, 66 Element proposals and 46 Relationship proposals
had to be reconciled after broad-context derivation. A failure of Element
consolidation also reduces the ability of downstream Relationship consolidation
to identify common endpoints.

### Existing reusable architecture

The repository already contains several compatible upstream concepts.

`SourceProjection` provides deterministic source content, ordered persisted
segments, source locators, segment identifiers, and exact offsets.

`InformationUnitSourceAnchor` provides exact source ranges within persisted
Source Projection segments.

`semantic_extraction` provides persona/run-bound candidate representations with
exact source anchors and excerpts.

`semantic_consensus` already models:

- required Personas,
- repeated Persona runs,
- intra-Persona stability,
- distinct-Persona voting,
- exact source-evidence grouping,
- field variance,
- Human Review requirements,
- and proposed Information Unit drafts.

These concepts shall be reused where compatible.

The existing `InformationUnit` concept shall not be repurposed as the
pre-interpretation orchestration anchor. An Information Unit is already an
interpreted, independently reviewable semantic claim with classification,
epistemic state, extraction provenance, confidence, and content fingerprint.

A separate source-level orchestration identity is therefore required.

---

## Decision

### 1. Introduce canonical Source Analysis Units

Phase F shall introduce an immutable source-level orchestration artifact:

`SourceAnalysisUnit`

Proposed identifier form:

`SAU-000001`

A Source Analysis Unit answers only:

> Which exact portion of the original projected source are the Personas being
> asked to interpret together?

It is not an engineering interpretation and does not itself assert a model
element, requirement, function, relationship, or other semantic meaning.

Each Source Analysis Unit shall bind at least:

- project identity,
- source identity,
- source projection identity,
- source analysis unit identity,
- exact Source Projection segment/range anchors,
- exact source excerpt,
- deterministic source-order information,
- segmentation-profile/version reference,
- immutable content fingerprint.

Source Analysis Units shall remain persona-independent.

No Agent may create or redefine the identity of the Source Analysis Unit it is
asked to assess.

---

### 2. Initial Source Analysis Unit segmentation

For the first implementation, the deterministic persisted Source Projection
segments are the default Source Analysis Unit boundaries.

For text and Markdown sources, the existing Source Projection adapter currently
creates ordered non-empty blank-line blocks.

This default is intentionally conservative:

- it already has deterministic identity and source locations,
- it avoids introducing another LLM-controlled segmentation authority,
- it keeps all Personas on the same bounded source context,
- and it is sufficient for the WP12 controlled source fixtures.

A later versioned segmentation profile may deterministically subdivide Source
Projection segments where evidence shows that smaller analysis units materially
improve comparison quality.

Such subdivision must preserve exact source anchors and must not depend on the
interpretation of an individual Persona.

---

### 3. Source-anchored Persona execution

All configured Personas for a semantic/derivation team shall assess the same
Source Analysis Unit.

Conceptually:

```text
SourceAnalysisUnit SAU-000003
        |
        +-- Persona A / Run 1
        +-- Persona A / Run 2
        +-- Persona B / Run 1
        +-- Persona B / Run 2
        +-- Persona C / Run 1
        +-- Persona C / Run 2
```

Every produced proposal shall retain:

- `source_analysis_unit_id`,
- exact Agent identity,
- Persona identity,
- Persona run index,
- exact source evidence,
- exact Agent wording,
- confidence and rationale,
- and existing immutable provenance.

The Source Analysis Unit identity is the comparison scope, not the semantic
identity of the proposed engineering subject.

One Source Analysis Unit may legitimately produce zero, one, or many proposed
engineering subjects.

---

### 4. Local source-anchored semantic consolidation

Semantic consolidation shall first operate inside one Source Analysis Unit.

The local comparator receives only proposals produced for that Source Analysis
Unit.

Its task is to identify which proposals represent the same local engineering
subject and which remain distinct or uncertain.

Conceptually:

```text
SAU-000003
    |
    +-- Persona A: "remote expert"
    +-- Persona B: "external expert"
    +-- Persona C: "remote specialist"
    |
    +--> Local Subject: Remote Expert
```

The local result shall preserve:

- every exact Agent proposal,
- Persona/run provenance,
- recognition consensus,
- classification variance,
- intra-Persona instability,
- evidence,
- and unresolved semantic differences.

Semantic uncertainty shall remain reviewable.

It shall not be converted into a technical Processing failure merely because the
system cannot safely merge proposals.

---

### 5. Persona voting and repeated runs

A Persona contributes at most one recognition vote to one local engineering
subject.

Repeated runs of the same Persona measure stability and shall not create
additional independent votes.

The existing semantic-consensus principles for distinct-Persona voting and
intra-Persona stability shall be reused where compatible.

---

### 6. Cross-Unit Semantic Synthesis

After all Source Analysis Units of the applicable source scope have been
processed, the locally consolidated engineering subjects shall be synthesized
across Source Analysis Units.

The purpose is to establish project/source-level semantic continuity.

Conceptually:

```text
SAU-000003 / "remote expert"
SAU-000007 / "expert"
SAU-000011 / "remote specialist"
              |
              +--> Engineering Subject: Remote Expert
```

Cross-unit synthesis operates on locally consolidated subjects rather than on
the complete pool of raw Agent proposals.

This substantially reduces comparison scope while preserving all contributing
source and Agent provenance.

Cross-unit synthesis shall be conservative:

- explicit equivalence may consolidate subjects,
- explicit distinction keeps subjects separate,
- uncertainty does not authorize automatic merging,
- unresolved cases remain available to Human Review.

---

### 7. Relationship synthesis and endpoint rebinding

Relationships are first derived with exact source-local proposal provenance.

After cross-unit Element synthesis, Relationship endpoints shall be rebound to
the resulting synthesized Element subjects where exactly resolvable.

Unresolved or ambiguous endpoint bindings remain explicit Human Review findings.

Relationship synthesis shall not invent an endpoint or relationship merely to
complete a graph.

A degraded or uncertain Relationship interpretation shall remain reviewable
provided source evidence and provenance remain trustworthy.

Actual provenance, identity, or artifact-integrity corruption remains a hard
Processing failure.

---

### 8. Compiled Human Review

Human Review shall operate on compiled Engineering Subjects rather than raw
Agent proposals.

The primary review subject shall expose, as applicable:

- synthesized Engineering Subject,
- all contributing Source Analysis Units,
- exact source excerpts,
- all contributing Persona interpretations,
- Persona recognition consensus,
- classification variance,
- intra-Persona instability,
- relationship findings,
- open questions,
- and exact immutable provenance.

The reviewer shall not be forced to accept or reject each independent Agent
proposal when multiple proposals are merely alternative interpretations of the
same engineering subject.

Human Review remains the engineering authorization boundary.

---

### 9. Separation of identities

The following identities represent different concepts and shall not be
collapsed:

`SourceAnalysisUnit`
- Which exact source context is being jointly assessed?

`EvidenceReference`
- Which exact source statement or evidence occurrence supports a proposal?

`AgentProposal`
- What did one exact Persona run propose?

`LocalSemanticSubject`
- Which engineering subject is recognized within one Source Analysis Unit?

`SynthesizedEngineeringSubject`
- Which engineering subject persists across Source Analysis Units?

`InformationUnit`
- Which reviewed semantic claim is eligible for downstream engineering use?

Review workspace identity remains separate from synthesized engineering-subject
identity.

`SES-......` and `SRS-......` remain D4 synthesized-domain identifiers and
shall be preserved in Cross-Unit Synthesis artifacts and exact evidence
locators.

The existing Review Workspace and Approved Input `stable_subject_key` contract
remains lower-case and namespaced. D5 therefore maps synthesized identities
deterministically and one-to-one:

```text
SES-000001 -> semantic:element:ses-000001
SRS-000001 -> semantic:relationship:srs-000001
```

This mapping does not create a new semantic subject and does not replace the
D4 synthesized identifier. It is the downstream Review/Approved-Input identity
representation of the same synthesized authority subject.

This separation is required for traceability, comparability, and safe
Human-in-the-Loop processing.

---

### 10. Comparator failure behavior

Comparator execution remains advisory semantic processing.

A malformed, unavailable, incomplete, or contract-invalid comparator response
shall never authorize an unsafe merge.

Fallback behavior must preserve proposals and evidence.

However, a comparator fallback that would create an unacceptably large Human
Review workload shall be surfaced as a processing-quality finding rather than
silently treated as successful semantic consolidation.

The workflow may still reach Human Review when integrity is preserved, but the
result shall remain visibly degraded.

---

### 11. Implementation slices

Implementation shall proceed in controlled slices.

#### D1 — Source Analysis Unit contract

Introduce:

- immutable Source Analysis Unit type,
- deterministic identifiers,
- source-anchor validation,
- Source Projection binding,
- deterministic fingerprint,
- read/persistence contract,
- focused tests.

No Agent orchestration changes occur in D1.

#### D2 — Unit-bound Persona execution

Change Phase-F orchestration so all configured Persona runs process one explicit
Source Analysis Unit at a time.

Persist the Source Analysis Unit binding in every relevant Agent result.

#### D3 — Source-anchored local semantic consolidation

Consolidate Element and Relationship proposals inside one Source Analysis Unit.

Reuse existing semantic-extraction and semantic-consensus contracts where
compatible.

#### D4 — Cross-unit synthesis and relationship rebinding

Synthesize local subjects across Source Analysis Units and rebind Relationship
endpoints conservatively.

#### D5 — Human Review integration and empirical retest

Project synthesized Engineering Subjects into the existing Human Review
workflow.

Repeat the controlled WP12 Source-01 empirical test and compare:

- raw Agent proposals,
- local subjects,
- cross-unit synthesized subjects,
- Human Review items,
- Persona comparison coverage,
- retained evidence/provenance.

---

## Consequences

### Positive consequences

- Personas are explicitly aligned on the same source context.
- Source provenance becomes an orchestration input rather than only a
  post-hoc traceability attribute.
- Semantic comparison becomes bounded and local before project-wide synthesis.
- Global LLM partition problems are materially reduced.
- Persona consensus becomes easier to interpret.
- Repeated Persona runs remain stability evidence rather than extra votes.
- Human Review operates on engineering subjects rather than raw Agent outputs.
- Exact Agent wording and source evidence remain fully traceable.
- Existing Source Projection, semantic extraction, semantic consensus, and
  Human Review concepts can be reused.
- Comparator degradation no longer automatically explodes the primary review
  model without an explicit quality signal.

### Trade-offs

- Phase-F orchestration becomes more granular.
- More execution records are created because Agent runs are bound to Source
  Analysis Units.
- Cross-unit synthesis introduces an additional explicit semantic layer.
- Runtime and LLM call count may increase if every unit is processed
  independently.
- Batching and caching may be required later for performance, but they must not
  weaken source-unit identity or Persona comparability.
- Existing broad-context prompts and reports require adaptation.

---

## Rejected alternatives

### Only repair the global semantic comparator

Rejected as the sole solution.

The WP12 retest showed concrete parser and partition failures, but even a
technically valid global comparator would still have to reconcile a large pool
of independently generated broad-context proposals after semantic alignment had
already been lost.

Comparator robustness should still improve, but it is not sufficient as the
primary architecture.

### Use `InformationUnit` as the pre-Agent source unit

Rejected.

An Information Unit is already an interpreted semantic claim with engineering
classification, epistemic state, extraction provenance, confidence, and
review-oriented semantics.

Using it as the pre-interpretation orchestration anchor would conflate source
identity with engineering interpretation.

### Let each Persona create its own source-unit identifiers

Rejected.

This recreates the observed problem because identical identifier strings from
different Agent outputs do not establish shared source identity.

Source Analysis Unit identity must be created before Persona interpretation and
must be persona-independent.

### Human Review after every Source Analysis Unit

Rejected as the default workflow.

It would create excessive interaction overhead and prevent a coherent compiled
review of cross-unit semantic continuity.

Human Review occurs after local consolidation and cross-unit synthesis.

### Pure string matching as semantic identity

Rejected.

Lexical normalization is useful evidence but cannot establish semantic
equivalence across legitimate wording variation.

### Unrestricted embedding or LLM similarity as automatic authority

Rejected.

Similarity may support semantic comparison, but uncertainty must not silently
authorize merges or replace Human Review.

---

## Related decisions

- ADR-016 — Human Review Workspace and Approved Input Promotion Architecture
- ADR-017 — Simple-by-Default Interaction and Progressive Disclosure
- ADR-025 — Semantic Proposal Consolidation and Persona-Aware Consensus

---

## WP12 test status impact

ADR-026 does not mark BLK-003 as closed.

Current interpretation:

```text
BLK-003.1 processing robustness
PASS at focused test / controlled processing level

BLK-003 semantic consolidation effectiveness
OPEN

POST-BLK-003.1 empirical result
Processing reached Human Review
but semantic consolidation degraded to singletons
and produced 112 Review Items
```

WP12-E2E-DRY-001 remains the same interrupted controlled dry run.

The run shall not be restarted solely to hide the observed findings.

After implementation and focused validation of D1-D5, the affected Source-01
processing path shall be empirically retested before WP12 continues through the
remaining blocked stages.

---

## Implementation note

No D1-D5 production implementation shall precede acceptance of this ADR.

The implementation shall reuse existing Source Projection, source-anchor,
semantic extraction, semantic consensus, project isolation, fingerprint,
Human Review, and immutable provenance contracts where compatible.

The architecture shall preserve the distinction between:

- deterministic source segmentation,
- Agent interpretation,
- semantic consolidation,
- Human Review,
- and downstream engineering authority.


\newpage


# ADR-027 — Source-Grounded Evidence Detection and Persona Interpretation Architecture


Status

**Accepted — responsibility-boundary recovery accepted after Source-to-Human-Review audit. Implementation remains incremental and regression-controlled.**

Accepted

2026-08-21

Date

2026-08-20

---

## Context

WP-12 end-to-end formative testing showed that the technical authority chain can reach Human Review, but the semantic quality of the review input is not yet acceptable.

Representative real single-source run:

```text
Project 877791 / RUN-000001
3 personas × 1 run

93 element proposals
41 relationship proposals
134 raw proposals

D3: 70 elements + 39 relationships = 109 subjects
D4: 70 elements + 39 relationships = 109 subjects
Human Review: 110 items = 70 Elements + 39 Relationships + 1 Open Question
```

The D4-to-Review routing is technically operational. The formative result exposed:

1. source purity regression — processing/orchestration/task metadata can become engineering subjects,
2. model relevance regression — candidate content can be generic rather than concrete model-relevant engineering information,
3. subject multiplication — persona-generated candidate identities can create parallel subject sets which later stages attempt to consolidate.

Review Item count is therefore a diagnostic symptom, not the optimization target.

A deliberately simple external benchmark on `legacy/demo/wp12/01_product_overview.md` showed that a general-purpose LLM can identify a small set of source-grounded, model-relevant passages with a direct task and strict source boundary. The benchmark returned eight major source-grounded findings. It is qualitative diagnostic evidence only; it is not a gold standard and shall not become model authority.

ADR-011 already established several still-valid foundations: deterministic Source Projection, source anchors/excerpts, source-traceable Information Units, `engineering_source` vs `context_only`, semantic extraction, persona perspectives, consensus/variance, repeated runs as stability evidence rather than votes, ontology/terminology boundaries, and Human Review separation.

The recovery shall therefore first simplify and realign the existing architecture rather than add another post-processing contract.

---

## Relationship to ADR-025 and ADR-026

ADR-025 — Semantic Proposal Consolidation and Persona-Aware Consensus and
ADR-026 — Source-Anchored Multi-Persona Interpretation and Cross-Unit Semantic
Synthesis remain accepted historical architecture decisions and document the
evolution that led to this recovery.

ADR-027 refines the upstream responsibility sequence exposed by the subsequent
empirical WP-12 test. In particular, source anchoring alone is not sufficient if
Personas can still independently create engineering-subject populations that
must later be reconciled.

ADR-027 therefore moves the common source-grounded evidence space ahead of
Persona interpretation. Existing ADR-025/ADR-026 components remain reusable only
where their responsibility is compatible with this clarified sequencing.

## Decision

### 1. Separate Evidence Detection from Persona Interpretation

```text
Evidence Detection
"Which passages of the Engineering Source contain
potentially model-relevant engineering information?"
        ↓
Persona Interpretation
"What does this already identified source-grounded
engineering information mean?"
```

Detection establishes the common source-grounded evidence space. Persona interpretation operates on that common evidence space.

### 1a. Evidence Detection Is a Specialized Persona-Independent Agent Task

Evidence Detection is an explicit LLM-backed preparation task with one narrowly
bounded responsibility:

```text
Engineering Source
+ project/source identity
+ source projection / processing scope
+ reference examples and detection guidance
        ↓
Specialized Evidence Detection Agent
        ↓
exact source-grounded evidence spans
```

The detector may use curated repository examples, modeling guidance and other
reference knowledge to learn what kinds of engineering information are worth
marking. Those inputs remain `context_only` guidance. They shall never become
positive Project evidence.

The detector shall not:

- create requirements, actors, functions, interfaces or other model elements,
- perform architecture derivation,
- assign final engineering meaning on behalf of interpretation personas,
- create multiple evidence identities merely because several personas or runs
  will later consume the evidence.

Detector output must be independently verifiable against the Source Projection.
At minimum, every accepted evidence span is bound by exact source anchors and an
exact source excerpt.

### 1b. Source Registration and Source Preparation Are Architecturally Separate

The Source Registry remains the immutable authority for Project Source identity.
LLM execution shall not be added to the Source Registry contract itself.

The system distinguishes:

```text
REGISTER SOURCE
    ↓
PREPARE SOURCE
    ↓
Source Projection
    ↓
Evidence Detection
    ↓
SOURCE READY FOR INTERPRETATION
```

The UI may present registration and preparation as one convenient user action.
That is a UX choice, not a collapse of architectural responsibilities.

Evidence Detection is version/configuration dependent. Its persisted result must
therefore remain traceable to the immutable Source version and to the detector
configuration used to create it. Changing persona configuration alone shall not
require Evidence Detection to be repeated.

### 2. Reference Knowledge Is Guidance, Never Engineering Evidence

Reference knowledge may include Apollo 11 reference material, SysML v2/KerML references, modeling guidance, framework definitions, agent roles/personas, recipes, project principles, terminology and ontology references.

It may answer:

```text
"What kinds of engineering information should I look for?"
```

It shall never answer:

```text
"What engineering information exists in this Project Source?"
```

Only a registered `engineering_source` may provide positive engineering evidence. Prompt text, task instructions, recipes, orchestration manifests, processing metadata and reference models shall never become positive Project engineering evidence merely because they were visible to an LLM.

### 3. Source-Grounded Evidence Is the Common Discussion Basis

Every detected Evidence Unit shall remain bound to the Engineering Source through at least:

```text
project_id
source_id
source_projection_id
source_anchor(s)
exact source_excerpt
```

This is the digital equivalent of a text-marker highlight. Evidence identity is source-grounded. An agent-generated candidate name shall not be the primary identity used to determine whether personas are discussing the same source information.

### 4. Personas Interpret the Same Evidence

```text
Evidence E-xxx
      │
 ┌────┼────┐
 ▼    ▼    ▼
P1   P2   P3
      │
      ▼
Consensus / Variance
```

Personas may disagree about relevance confidence, professional interpretation, information type, modality, epistemic class, terminology, model relevance, uncertainty or missing information.

Their disagreement is first-class engineering information. It shall not automatically create multiple independent Review Subjects.

### 5. Persona and Run Counts Shall Not Multiply Engineering Subjects

The number of engineering subjects shall be driven by distinct source-grounded engineering information, not by persona/run count.

```text
Review Subjects ≠ Source Subjects × Personas × Runs
```

Repeated runs measure intra-persona stability and are not additional independent votes. Each persona contributes at most one effective vote to one evidence-centered consensus assessment.

### 6. Evidence Detection May Express Uncertainty

Evidence detection may classify a passage as:

```text
relevant
not_relevant
uncertain
```

One passage may contain multiple independently reviewable claims. The key constraint is that their identities remain source-grounded and are not multiplied merely by persona execution.

### 7. Review Item Count Is a Diagnostic, Not an Optimization Objective

Evaluation shall prioritize:

1. source purity,
2. model relevance,
3. source grounding,
4. clear interpretation/classification,
5. useful consensus/variance,
6. Human Review usability.

A high Review Item count can be correct if the Source contains many independent claims. A count increase caused only by adding personas/runs indicates an architectural or consolidation defect.

### 8. Terminology and Ontology Mapping Are Supporting Services

Semantic normalization, terminology mapping and ontology alignment may improve comparability and cross-source consistency. They shall not create new positive Engineering Source evidence or new subjects without a source-grounded basis.

### 9. Human Engineering Review Precedes Architecture Derivation

```text
Engineering Source
→ Deterministic Source Projection
→ Source-Grounded Evidence Detection
→ Persona Interpretation
→ Consensus / Variance
→ optional Semantic Normalization / Ontology Alignment
→ Human Review
→ Approved Engineering Information
→ Architecture Derivation
→ SysML v2 Generation
```

AI-generated interpretation is evidence for Human Review, not Approved Engineering Information.

### 9a. Architecture Derivation May Again Use Multiple Personas

Multi-persona processing remains valuable after Human Engineering Review.
However, its responsibility changes.

Before Human Review, personas answer:

```text
"What does this same source-grounded Evidence mean?"
```

After approval, model-derivation personas may answer:

```text
"How can this Approved Engineering Information be represented
as a coherent system/model architecture?"
```

Target sequence:

```text
Approved Engineering Information
        ↓
 ┌──────┼──────┐
 ▼      ▼      ▼
Model  Model  Model
Persona Persona Persona
 └──────┼──────┘
        ↓
Architecture / Model Candidate Derivation
        ↓
Semantic Comparison / Candidate Consolidation
        ↓
Model Candidate Review
        ↓
Approved Internal Model
        ↓
SysML v2 Generation
```

Whether this downstream derivation phase shall reuse existing personas or
introduce new task-specific modeling personas is intentionally deferred until
that stage is implemented and evaluated. The architecture requires the persona
branch; it does not prematurely freeze its future persona set.

### 10. Existing P9 Responsibilities Have No Grandfathering Protection

Every material P9/D3/D4 responsibility shall be classified as:

```text
KEEP
MOVE DOWNSTREAM
REDUCE TO ADAPTER
BYPASS
RETIRE
```

A responsibility shall not be retained merely because downstream code currently expects it.

---

## Thesis-Relevant Architecture Finding

The WP-12 formative run exposed a responsibility-ordering defect rather than a
simple clustering or prompt-quality problem.

### Previous responsibility sequence

In the previous active path, each interpretation persona could independently:

1. select what it considered meaningful engineering information,
2. assign persona-local `source_info_id` identities,
3. interpret/classify that selected information,
4. feed a derivation step that created model-oriented candidate elements and
   relationships before the first Human Engineering Review.

Downstream D3/D4 semantic consolidation then attempted to determine which of
those independently created candidate populations referred to the same
engineering subject.

This caused a fundamental comparability problem:

```text
Persona A selection ≠ Persona B selection ≠ Persona C selection
```

Therefore differences between persona outputs could not be interpreted cleanly
as professional interpretation variance. A difference could instead be caused
by different source selection, segmentation, abstraction, naming or early
modeling decisions.

### Observed consequences

The architecture produced several characteristic symptoms:

- **persona-driven subject multiplication** — additional personas could create
  additional subject populations rather than additional views on one subject;
- **unstable engineering-subject identity** — candidate names/types became part
  of the mechanism used to infer common subjects;
- **false or ambiguous variance** — source-selection differences and genuine
  interpretation differences were mixed;
- **excessive downstream semantic repair** — later LLM consolidation had to
  reconstruct commonality that had not been established upstream;
- **premature model derivation** — model structure was proposed from unreviewed
  interpretation;
- **Human Review overload** — the reviewer received a large model-oriented
  subject population rather than a compact set of source-grounded engineering
  information;
- **technical success without engineering effectiveness** — the processing
  chain could reach Human Review correctly while the review content remained
  unsuitable for engineering use.

The representative real single-source run made the effect visible:

```text
3 personas × 1 run
93 element proposals
41 relationship proposals
134 raw proposals
109 D3/D4 semantic subjects
110 Human Review items
```

These counts are evidence of the symptom, not a target to optimize directly.

### Architectural correction

ADR-027 introduces an explicit boundary:

```text
DETECTION
Which source passages are potentially engineering-relevant?
        ↓
fixed source-grounded Evidence identity
        ↓
INTERPRETATION
What does this same Evidence mean from different professional perspectives?
```

Only after Evidence identity is fixed do personas branch. This makes
consensus/variance meaningful because each persona is now discussing the same
source-grounded object.

After Human Engineering Review and approval, a second persona branch may be used
for architecture/model derivation. The key change is therefore not the removal
of personas or LLM reasoning, but the placement of those responsibilities on
the correct side of the engineering approval boundary.

### Thesis interpretation

This is a first-class formative engineering result of the prototype:

> Source grounding alone is insufficient for reliable multi-persona semantic
> processing when evidence detection and persona-specific interpretation share
> the same responsibility boundary. A common evidence identity must be
> established before persona variance can be interpreted as engineering
> variance.

This finding should be carried into the thesis as an observed failure,
root-cause analysis, architectural correction and subsequent evaluation target.

---

## Relationship to ADR-011

ADR-027 does not replace ADR-011 wholesale. The following remain valid unless separately changed:

- deterministic Source Projection,
- immutable Source identity,
- source anchors/excerpts,
- Information Unit traceability,
- `engineering_source` / `context_only`,
- explicit epistemic classification,
- independent persona perspectives,
- deterministic consensus/variance,
- repeated runs as stability evidence rather than votes,
- explicit terminology/ontology mapping,
- Human Review separation.

ADR-027 sharpens the sequencing: a common source-grounded evidence space is established before persona interpretations become downstream Review Subjects.

---

## Qualitative Benchmark

The 2026-08-20 external diagnostic benchmark used exactly:

```text
legacy/demo/wp12/01_product_overview.md
```

A simple source-bounded prompt returned eight major findings around:

- remote microscope collaboration capability,
- microscope operator and remote expert,
- workstation and remote client context,
- remote consultation purpose,
- live-image observation,
- temporary remote control subject to operator permission,
- operator responsibility/controller transparency,
- session-information retention,
- explicitly unspecified protocol/deployment/performance/latency/regulatory information and open product questions.

The benchmark also showed that a general-purpose LLM can jump too early to modeling choices such as State Machine/Guard. Therefore Turing shall distinguish engineering meaning from later model-structure decisions.

The benchmark is diagnostic only and shall not become Project authority.

---

## Recovery Audit

Before implementation changes, trace the active Source-to-Human-Review path end-to-end.

For every material component classify:

```text
KEEP
MOVE
REDUCE
BYPASS
REMOVE / RETIRE
```

The audit shall answer:

1. What exact content is supplied to each LLM call?
2. Which inputs are Engineering Source vs reference/context/instruction?
3. Where are source-grounded anchors first established?
4. Where is a new subject identity first created?
5. Where do personas begin to create independent subject sets?
6. Which P4 semantic-extraction / semantic-consensus components are active in the live path?
7. Why does current P9 derivation appear before the first Engineering Human Review?
8. Which D3/D4 responsibilities remain necessary once evidence-centered subject identity is restored?
9. Which contracts are current authority, and which are legacy compatibility ballast?
10. Can the corrected path reuse existing Source Projection, Information Unit, consensus, Human Review and Approved Input infrastructure?

No new end-to-end LLM run should be used as improvement evidence until this audit identifies and corrects the source-contamination / subject-multiplication path.

---

## CATIA / Presentation Rule

Intended future high-level System Behavior:

```text
REFERENCE KNOWLEDGE
        │ guidance only
        ▼
ENGINEERING SOURCE
        ↓
Register Source
        ↓
Prepare Source
        ↓
Deterministic Source Projection
        ↓
Specialized Evidence Detection Agent
        ↓
Source-Grounded Evidence
        ↓
 ┌──────┼──────┐
 ▼      ▼      ▼
P1     P2     P3
Interpret / Classify the SAME Evidence
 └──────┼──────┘
        ↓
Consensus / Variance
        ↓
optional Semantic Normalization / Ontology Alignment
        ↓
Human Engineering Review
        ↓
Approved Engineering Information
        ↓
 ┌──────┼──────┐
 ▼      ▼      ▼
Model  Model  Model
Persona Persona Persona
 └──────┼──────┘
        ↓
Architecture / Model Candidate Derivation
        ↓
Candidate Consolidation
        ↓
Model Candidate Review
        ↓
Approved Internal Model
        ↓
SysML v2
```

Add this to the authoritative CATIA model only after the implementation is aligned and ADR-027 is accepted. Do not model it merely to improve the Monday presentation.

---

## Status and Next Decision

ADR-027 is `Accepted` as the governing architecture-recovery direction.

The completed Source-to-first-Human-Review audit established that the primary
BLK-003 root cause is the mixing of evidence detection, persona interpretation
and pre-review model derivation.

Implementation proceeds incrementally:

```text
R1  Accept/document ADR-027 and thesis finding
R2  Introduce source-grounded Evidence contract and persistence
R3  Add specialized Evidence Detection Agent and Source Preparation
R4  Make interpretation personas consume the same persisted Evidence
R5  Move model derivation behind Human Engineering Approval and evaluate
    the required downstream modeling personas
```

After at least one material corrected processing path exists:

- run focused automated tests,
- run one bounded real single-source LLM test,
- evaluate source purity, model relevance, source grounding, persona behavior
  on shared Evidence, consensus/variance and Human Review usability,
- retain a successful persisted run as the preferred Monday demo reference.


\newpage


# ADR-028a — Model Derivation Mode and Review Escalation Architecture


## Status

Accepted

## Date

2026-08-21

## Context

The corrected WP12 architecture establishes source-grounded Evidence before
persona interpretation and introduces Human Engineering Review before target
model derivation.

Phase H already consumes only active Approved Inputs and already supports both
strict deterministic profile projection and LLM-assisted hybrid projection.
The Human Model Candidate Review remains mandatory regardless of the selected
derivation strategy.

The architecture includes an explicit energy-saving mode and a Human-controlled
escalation path:

- model derivation may run without any LLM call,
- LLM assistance is optional rather than mandatory,
- the system may recommend a strategy based on deterministic projection
  coverage,
- the Human remains free to select the strategy,
- and a rejected deterministic Candidate may be regenerated with LLM assistance
  without repeating the upstream Engineering Review.

A further architectural issue follows from ADR-020 H9-05. The normal hybrid path
does not send deterministically mapped Approved Inputs to the LLM. A Candidate
that was deterministically mapped successfully but later rejected by the Human
Model Review therefore requires an explicit escalation exception: the Approved
Inputs supporting that rejected predecessor Candidate must become eligible for
LLM re-proposal even if deterministic coverage still reports them as `mapped`.

---

## Decision

### R5-01 — Two explicit derivation modes

The supported Human-selectable derivation modes are:

- `eco_deterministic`
- `llm_assisted`

`eco_deterministic` performs no LLM request.

`llm_assisted` remains deterministic-first but may invoke bounded modeling LLM
reasoning for eligible Approved Inputs.

The selected mode does not remove the downstream Model Candidate Review.

---

### R5-02 — Strategy recommendation is advisory

Before Candidate generation, deterministic projection coverage shall be
assessed.

The system may recommend:

- `eco_deterministic` when all projectable Approved Inputs are deterministically
  mapped,
- `llm_assisted` when projection remains ambiguous or unmapped,
- or `llm_assisted` when a predecessor Candidate has been rejected and explicit
  Human escalation is requested.

The recommendation is advisory. It shall not silently select or approve a
derivation strategy on behalf of the Human.

---

### R5-03 — Eco mode remains fail-closed

Eco mode shall not weaken deterministic profile safety.

If deterministic projection cannot establish the target mapping required to
generate a complete Candidate Set, the strict deterministic deriver may fail
closed.

The system shall not force a target mapping merely to keep the no-LLM path
running.

---

### R5-04 — Human Model Review remains mandatory

Candidate Sets generated in either mode shall pass the same Candidate Review
authority boundary.

A deterministic result is not approved merely because it was reproducible.

An LLM-assisted result is not approved merely because multiple modeling
perspectives agree.

---

### R5-05 — Review rejection may escalate to LLM-assisted regeneration

A rejected predecessor Candidate may trigger a successor Candidate Set with:

- `predecessor_candidate_set_id`,
- a non-empty `regeneration_reason`,
- and derivation mode `llm_assisted`.

The predecessor Candidate Set remains immutable and traceable.

---

### R5-06 — Explicit escalation eligibility

For review-driven regeneration, the system shall resolve each currently rejected
predecessor Candidate to its immutable Approved Input references.

Those Approved Input IDs become explicit escalation targets.

Explicit escalation targets may enter LLM-assisted modeling even when the
deterministic resolver still classifies them as `mapped`.

This is the only accepted exception to the normal ADR-020 rule that uniquely
mapped inputs are not sent to the LLM.

The exception requires an explicit Human Model Review rejection bound to the
exact predecessor Candidate snapshot.

---

### R5-07 — No upstream reinterpretation

Review escalation shall not return to Source Evidence Detection, pre-review
persona interpretation, or Human Engineering Review unless the Human explicitly
changes the approved engineering information itself.

The LLM escalation task remains target-model derivation only.

---

### R5-08 — Modeling personas are LLM-mode only

The modeling persona team is not executed in Eco mode.

In `llm_assisted` mode, eligible Approved Inputs may be assessed by the accepted
modeling perspectives:

- rules-focused,
- architecture-focused,
- conservative review.

All modeling personas receive the same Approved Input identities and the same
profile-controlled target options.

---

### R5-09 — Modeling comparison is bounded by profile rules

Modeling-persona comparison shall operate on profile-controlled mapping results,
not free-form semantic subjects.

Agreement may yield one proposed mapping.

Material disagreement shall remain explicit variance / ambiguity.

No comparison step may invent new Approved Engineering Information.

---

## Target lifecycle

```text
Approved Engineering Information
        |
        v
Deterministic Projection Coverage
        |
        +--> Recommendation: Eco / LLM-assisted
        |
        v
Human-selected Derivation Mode
   |                         |
   | Eco                     | LLM-assisted
   v                         v
Strict deterministic      deterministic-first
projection               + modeling personas
   |                         |
   +------------+------------+
                v
        Model Candidate Set
                |
                v
        Human Model Review
          |             |
       accepted       rejected
          |             |
          v             v
     Internal Model   Regenerate successor
                        |
                        +--> LLM-assisted escalation
                             of rejected Candidate's
                             Approved Input support
```

---

## Consequences

### Positive

- zero-LLM model derivation remains available,
- deterministic results still receive Human authorization,
- LLM use becomes targeted and explainable,
- review rejection becomes a first-class feedback mechanism,
- successful upstream Engineering Review is not repeated unnecessarily,
- and predecessor/successor Candidate Sets preserve full traceability.

### Trade-offs

- the LLM-assisted executor must distinguish normal unresolved targets from
  explicit review-escalation targets,
- the recommendation layer is not itself an authority decision,
- and deterministic Eco mode may legitimately fail when the selected profile
  cannot resolve the approved information.

---

## Implementation decomposition

```text
R5a  Derivation mode, recommendation and review-escalation contract
R5b  Modeling-persona execution and bounded comparison for LLM-assisted mode
R5c  Guided Workflow integration and full regression / demo path
```

R5a introduces no new LLM call.


\newpage


# ADR-028b — Context-Preserving Canonical Engineering Subject Discovery


Status

**Accepted — architecture direction approved on 2026-08-23; implementation and live validation pending.**

Date

2026-08-23

---

## Context

WP-12 recovery under ADR-027 successfully restored a shared, source-grounded Evidence population before Persona interpretation. The corrected live path demonstrated:

- deterministic Source grounding,
- stable `EVD-*` identity,
- all Personas operating on the same Evidence population,
- no multiplication of Review Subjects by Persona count,
- successful propagation into Human Review.

However, formative Human Review testing exposed a second semantic defect.

The active interpretation contract effectively maps one `SourceEvidence` object to one interpreted statement and one information type. A single Evidence block can contain multiple distinct engineering subjects and assertions. The result is therefore often a paraphrase of the Evidence block rather than an MBSE-relevant decomposition.

Example:

```text
The remote expert shall be able to observe the live microscope image.
During the session, the expert may also take temporary control of the microscope
when the operator permits it.
The operator remains responsible for the local session and must be able to
understand who currently controls the microscope.
```

This source passage contains several independently meaningful engineering subjects, for example:

```text
Remote Expert
Live Microscope Image Observation
Temporary Microscope Control
Operator Permission
Operator Responsibility
Current Controller Awareness
```

Treating the whole passage as one semantic subject loses this structure.

A second issue is context starvation. Provenance units were made deliberately small to guarantee exact source grounding. Those small units are useful for identity and traceability, but they are not necessarily the correct context window for LLM interpretation.

The architecture therefore needs to distinguish:

```text
small deterministic provenance units
from
larger context-preserving interpretation windows
```

A third issue is repeated mention. The same engineering subject can occur several times in one source. Repeated mentions shall not be interpreted independently by every Persona.

---

## Relationship to ADR-027

ADR-028 refines ADR-027; it does not reverse it.

ADR-027 remains authoritative that:

1. source-grounded identity is established before Persona variance,
2. Personas shall not independently create incomparable subject populations,
3. Reference Knowledge is guidance only,
4. Human Review precedes model derivation,
5. Persona/run counts shall not multiply engineering subjects.

ADR-028 adds one missing level between SourceEvidence and Persona interpretation:

```text
SourceEvidence / Source Spans
→ Mention Discovery
→ Canonical Engineering Subjects
→ Shared Persona Interpretation
```

ADR-027's phrase "Personas interpret the same Evidence" is therefore refined to:

> Personas interpret the same canonical engineering subjects, with all of those subjects bound to exact source-grounded Evidence and adequate surrounding source context.

---

## Decision

### 1. Provenance Unit and Interpretation Context Are Separate Concepts

The system shall not assume that the smallest deterministic source unit is also the best LLM interpretation context.

```text
Provenance unit
→ small, exact, deterministic, source-bound

Interpretation context
→ larger, readable, context-preserving, still source-only
```

For a small source document, the interpretation context may be the complete Engineering Source. For larger documents it may be a section or bounded multi-paragraph window.

Every discovered Mention and Canonical Subject shall still bind to exact deterministic source spans.

### 2. One SourceEvidence Does Not Equal One Engineering Subject

The following cardinalities are valid:

```text
1 SourceEvidence → 0..n Mentions
1 SourceEvidence → 0..n Canonical Engineering Subjects
1 Canonical Engineering Subject → 1..n Mentions
1 Canonical Engineering Subject → 1..n SourceEvidence references
```

SourceEvidence establishes that source content is engineering-relevant and provides provenance.

It is not the semantic subject identity.

### 3. Mention Discovery Precedes Professional Interpretation

A shared discovery stage shall identify source mentions of potentially MBSE-relevant engineering subjects without assigning the final professional interpretation.

Examples include mentions of:

- people or roles,
- systems or applications,
- capabilities,
- functions or behaviors,
- information or data,
- states,
- constraints,
- requirements,
- interfaces,
- relationships,
- unresolved engineering questions.

The discovery stage may propose a neutral canonical label but shall not make the final SysML v2 representation decision.

### 4. Repeated Mentions Shall Be Consolidated Before Persona Interpretation

Multiple mentions of the same engineering subject shall resolve to one Canonical Subject whenever equivalence is sufficiently established.

Example:

```text
"The remote expert"
"the expert"
"remote expert"

→ one Canonical Subject:
SUBJ-000003 Remote Expert
```

The Canonical Subject retains all supporting Mention references.

Personas shall interpret `SUBJ-000003` once each, not once per Mention.

### 5. Ambiguous Identity Shall Not Be Silently Merged

If two mentions may refer to the same subject but equivalence is uncertain, the system shall preserve that uncertainty.

Allowed:

```text
SUBJ-000010
SUBJ-000011
possible_equivalence = uncertain
```

Not allowed:

```text
silent merge based only on lexical similarity
```

Deterministic normalization may merge trivial representation variants such as case and surrounding whitespace, but semantic/coreference consolidation beyond trivial equivalence is explicit and traceable.

### 6. Canonical Subject Identity Is Established Before Persona Variance

Canonical Subject identity shall not contain the Persona's semantic classification.

For example:

```text
SUBJ-000003
canonical_label: Remote Expert
mentions: MNT-000004, MNT-000009, MNT-000014
```

Personas then independently assess the same Subject:

```text
P1 → Actor
P2 → External Actor
P3 → Actor
```

`Actor` vs `External Actor` is interpretation variance, not Subject identity variance.

### 7. Personas Receive the Same Subject Population and Adequate Context

Each configured Persona shall receive:

```text
same Canonical Subject IDs
same Mention bindings
same source-only interpretation context
same Source authority
```

Persona prompts shall ask for professional interpretation/classification of the supplied subjects.

They shall not independently rediscover the subject population.

### 8. One Persona Produces At Most One Effective Interpretation per Canonical Subject

For one effective run:

```text
(Persona ID, Canonical Subject ID) → at most one effective interpretation
```

Repeated runs remain stability evidence and shall not multiply professional votes.

### 9. Interpretation Shall Be MBSE-Relevant but Pre-Model

Persona interpretation may classify engineering meaning such as:

```text
actor
stakeholder
system
external_system
capability
function
behavior
requirement
constraint
information
interface
state
relationship
open_question
other_engineering_subject
```

The exact controlled vocabulary is an implementation contract and may evolve.

This stage shall not prematurely decide the final SysML v2 model representation where multiple valid representations remain possible.

For example:

```text
engineering meaning: Actor
```

is permitted, while an unnecessary early decision about the exact later SysML containment/package structure is not.

### 10. Assertion/Relationship Meaning May Be Separate from Entity Subjects

The system shall support engineering semantics that are not simple noun-like entities.

Example source:

```text
The expert may take temporary control when the operator permits it.
```

Possible canonical subjects include:

```text
Remote Expert
Temporary Microscope Control
Operator Permission
```

and an assertion/relationship may express:

```text
Temporary Microscope Control
is constrained by
Operator Permission
```

The implementation may represent entity-like subjects and assertion-like subjects with one shared base contract or explicit subtypes, provided identity and provenance remain deterministic.

### 11. Human Review Operates on Canonical Engineering Subjects

Human Review shall present professionally meaningful subjects, not raw Evidence blocks.

Target presentation:

```text
Remote Expert
Proposed engineering meaning: Actor

P1: Actor
P2: External Actor
P3: Actor

Source mentions:
- SENT-002: "The remote expert ..."
- SENT-007: "... the expert ..."
```

The Human may accept, modify, reject, defer, mark out of scope, split, merge or resolve uncertainty according to the Review contract.

### 12. Model Derivation Remains Downstream

The corrected sequence is:

```text
Engineering Source
→ Deterministic Source Projection / Source Spans
→ Source-Grounded Evidence Detection
→ Context-Preserving Mention Discovery
→ Cross-Mention Canonical Subject Consolidation
→ Shared Persona Interpretation
→ Field-Level Consensus / Variance
→ Human Engineering Review
→ Approved Engineering Information
→ Architecture / Model Derivation
→ SysML v2
```

No Canonical Subject becomes Approved Engineering Information without Human Review.

---

## Canonical Identity Principle

The architecture shall preserve the following invariant:

> Mentions are many; canonical engineering subjects are unique within the resolved source scope; professional interpretations are many per subject only because Personas may legitimately disagree.

Formally:

```text
Source Span
    ↓
Mention
    ↓
Canonical Engineering Subject
    ↓
Persona Interpretation
    ↓
Consensus / Variance
    ↓
Human Review
```

Identity shall be established before variance.

---

## Source Context Principle

A key lesson from WP-12 is:

> Small units are required for exact provenance; larger context is often required for correct interpretation.

The architecture shall therefore never reduce LLM context merely to mirror persistence granularity.

Source context supplied to the LLM shall remain bounded to registered Engineering Source content plus clearly separated reference guidance. Reference guidance shall never become positive engineering evidence.

---

## Expected Effect on the WP-12 Example

A context-preserving discovery of the Remote Microscope Collaboration source is expected to identify a small canonical population resembling, without prescribing as a gold standard:

```text
Remote Microscope Collaboration
Microscope Operator
Remote Expert
Microscope Workstation
Client Application
Live Microscope View Sharing
Remote Consultation
Live Image Observation
Temporary Microscope Control
Operator Permission
Operator Responsibility
Current Controller Awareness
Session Information Retention
Retention Period
Connection Quality Limits
Remotely Controllable Microscope Functions
```

The exact result remains LLM-generated and subject to Human Review.

Repeated appearances of e.g. `Remote Expert` shall contribute additional provenance Mentions to one Canonical Subject rather than create duplicate subjects.

---

## Rejected Alternatives

### A. Continue One-Evidence-to-One-Interpretation

Rejected because one Evidence block can contain multiple independent engineering subjects and assertions.

### B. Let Every Persona Rediscover Subjects Independently

Rejected because it recreates the original subject-population mismatch and makes downstream consensus compare non-equivalent populations.

### C. Match Independent Persona Outputs Only Afterward

Rejected as the primary architecture because late semantic matching recreates the exact reconciliation problem ADR-027 was introduced to avoid.

### D. Use Sentence Identity as Engineering Subject Identity

Rejected because one sentence may contain multiple engineering subjects and one engineering subject may appear across multiple sentences.

### E. Interpret Every Mention Independently

Rejected because repeated mentions of the same engineering subject would multiply work and votes without adding engineering value.

---

## Implementation Slices

### R4c.1 — Contract and deterministic identity

Introduce immutable contracts for:

```text
SourceSpanReference
EngineeringMention
CanonicalEngineeringSubject
CanonicalSubjectSet
```

including fingerprints and fail-closed validation.

### R4c.2 — Context-preserving Subject Discovery

Use a dedicated LLM task over a source-only context window to discover `0..n` Mentions and propose cross-mention subject groupings.

The system, not the LLM, binds returned span IDs to exact source text.

### R4c.3 — Canonical consolidation

Resolve trivial deterministic equivalence directly and validate LLM-proposed cross-mention grouping.

Unknown spans, impossible mention ranges, duplicate identities, unsupported merges and ungrounded subjects fail closed.

### R4c.4 — Shared Persona Interpretation

All Personas interpret the same Canonical Subject Set and return one professional interpretation per subject.

### R4c.5 — Subject-centered Consensus and Human Review

Consensus compares fields for the same `SUBJ-*`. Human Review is generated from those subjects and their Persona interpretations.

### R4c.6 — Live validation and documentation closeout

Validate on the WP-12 source, run focused/full regression, update recovery findings and SSOT, and only then stage/commit.

---

## Acceptance Criteria

R4c is successful only if the real single-source test demonstrates all of the following:

1. source context remains readable to the LLM,
2. every Canonical Subject is grounded in exact source spans,
3. one passage may yield multiple MBSE-relevant subjects,
4. repeated mentions of one subject are consolidated,
5. Personas interpret the same Subject population,
6. adding Personas does not add Subject identities,
7. Human Review displays meaningful engineering semantics,
8. no positive engineering subject is derived from instructions/reference knowledge,
9. no premature SysML v2 model structure is treated as approved fact,
10. the resulting Review is usable by a Systems Engineer.

---

## Thesis-Relevant Finding

The WP-12 recovery produced a second architectural finding:

> Provenance granularity and interpretation granularity are different concerns. Exact traceability benefits from small deterministic source units, while semantic understanding benefits from larger context windows. A robust AI4MBSE pipeline should therefore establish canonical engineering-subject identity from source-bound mentions before professional interpretation, while supplying sufficient surrounding source context to the interpreting model.

This finding complements ADR-027's earlier conclusion that source evidence detection and professional interpretation must be separated.


\newpage


# ADR-029 — Human-Reviewed Model Placement Before Model Assembly


## Status

Accepted

## Date

2026-08-24

## Context

The first live LLM-assisted Phase-H generation attempt for WP-12 Project
`120412` exposed `BLK-006`.

The three modeling personas successfully produced and persisted bounded
projection results for 14 unresolved Approved Inputs. The consolidated result
contained:

```text
proposed_mapping: 0
ambiguous:        10
unmapped:          4
```

The current hybrid deriver treats every non-`proposed_mapping` result as a
generation failure and therefore aborts before a reviewable Candidate Set can
be produced.

This behavior is inconsistent with ADR-028. Material modeling disagreement was
intended to remain explicit variance while Human Model Review remains the
authority boundary.

A second architectural issue became visible at the same time: the current
Phase-H step tries to resolve local framework placement and assemble a complete
model in one operation. This combines two materially different reasoning tasks.

The accepted recovery pattern from R4c is applicable again:

```text
local identification / interpretation
-> Human authority
-> later synthesis
```

For model creation this becomes:

```text
placement
-> Human placement authority
-> model assembly
-> Final Model Review
```

## Decision

### MPA-01 — Model placement is a separate stage

Before model assembly, every model-promotable Approved Engineering Subject shall
receive an explicit placement decision against the pinned Model Structure
Profile.

Placement answers:

> Where does this approved engineering information belong in the model
> framework?

The exact placement is expressed through a profile-controlled rule. The rule
therefore carries the target framework location, model area, element type and,
where encoded by the profile, the Stakeholder / System / Subsystem level.

### MPA-02 — Dedicated modeling personas

LLM-assisted placement uses three dedicated modeling personas:

1. SysML / Profile Modeler
2. System Architecture Modeler
3. Conservative Modeling Reviewer

They belong to the model-placement responsibility and shall not reuse the
legacy-processing / derivation-assessment persona definitions.

All three receive the same Approved Engineering Information and the same pinned
placement options.

### MPA-03 — Persona agreement is not a gate

The three personas reason independently.

Valid outcomes include:

```text
3:0 agreement
2:1 variance
1:1:1 variance
ambiguous
unmapped
```

No majority result and no unanimity requirement may silently create engineering
or model authority.

Persona agreement is advisory evidence only.

### MPA-04 — Human Model Placement Review is the authority boundary

Persona placement results are collected into a reviewable placement bundle.

The Human reviewer decides the authoritative placement for each required
Approved Subject.

The UX shall intentionally follow the already established Human Engineering
Review pattern where practical:

- Pending Decisions
- Reviewed Decisions
- All
- Needs Attention
- explicit persona proposals / variance
- immutable decision history
- explicit reopen / correction

A Human placement decision may select only a placement allowed by the pinned
profile unless a separately governed exception path is introduced.

### MPA-05 — Approved Model Placement Set precedes assembly

A complete, Human-resolved placement population forms the input authority for
model assembly.

Model assembly answers a different question:

> How do the already placed engineering building blocks form one coherent
> model?

Assembly may construct hierarchy, topology and relationships and may create
multiple coordinated model views. One source does not imply one diagram, and
one approved engineering subject does not imply one complete diagram.

### MPA-06 — Relationships remain engineering authority for later assembly

Accepted semantic Relationships from Approved Engineering Information are not
forced through the per-Subject placement decision merely to obtain a target
relationship type.

They remain authoritative engineering semantics and are consumed during the
later assembly / target-representation stage.

### MPA-07 — Preview belongs primarily after placement resolution

The placement stage may show a lightweight framework overview to communicate
where Subjects are being sorted.

Rich model preview belongs after placement decisions have been resolved and
assembly has produced a coherent model draft. That later review may expose
multiple coordinated diagrams and a textual notation view.

### MPA-08 — Candidate / model materialization follows Human placement

A complete Model Candidate Set or Internal Model shall not be created by
silently resolving modeling-persona variance.

Human-resolved placement authority must precede deterministic model
materialization / assembly.

## Relationship to ADR-028

ADR-028 remains authoritative for:

- `eco_deterministic` versus `llm_assisted`,
- advisory strategy recommendation,
- mandatory Human authority,
- review-driven LLM escalation,
- and no upstream reinterpretation.

This ADR supersedes any implementation interpretation of ADR-028 R5-09 that
treats persona unanimity as a prerequisite for successful LLM-assisted
processing. Modeling comparison preserves variance for Human resolution.

## Target lifecycle

```text
Approved Engineering Information
        |
        v
Deterministic placement coverage
        |
        v
Human-selected mode
   |                 |
   | Eco             | LLM-assisted
   v                 v
profile proposal   3 placement personas
   |                 |
   +--------+--------+
            v
Model Placement Review Bundle
            |
            v
Human Model Placement Review
            |
            v
Approved Model Placement Set
            |
            v
Model Assembly
            |
            +--> coordinated diagram view(s)
            +--> textual notation
            |
            v
Final Model Review
            |
            v
Approved downstream model
```

## Consequences

### Positive

- placement uncertainty becomes reviewable rather than fatal,
- Stakeholder / System / Subsystem assignment becomes an explicit Human
  modeling decision,
- persona disagreement becomes useful engineering evidence,
- model assembly receives cleaner, already placed building blocks,
- and the Final Model Review can focus on the assembled model rather than
  re-litigating local placement.

### Trade-offs

- Phase H gains an additional persisted Human authority boundary,
- current direct Candidate generation must be decomposed,
- and the existing Candidate Review / Final Model Review integration must be
  reconciled with the new placement stage without weakening traceability.

## Implementation slices

```text
BLK-006 C1  architecture + dedicated model-placement department
BLK-006 C2  immutable placement proposal / comparison contracts
BLK-006 C3  persisted Human Model Placement Review
BLK-006 C4  Guided Workflow UI analogous to Human Engineering Review
BLK-006 C5  placement-authoritative model assembly + Candidate materialization
BLK-006 C6  live same-project retest + downstream Final Model Review
```


\newpage


# ADR-030 — Semantic Interpretation and Controlled Classification Alignment


Semantic Interpretation and Controlled Classification Alignment

Status

Accepted

Date

2026-08-26

## Context

Cross-source processing tests exposed a recurring semantic-contract failure
that is distinct from source grounding and from system integrity.

An LLM may return a professionally meaningful classification expression such
as `architecture`, `condition` or `classification` even when ADR-011 defines a
closed internal `information_type` vocabulary. Treating every such expression
as a failed interpretation couples two responsibilities that should remain
separate:

1. understanding what the Engineering Source means; and
2. representing that meaning with the repository's controlled vocabulary.

The observed terms are test vectors, not mapping rules. This ADR does not
authorize hard-coded aliases such as `architecture -> logical_element` or
`condition -> constraint`; those mappings are context-dependent.

## Decision

The semantic workflow shall explicitly separate professional interpretation
from controlled representation:

```text
Engineering Source
    -> LLM professional interpretation
    -> controlled classification alignment
    -> strict internal semantic contract
    -> consensus / Human Engineering Review
```

An LLM-produced classification expression is a semantic proposal, not an
authoritative internal enum value.

### Controlled classification alignment

Before an interpretation enters the strict internal semantic contract, any
out-of-vocabulary required classification expression shall be aligned to the
accepted controlled vocabulary.

The alignment input retains at least immutable item identity (`EVD-*` or
`SUBJ-*`), field name, raw LLM expression, interpreted statement, relevant
source-grounded context when available and the complete allowed target
vocabulary.

The alignment may change only the explicitly identified classification field.
It may not change item identity or population, interpreted statements, source
grounding, rationale, uncertainty, missing-evidence content, relationships or
already-valid fields.

A value already contained in the controlled vocabulary passes through
unchanged. Pure case/whitespace normalization to one unique controlled token
may be performed deterministically.

If semantic inference is required, exactly one bounded alignment LLM request
may translate the raw expression to the controlled vocabulary using context.
The mapper reports `mapped`, `ambiguous` or `unmapped`.

For `information_type`, `ambiguous` and `unmapped` normalize to the already
accepted neutral value `unclassified`. If the mapper itself violates its
contract, `information_type` may likewise fail safely to `unclassified` rather
than inventing a more specific meaning. A classification dimension without an
accepted neutral value remains fail-closed if alignment cannot produce a valid
controlled target.

The original LLM output remains immutable. Every non-identity alignment is
retained as an auditable record containing item identity, field name, raw
value, normalized value, mapping status, concise rationale/fallback reason,
mapper response identity when applicable and a deterministic fingerprint.
Only normalized controlled values may enter downstream semantic processing.

### Boundary to ontology mapping

Controlled Classification Alignment is not ADR-011 P4.3 Terminology and
Ontology Candidate Mapping. It maps an interpreted classification expression
to the existing engineering classification vocabulary. Project Glossary,
Turing Core, IOF Core and BFO mapping remain downstream and may not become
accidental `information_type` values.

### No source-specific aliases

No source-specific or example-specific semantic alias table is permitted. The
system shall not contain hard-coded translations for the terms observed in the
current test sources.

### Validation boundary

Strict deterministic parsing remains mandatory after alignment. Classification
alignment shall not repair malformed JSON, missing or duplicate item
identities, changed item population, invalid source references, malformed
relationships or system-owned integrity corruption.

The existing bounded R4c classification repair is superseded as the primary
handling of out-of-vocabulary classification expressions. A compatibility
projection may temporarily remain, but the architectural authority is the
explicit alignment artifact.

## Consequences

Semantically reasonable wording no longer fails only because the LLM did not
reproduce one exact internal token. Downstream stages receive normalized
controlled values and unresolved precision becomes visible as `unclassified`
instead of a technical processing failure.

An additional LLM call may be required for persona outputs containing
out-of-vocabulary classifications. Raw and normalized semantics remain fully
traceable.

## Acceptance Criteria

1. Valid controlled classifications pass without an alignment LLM call.
2. Lexical-only normalization is deterministic.
3. Out-of-vocabulary Information Types can be contextually mapped by one
   bounded alignment call.
4. Ambiguous/unmapped Information Types become `unclassified`.
5. An invalid mapper response cannot inject an uncontrolled value.
6. Raw LLM output remains unchanged and alignment provenance is auditable.
7. Structural/identity/integrity failures remain fail-closed.
8. The same alignment service is reusable by Evidence and Subject
   interpretation.
9. No source-specific alias for observed test expressions is introduced.


\newpage


# ADR-031 — Semantic Field Consistency Alignment


Semantic Field Consistency Alignment

Status

Accepted

Date

2026-08-26

## Context

ADR-030 separates professional semantic interpretation from controlled
classification vocabulary. Cross-source retry RUN-000004 then exposed a
different failure class after classification alignment had succeeded.

The LLM returned a valid controlled `epistemic_class="explicit"` together with
a non-null object-valued `missing_evidence`. The object contained explanatory
semantic text, but the strict internal contract requires the coupled invariant:

- `assumption` -> non-empty textual `missing_evidence`
- `explicit` / `interpretation` -> `missing_evidence=null`

This is not a classification-vocabulary problem. It is a semantic consistency
problem between two coupled fields.

Blindly coercing every non-null value to null would be unsafe because a
non-null value may represent a genuine evidence gap and therefore justify
`epistemic_class="assumption"` instead. The system must preserve meaning rather
than merely force schema validity.

The same coupled invariant exists in both Evidence Interpretation and Subject
Interpretation.

## Decision

A reusable Semantic Field Consistency Alignment step shall run after
Controlled Classification Alignment and before the strict interpretation
parser:

```text
LLM professional interpretation
    -> Controlled Classification Alignment
    -> Semantic Field Consistency Alignment
    -> strict deterministic interpretation contract
    -> downstream consensus / Human Engineering Review
```

The first supported coupled field set is:

```text
epistemic_class + missing_evidence
```

### Trigger

No additional LLM request is made when the pair already satisfies the internal
contract.

A consistency-alignment need is created only when:

- `epistemic_class` is one of the accepted pre-review values; and
- the coupled `missing_evidence` value violates the deterministic pair
  invariant.

Malformed item identity, changed population, malformed JSON, invalid
relationships and other structural failures remain outside this recovery
mechanism.

### Bounded contextual resolution

All inconsistent pairs in one persona output are batched into exactly one
bounded mapper request.

For each requested item the mapper sees immutable item identity, interpreted
statement, raw epistemic class, raw `missing_evidence`, and source-grounded
context when available.

The mapper may change only:

- `epistemic_class`
- `missing_evidence`

It may not change identity, population, interpreted statement, information
type, modality, rationale, uncertainties, relationships or source grounding.

The normalized pair must satisfy exactly one of:

- `explicit` + `null`
- `interpretation` + `null`
- `assumption` + non-empty trimmed text

If the raw non-null content represents a genuine evidence gap, the mapper may
normalize to `assumption` and preserve that meaning as concise textual
`missing_evidence`. If it does not represent a genuine evidence gap, the
mapper may retain/choose `explicit` or `interpretation` and normalize
`missing_evidence` to null.

There is no automatic neutral fallback for an unresolved pair. Mapper
execution or contract failure remains fail-closed because silently discarding
possible missing-evidence meaning would reduce semantic quality.

### Auditability

The raw LLM output remains immutable.

Every non-identity consistency decision is persisted with raw and normalized
pair values, rationale, mapper response identity and deterministic
fingerprint. Only the normalized output enters the strict parser.

### Boundary to ADR-030

ADR-030 remains authoritative for controlled classification vocabulary and
continues to prohibit Classification Alignment from modifying
`missing_evidence`.

ADR-031 is a separate downstream consistency layer for semantically coupled
fields and does not weaken ADR-030's field authority.

## Acceptance Criteria

1. Already-consistent pairs pass without an additional LLM request.
2. Inconsistent pairs are batched into one bounded request per persona output.
3. The mapper may modify only `epistemic_class` and `missing_evidence`.
4. Every normalized pair satisfies the deterministic pair invariant.
5. Genuine evidence-gap meaning can be retained by normalizing to
   `assumption` plus textual `missing_evidence`.
6. No automatic nulling or other lossy fallback is permitted on mapper
   execution/validation failure.
7. Raw outputs remain immutable and decisions are auditable.
8. The service is reusable by Evidence and Subject Interpretation.
9. Structural, identity and relationship failures remain fail-closed.


\newpage


# ADR-032 — Project-Level Multi-Source Reconciliation and Controlled Engineering Evolution


## Status

Accepted

## Date

2026-08-31

## Context

The Turing Generator currently processes registered engineering sources through source-bound Processing Runs.

ADR-012 intentionally established this boundary:

- one Processing Run processes one primary registered source;
- Information Units and downstream processing evidence remain source-traceable;
- equivalent, overlapping, or contradictory information from different sources remains separate during source-level processing;
- project-level comparison is a later responsibility.

ADR-025 and ADR-026 subsequently introduced source-anchored semantic consolidation and Persona-aware interpretation. These capabilities improve semantic consistency inside one source-processing context but do not provide project-level reconciliation between independent registered sources.

WP-12 Multi-Source validation exposed BLK-002:

> Local Processing Artifact identities such as `IU-000001` can legitimately occur in different Processing Runs, while existing project-level lifecycle aggregation incorrectly treats `(artifact_type, artifact_id)` as a globally unique artifact identity.

Resolving this collision is necessary but not sufficient for genuine Multi-Source processing.

A project may contain:

- several engineering sources describing the same product;
- complementary documents covering different engineering viewpoints or abstraction levels;
- contradictory or materially different statements;
- context-only documents that establish product terminology and system understanding;
- accidentally registered documents that belong to another project;
- already Human-reviewed engineering information and an existing accepted model when additional sources are registered later.

The architecture must therefore distinguish:

1. source-local processing;
2. project-fit assessment;
3. cross-source semantic reconciliation;
4. Human Engineering Authority;
5. model-impact reconciliation.

The system must not force unrelated information into a common semantic subject merely because it belongs to the same Project.

Likewise, absence of semantic overlap does not imply that a source belongs to another Project. A BOM, interface specification, requirement specification and user-needs document may contain very different terminology while still describing the same product.

Context-only sources exist explicitly to provide product and domain understanding and shall be usable for this purpose without becoming Engineering Authority.

Finally, newly registered information must not automatically override already reviewed information merely because the source is newer. Existing Human-reviewed Approved Inputs remain Engineering Authority until a subsequent Human Review explicitly changes that authority.

---

## Decision

### 1. Preserve source-bound Processing

Existing source-level Processing remains source-bound.

The following conceptual flow remains unchanged:

```text
Registered Source
→ Source Projection
→ Source Analysis Units / Evidence
→ Source-local Interpretation
→ Canonical Engineering Subjects
→ Persona Interpretation / Consensus
```

No source-level Processing stage may silently merge evidence from independent registered engineering sources.

All source-derived artifacts retain exact Source, Processing Run and Attempt provenance.

---

### 2. Qualify Processing Artifact identity by Processing Run at project-wide boundaries

A `ProcessingArtifactReference` remains a run-local artifact reference and shall not be extended with additional persisted identity fields solely to resolve BLK-002.

Within one Processing Run, local artifact identity remains:

```text
(artifact_type, artifact_id)
```

At boundaries that aggregate several Processing Runs, effective artifact identity becomes:

```text
(processing_run_id, artifact_type, artifact_id)
```

Therefore:

```text
RUN-000001 / information_unit / IU-000001
RUN-000002 / information_unit / IU-000001
```

are legal independent artifacts.

Within the same Processing Run, conflicting immutable references, duplicate publication and other existing integrity violations remain fail-closed.

No historical Processing Artifact manifest migration is required.

---

### 3. Introduce a Project Fit Assessment before cross-source reconciliation

A processed source shall not automatically participate in project-level semantic reconciliation merely because it was registered inside the Project.

A new derived processing boundary shall assess:

> Is this source plausibly part of the product/system represented by this Project?

The assessment may use:

- explicit `context_only` sources;
- project/product contextual information;
- terminology and concepts derived from existing project sources;
- existing active Human-reviewed engineering information where available.

Context information is evidence for project understanding. It is not Engineering Authority merely because it is used by the Project Fit Assessment.

The Project Fit Assessment produces one of:

```text
plausible_in_scope
uncertain
likely_out_of_scope
```

The result is machine-generated processing evidence.

It does not modify:

- the Source Manifest;
- the registered Source Role;
- Engineering Authority;
- Approved Inputs;
- the engineering model.

`plausible_in_scope` permits the source to continue toward project-level reconciliation.

`uncertain` and `likely_out_of_scope` prevent the source from entering project-level engineering reconciliation until a Human Processing Decision resolves its treatment.

The source remains registered and auditable in every case.

Existing source dispositions defined by ADR-012 remain authoritative for operational source treatment:

```text
in_scope
context_only
out_of_scope
```

The Project Fit Assessment may support such a Human decision but does not replace it.

---

### 4. Introduce Project-Level Cross-Source Semantic Reconciliation

Only sources admitted to project-level engineering processing participate in Cross-Source Semantic Reconciliation.

This boundary operates after sufficiently stable source-local Engineering Subjects and Persona interpretation/consensus are available.

Its purpose is not to determine model placement.

It answers:

> How are engineering statements from different sources semantically related?

Supported semantic relations are:

```text
equivalent
complementary
potential_conflict
distinct
uncertain
```

#### Equivalent

Two or more source contributions express substantially the same engineering meaning.

They may be presented as one project-level review subject while retaining all individual source evidence.

#### Complementary

Contributions relate to the same engineering concern but provide different, non-competing information, abstraction levels, viewpoints or detail.

They shall not be collapsed into one statement merely because they overlap semantically.

#### Potential conflict

Contributions appear to make materially incompatible claims concerning the same engineering concern.

The system records the variance but does not select a winner.

#### Distinct

The contributions represent separate engineering subjects.

#### Uncertain

The semantic relationship cannot safely be determined.

Uncertain contributions remain separate and visible for Human reasoning.

---

### 5. LLM-assisted semantic comparison has no Engineering Authority

Project Fit Assessment and Cross-Source Semantic Reconciliation may use bounded LLM calls.

LLMs may propose semantic relationships and supporting reasoning evidence.

They may not authorize:

- Engineering Approval;
- source priority;
- automatic truth selection;
- automatic supersession;
- model modification;
- semantic merging without valid evidence.

The following never constitute Engineering Authority:

```text
LLM confidence
number of agreeing sources
source order
source age
source type
Persona consensus
semantic equivalence
```

Deterministic validation shall verify provenance, referenced identities and reconciliation consistency.

Missing, malformed or uncertain semantic evidence shall fail closed against automatic merge authority.

---

### 6. Preserve Human Engineering Authority before project-level reconciliation

Cross-Source Semantic Reconciliation produces review evidence only and has no Engineering Authority.

The existing Human Review, Approved Input and Approved Engineering Information contracts remain the authority boundary for source-derived engineering information. Each participating engineering Source therefore retains its existing source-local path:

```text
Source / Processing Evidence
        ↓
Human Engineering Review
        ↓
Approved Input / Approved Engineering Information
```

Cross-source semantic evidence does not bypass, replace or synthesize the result of that Human Engineering Review.

After source-local Engineering Authority exists, the Multi-Source extension introduces one common project-level Human Engineering Authority boundary. At this boundary, Human reviewers reconcile the relationship between already reviewed source-local authority using the exact Cross-Source Semantic Reconciliation evidence.

The reviewer may determine that reviewed engineering information shall:

```text
remain independent
coexist as valid information concerning the same engineering concern
supersede previously accepted project-level authority
remain unresolved
```

Existing source provenance remains attached to every reviewed result and every project-level authority decision.

The common project-level Human Engineering Authority boundary is an authority boundary, not a requirement to represent all cross-source evidence inside one physical source-bound `ReviewDocument`.

---

### 6a. Project-Level Engineering Authority Reconciliation

The existing Human Review, Approved Input and Approved Engineering Information contracts remain source-bound and shall not be migrated solely to support Multi-Source processing.

Cross-source semantic evidence shall therefore not be forced into a legacy source-bound `ReviewDocument`, and no artificial primary Source shall be assigned to engineering information derived from several independent Sources.

Existing `stable_subject_key` values shall not be interpreted as project-wide cross-source semantic identity unless such continuity has been explicitly established. Equality or inequality of source-local `stable_subject_key` values alone does not establish cross-source Engineering Authority continuity.

After source-local Human Review and Approved Input / Approved Engineering Information projection, a new project-level boundary is introduced:

```text
Source-local Approved Engineering Authority
        +
Cross-Source Semantic Reconciliation Evidence
        ↓
Project Engineering Authority Reconciliation
        ↓
Explicit Human Authority Decision
        ↓
Project Engineering Authority State
```

Project Engineering Authority Reconciliation references existing immutable source-local Approved Inputs / Approved Engineering Information and the exact Cross-Source Semantic Reconciliation evidence. It does not rewrite those artifacts.

The Human reviewer may establish whether reviewed engineering information shall:

```text
remain independent
coexist as valid information concerning the same engineering concern
supersede previously accepted project-level authority
remain unresolved
```

A project-level authority concern or continuity identity may only be established through this explicit Human decision boundary. Machine-generated semantic relationships such as `equivalent`, `complementary`, or `potential_conflict` do not create that identity and do not authorize an authority transition.

Source-local Approved Input lifecycle state and project-level Engineering Authority state are separate concepts. Cross-source reconciliation may therefore determine that an otherwise valid source-local Approved Input is no longer active project-level authority without modifying or deleting its immutable source-local authority record.

Where the existing Approved Input supersession contract can represent an accepted successor without falsifying provenance or continuity, it may be reused. Where its source-local `stable_subject_key` contract is insufficient for genuine cross-source continuity, project-level authority reconciliation remains authoritative and shall not fabricate a matching `stable_subject_key`.

If Human Review determines that the accepted engineering meaning requires a newly synthesized or materially changed engineering statement that is not represented by an existing Approved Input, the reconciliation layer shall not author that statement automatically. The new statement must pass an explicit Human Engineering Review and become new reviewed Engineering Authority before it may affect the model.

The resulting Project Engineering Authority State is the authoritative input to Model Impact Reconciliation in S5.

The phrase "one common Human Engineering Review boundary" denotes a common Human-authority boundary at project level; it does not require all cross-source evidence to be represented by one physical `ReviewDocument`.

### 7. Existing reviewed Engineering Information remains authority when new sources arrive

When additional sources are registered after engineering information has already been reviewed and modeled:

```text
existing active Approved Inputs / AEI
= current Engineering Authority

new source
= new evidence / change candidate
```

A newer source does not automatically supersede existing authority.

Cross-source reconciliation may identify semantic overlap or material variance between new source-derived information and existing active reviewed engineering information.

Human Engineering Review determines whether the accepted authority shall:

```text
remain unchanged
be extended
be replaced/superseded
coexist as separately valid information
remain unresolved
```

Existing Approved Input successor and supersession mechanisms shall be reused wherever compatible.

A changed reviewed successor creates new authority; previous authority is retained immutably and may become `superseded`.

No existing reviewed evidence is rewritten.

---

### 8. Separate Engineering Authority from Model Authority

The existing engineering model is not the primary authority for deciding whether new source information is correct.

Authority order remains:

```text
Human-reviewed Engineering Information
        ↓
Approved Input / AEI
        ↓
Accepted Engineering Model
        ↓
SysML v2 Representation
```

The model represents accepted Engineering Authority.

It does not override new evidence merely because the new evidence differs from the current model.

---

### 9. Introduce Model Impact Reconciliation after Engineering Review

Only after new Engineering Authority has been established may the system compare it with the currently accepted model.

A separate Model Impact Reconciliation boundary shall evaluate the impact of accepted engineering changes on existing model elements.

It may propose:

```text
retain
extend
modify
new
supersede
unresolved
```

Potential model impact shall be derived using:

- active Engineering Authority;
- existing model traceability;
- model classification and placement information;
- existing accepted model structure.

Model Impact Reconciliation is advisory evidence.

It shall not directly mutate the accepted model.

Human Model Review remains authoritative for model changes.

This separation prevents semantically related statements on different engineering abstraction levels from being falsely treated as competing model elements during source processing.

---

### 10. Preserve explicit product evolution and Change Control

When Human Review establishes that newly processed information replaces previously accepted engineering information:

```text
old reviewed authority
        ↓
superseded by
        ↓
new reviewed authority
```

the change shall remain explicitly traceable.

Corresponding model changes shall be represented as a subsequent accepted model state rather than rewriting historical authority or historical model evidence.

The architecture therefore supports product evolution without using document age as automatic truth.

---

## Consequences

### Positive

- Genuine Multi-Source processing becomes possible without rewriting source-level Processing.
- Sources accidentally registered in the wrong Project can be detected before project-level semantic integration.
- Context-only documents obtain a clear architectural role in establishing product understanding.
- Complementary information is preserved instead of being falsely treated as conflict.
- Cross-source conflicts remain explicit and provenance-preserving.
- Existing reviewed information remains authoritative until Human-controlled change.
- Existing Approved Input supersession mechanisms can support auditable Change Control.
- Model updates become consequences of reviewed engineering change rather than direct consequences of LLM output.
- Existing Single-Source behavior remains valid.
- No migration of persisted `ProcessingArtifactReference` contracts is required.

### Negative / Cost

- Multi-Source processing requires additional LLM-assisted reasoning boundaries.
- A project-level reconciliation contract and Human Review bridge must be introduced.
- Incremental source ingestion must consider existing active Engineering Authority.
- Model impact analysis adds an additional reasoning step before model update.
- More explicit provenance and lifecycle state must be retained across boundaries.

These costs are accepted because collapsing Source Fit, semantic reconciliation, Engineering Authority and Model Change into one step would create unsafe hidden authority and unreliable model evolution.

---

## Affected Components

Existing components retained:

```text
modules/project_sources/
modules/project_processing/
modules/source_evidence/
modules/engineering_subjects/
modules/subject_interpretation/
modules/subject_consensus/
modules/human_review/
modules/review_workspace/
modules/approved_input/
Approved Engineering Information projection
Model Candidate / Placement processing
Internal Engineering Model
SysML v2 generation
SYSIDE validation
Final Human Release
```

New or extended responsibilities are expected around:

```text
project-fit assessment
project-level cross-source semantic reconciliation
multi-source Human Review projection / bridge
engineering-authority continuity
model-impact reconciliation
```

Existing immutable Project `000116` Gate-3 evidence shall not be modified or regenerated by this work.

---

## Supersedes

None.

This ADR extends, but does not supersede:

```text
ADR-012 Processing State and Artifact Organization
ADR-016 Human Review Workspace and Approved Input Promotion Architecture
ADR-025 Semantic Proposal Consolidation and Persona-Aware Consensus
ADR-026 Source-Anchored Multi-Persona Interpretation and Cross-Unit Semantic Synthesis
ADR-029 Human-Reviewed Model Placement Before Model Assembly
ADR-023 Final Model Review and Output Publication Architecture
```

Where this ADR introduces project-level behavior, the existing source-level contracts remain valid.

---

## Related Roadmap Phase

```text
BLK-002 — Cross-Source Processing Artifact Identity Collision
WP-12 Multi-Source Acceptance
30-Day Thesis Completion Track A
Multi-Source → Safe Demo
```

---

## Related Implementation

Implementation is divided into bounded, independently testable slices.

### S1 — Contextual Processing Artifact Identity

At project-wide lifecycle and aggregation boundaries:

```text
(processing_run_id, artifact_type, artifact_id)
```

becomes effective identity.

No persisted Processing Artifact schema change.

### S2 — Project Fit / Source Admissibility

Introduce the Project Fit Assessment and its Human escalation boundary.

### S3 — Project-Level Cross-Source Semantic Reconciliation

Introduce provenance-preserving:

```text
equivalent
complementary
potential_conflict
distinct
uncertain
```

classification.

### S4 — Project-Level Engineering Authority Reconciliation

Connect source-local Approved Input / AEI authority and Cross-Source Semantic Reconciliation evidence to the explicit project-level Human Authority Decision defined by Sections 6 and 6a, without migrating or falsifying source-bound Review / Approved Input / AEI provenance.

### S5 — Model Impact Reconciliation

Compare newly accepted Engineering Authority with the existing accepted model and produce reviewable model-impact proposals.

Each implementation slice requires focused tests.

Before final acceptance:

```text
focused slice tests
full repository regression
git diff --check
real heterogeneous Multi-Source E2E
downstream deterministic SysML v2 generation
real SYSIDE validation
Final Human Release
SSOT update
```


\newpage


# ADR-033 — Concern-Centric Project Reconciliation and Coherent Model Handoff


- **Status:** Accepted
- **Date:** 2026-08-31
- **Decision scope:** BLK-002 project-level multi-source reconciliation
- **Refines:** ADR-032
- **Replaces:** pair-centric S3 orchestration introduced experimentally by I2D.4
- **Does not replace:** source-local Human Review / Approved Input authority, S4 Human Project Authority, S5 Model Impact authority boundaries, or Human Final Model Review

## Context

BLK-002 must reconcile multiple independent Engineering Sources without collapsing
their provenance or allowing an LLM to become Engineering Authority.

The first S3 implementation asked one LLM call to reason across all supplied
Subjects and to construct arbitrary cross-source relations. A later experiment
constrained each call to one Source pair. Both approaches remained relation-centric:
the LLM still had to choose which Subjects should be compared.

Real E2E execution showed that this is the wrong abstraction. The engineering
question is not primarily "which pair of Subjects relates to which other pair?".
It is:

1. Do the Sources belong to the same Project/System context?
2. Which source-local Subjects concern the same engineering topic?
3. For each such topic, are the statements compatible, complementary,
   conflicting, distinct, or uncertain?
4. Which engineering meaning is accepted by Human Project Authority?
5. How does the complete accepted project-level meaning become one coherent
   Model Candidate Set?

The useful analogy is a collection of stickers:
Project Fit determines whether a sticker belongs to the same album/tournament;
semantic indexing groups stickers by the number/topic they concern; only then are
all stickers of that topic assessed together.

## Decision

Project reconciliation is **concern-centric, not pair-centric**.

### S2 — Project Fit

S2 remains unchanged.

Its question is whether each Engineering Source is plausibly in scope for the
current Project/System. Only Sources with an admitted Project Fit gate state may
enter project-level semantic reconciliation.

S2 answers the "same tournament/album?" question. It does not establish semantic
identity between Subjects and does not create Engineering Authority.

### S3A — Global Project Semantic Index

S3A receives all admitted, source-bound Project semantic Subjects in one bounded
semantic-indexing task.

Its single task is:

> Group supplied Subjects by shared engineering concern.

S3A does **not**:
- decide which Source is correct;
- decide conflict or compatibility;
- merge Subjects;
- create model elements;
- create Engineering Authority;
- rank Sources;
- create persisted Case identity.

The LLM sees only transient opaque Subject transport identifiers such as
`SUBJ-0001`. Real `project_subject:...` identity remains application-controlled.

The deterministic application layer validates:
- every supplied Subject appears exactly once;
- no unknown Subject reference is accepted;
- no Subject appears in more than one group;
- no Subject is silently dropped;
- a multi-member Case contains Subjects from at least two different Sources;
- Subjects without a cross-source counterpart become Singleton Cases.

After successful validation, application code deterministically orders Cases and
assigns `CASE-000001`, `CASE-000002`, ... . LLM-generated labels are descriptive
only and never Case identity.

Case identity is fingerprint-bound to:
- Project ID;
- exact global S3 input fingerprint;
- sorted real member `project_subject:...` references.

### S3B — Reconciliation Case Assessment

S3B processes one non-singleton Reconciliation Case per LLM call.

Its single task is:

> Assess how all statements belonging to this one engineering concern fit together.

Allowed outcomes are:
- `equivalent`
- `complementary`
- `potential_conflict`
- `distinct`
- `uncertain`

`unique` is reserved for Singleton Cases and is derived deterministically without
an LLM call.

S3B assesses the Case **as a whole**. It does not expand the Case into a graph of
pairwise relations.

For example, if three Sources state viewer limits of 2, 2 and 5, the result is one
Case with two claim groups and `potential_conflict`, not three pairwise relations.

Claim groups are non-authoritative evidence describing materially different
variants within one Case. For `potential_conflict`, claim groups must partition
all Case members into at least two explicit competing groups.

S3B remains relationship/meaning evidence only. Human Project Authority remains
required for every non-singleton Case.

### Deterministic Project Reconciliation Summary

After every Case has an assessment, application code derives the global Project
Reconciliation summary without an additional LLM call.

The summary may state:
- number of Cases by outcome;
- whether potential cross-source conflicts were detected;
- whether uncertainty exists;
- whether regrouping is required because a Case was assessed as `distinct`;
- whether Human Project Authority is required.

The system must say "no cross-source conflict detected" rather than claiming the
Project is objectively contradiction-free.

### S4 — Human Project Authority

S4 operates on Reconciliation Cases, not synthetic pairwise relations.

S4 may establish that source-bound engineering meaning:
- remains independently active;
- coexists with other source-bound meaning;
- is superseded by explicitly selected source-bound meaning;
- remains unresolved.

No synthetic merged Approved Engineering Input is created.

Singleton Cases retain their existing source-local authority and do not require
an additional semantic LLM assessment.

### Project Engineering State and coherent downstream model handoff

The end product of project reconciliation is **not a list of isolated Cases**.

Resolved Cases, unique retained Subjects, exact source-local authority bindings,
and explicit Human Project decisions together form the Project Engineering State.

That state is handed to the downstream model derivation pipeline as one coherent
project-level engineering handoff.

The downstream model derivation outcome is one coherent **Model Candidate Set**
which may contain, together:
- model elements;
- relationships;
- properties / attributes;
- constraints;
- allocations / usages;
- other required structural model constructs.

Cases are not written into the model one by one.

S3/S4 establish approved engineering meaning. The model-candidate / Phase-H layer
decides how that meaning is represented structurally in SysML v2.

The complete Model Candidate Set is then compared with the accepted model state,
processed through Model Impact / model proposal logic, reviewed by Human Model
Authority, and only then released to the model.

### LLM design principle

For project reconciliation:

> LLMs perform bounded semantic judgments. Deterministic application logic owns
> identity, coverage, orchestration, aggregation, and authority boundaries.

And:

> A singular LLM task does not necessarily mean a tiny input. S3A may see the
> complete bounded Subject set because its task is only semantic indexing. S3B
> sees one Case because its task is only Case assessment.

If the bounded S3A input is ever too large, the system must fail closed until an
explicit indexing reduction/hierarchy architecture is accepted. It must not
silently chunk and infer global grouping equivalence.

## Consequences

### Positive

- Reconciliation matches the actual engineering question rather than creating
  O(n²) relation evidence.
- Conflicts are represented once per engineering concern.
- Human review sees coherent topics and variants rather than redundant pairwise
  edges.
- Source provenance remains intact.
- The LLM no longer controls identity or pair construction.
- The downstream model receives one coherent approved project meaning rather
  than Case-by-Case model mutations.

### Trade-offs

- S3A is a global semantic indexing task and therefore requires an explicit
  bounded-input contract.
- Existing S4 relation-oriented implementation must be adapted to Case-oriented
  authority in a later slice.
- Existing pairwise I2D.4 runtime code must be removed/replaced before the next
  live Project Reconciliation execution.
- Persisted project-reconciliation schema changes, if required, must be additive
  and separately accepted.

## Migration / implementation plan

1. **I2D.5A** — freeze ADR-033 and additive in-memory Case contracts.
2. **I2D.5B** — implement S3A global semantic indexing with transient Subject refs.
3. **I2D.5C** — implement S3B one-Case assessment and deterministic project summary.
4. **I2D.5D** — replace I2D.4 runtime orchestration and define additive persistence.
5. **I2D.5E** — adapt S4 Human Project Authority from relation decisions to Case decisions.
6. Reconnect S5 / I1 project-level handoff so all resolved/unique meaning enters
   one coherent Model Candidate Set.
7. Resume Project 308131 E2E only after the pairwise runtime path has been removed
   and the Case path is green.

No immutable accepted evidence is rewritten by this ADR.


\newpage


# ADR-034 — Source Provenance Does Not Constrain Concern Grouping


**Status:** Accepted refinement of ADR-033
**Scope:** BLK-002 / I2D.5D4

## Context

ADR-033 replaced pair-centric cross-source reconciliation with concern-centric
Project Reconciliation Cases. During the first live Project 308131 run, S3A
returned a multi-member semantic group whose Subjects all originated from one
Source. The existing Case contract rejected that group because a multi-member
Case was required to span at least two Sources.

That restriction carries forward source-pair semantics into a concern-centric
architecture. It is especially inappropriate for well-separated engineering
documents, where several Subjects from one document may legitimately address
the same engineering concern.

The source of a Subject remains important for provenance, traceability, source-
local Human Authority, and later project-level authority. It is not semantic
evidence that determines whether Subjects concern the same topic.

## Decision

Reconciliation Case membership is determined solely by shared engineering
concern.

`source_id` is provenance only and SHALL NOT constrain semantic concern
grouping.

Therefore:

- A one-member group becomes a Singleton Case.
- A multi-member Case MAY contain Subjects from one Source or multiple Sources.
- `unique` is defined by one Subject, not by one Source.
- Every real source binding remains preserved on every Subject and Case.
- Same-source multi-member Cases remain non-singleton Cases and therefore
  receive the normal S3B bounded semantic assessment.
- No automatic cross-group merging, source balancing, or source-based splitting
  is permitted.
- Exact Subject coverage, no duplicates, known references, deterministic Case
  identity, and immutable provenance remain fail-closed constraints.

## Rationale

The concern-centric architecture separates semantics from provenance:

> Semantic meaning decides grouping. Source provenance records where evidence
> came from.

This is analogous to grouping Panini stickers by motif regardless of the kiosk
where a packet was purchased. The purchase location is traceability metadata,
not part of the motif identity.

## Consequences

The former invariant

`multi-member Case => at least two Sources`

is removed from the S3A Case contract and persistence validation.

Case identity remains unchanged and continues to bind:

- project identity,
- exact semantic input fingerprint,
- exact sorted Subject references.

No persisted PRC cycle schema changes. Existing semantic-index schema 1.0.0
remains readable, and provenance-bearing semantic-index schema 1.1.0 remains
the live S3A format.

Human Project Authority remains Case-aware and is still a later gate. This ADR
does not create authority and does not alter source-local Approved Engineering
Input.


\newpage


# ADR-035 — Safe-Demo Review Compression and Final Review Routing


## Status

Accepted for v0.5.0 feature implementation.
Integration into `main` remains gated by Safe-Demo acceptance.

## Date

2026-09-10

## Context

The v0.4.0 application exposes several technically distinct authority and
transformation steps in one long Model Proposal / Internal Model working surface.

A 2026-09-10 informal professor demo showed that the late-stage workflow is
functionally usable but unnecessarily time-consuming in Focused View.

The most visible problem was Target-Model Formulation: deterministic,
single-candidate formulation items were presented as many individual Human review
cards with individual rationale entry.

At the same time, the system already has a separate top-level Phase-L
`Final Model Review` workspace after generated SysML v2 and validation.

The earlier whole-assembly gate inside `app/model_final_review_ui.py` was also
user-facing as `Final Model Review`, creating naming ambiguity already tracked by
`OBS-032`.

ADR-024 requires the engineer-facing UI to be task-oriented, simple by default and
fully traceable underneath. This ADR refines that UX direction for v0.5.0 without
redesigning the underlying authority schemas.

## Decision

### D1 — Keep the real Phase-L Final Model Review

The top-level Final Model Review after generated SysML v2 and validation remains the
final whole-model engineering review before release/publication authority.

It is not removed or replaced.

### D2 — Rename the pre-generation assembly gate

The earlier whole-assembly review is user-facing as:

```text
Model Assembly Review
```

It remains responsible for:

- reviewing the assembled model;
- resolving remaining target Relationship representation where necessary;
- approving materialization of the authority-backed Internal Model.

### D3 — Compress deterministic Target-Model Formulation in Focused View

The bounded current Target-Model Formulation bridge produces one candidate per
review item.

Focused View shall not require an engineer to open and accept every deterministic
single-candidate mapping separately.

One action may batch-confirm all pending single-candidate items that are not:

```text
unresolved_human_review
```

Existing persisted decisions are resumed.

`unresolved_human_review` remains explicit Human attention and blocks authority
finalization.

`intentionally_not_materialized` may be included in the batch only when the Focused
UI explicitly tells the engineer that the approved engineering information remains
authoritative but is not formally materialized under the current bounded notation.

### D4 — Preserve existing formulation authority contracts for v0.5.0

v0.5.0 does not remove TFA/TFD persistence or redesign authority schemas.

Batch confirmation delegates to the existing exact write service and persists one
exact decision per item with the same immutable binding and fingerprints.

This is an interaction compression, not hidden weakening of authority.

A future architecture revision may reassess whether deterministic mappings require
Human authority at all, but that is outside the Safe-Demo slice.

### D5 — Batch only clean LLM Model Quality proposals

Model Quality refinement remains LLM-assisted and therefore retains Human authority.

Focused View may offer:

```text
Accept all clean refinements
```

only for pending proposals satisfying all of:

```text
meaning_preserved == true
unsupported_information_added == false
requires_human_attention == false
```

All other proposals remain individual Human review.

Modify and reject remain explicit rationale-backed actions.

Technical View continues to expose the complete proposal and decision evidence.

### D6 — Route directly to the real Final Model Review

After authority-backed SysML v2 is generated, Focused View presents a compact
artifact summary and an explicit:

```text
Go to Final Model Review
```

action.

That action routes to the existing top-level Final Model Review workspace.

Generated code remains fully inspectable in Technical View and again in Final Model
Review.

### D7 — Safe-Demo integration gate

v0.4.0 remains the functional fallback.

v0.5.0 may be merged into `main` only after:

```text
focused tests
→ appropriate broader regression
→ complete repository regression
→ fresh E2E project
→ generated SysML v2
→ SYSIDE validation
→ Phase-L Final Model Review
→ release/publication path
→ exact Safe-Demo rehearsal
→ explicit Human acceptance
```

No new unrelated feature work is bundled into this slice.

## Consequences

Positive:

- substantially fewer low-value clicks in the demo and normal Focused workflow;
- clearer separation of deterministic transformation and Human engineering review;
- clear naming between Model Assembly Review and Phase-L Final Model Review;
- Human attention remains concentrated on ambiguity, LLM-risk and whole-model
  authority;
- existing traceability and authority contracts remain intact;
- v0.4.0 rollback remains available.

Trade-offs:

- the current persisted authority model still records item-level formulation
  decisions even though the UI performs one batch confirmation;
- batch acceptance can persist several decisions before a later item fails, so the
  operation is resumable rather than transactionally all-or-nothing;
- the long-term question whether deterministic mappings need Human authority is
  deliberately deferred;
- real-data robustness and synthesis-relevance findings are not solved by this ADR.
