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
