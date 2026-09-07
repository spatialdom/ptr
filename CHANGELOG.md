# Changelog

All notable repository releases are documented in this file.

Release entries include the repository tag, release date, PTR format version, normative specification changes, schema changes, example/test changes, and compatibility notes where relevant.

## Unreleased

No unreleased normative changes.

## v0.1.0 — 2026-09-07

**PTR format version:** `0.1`

Initial public baseline release of Parcel Truth Records (PTR) v0.1.

### Added

- Normative PTR v0.1 specification.
- Required fields: `ptr_version` and `lines`.
- Optional fields: `name`, `record_id`, `tie_point`, `tie_line`, and `declared_area`.
- Canonical `[bearing, distance]` course representation.
- ASCII-safe cardinal and quadrant bearing grammar, including optional nonzero seconds.
- Input-normalization guidance that rejects ambiguous bearings rather than guessing.
- Metre and square-metre unit rules and JSON numeric serialization guidance.
- Boundary-order, point-numbering, closure, and geometric-QA semantics.
- Tie-point and tie-line semantics, including the convention that the tie line terminates at Point 1.
- Explicit separation between documentary PTR values and derived computational values.
- Explicit scope boundaries separating parcel description from ownership, taxation, zoning, hazards, buildings, valuation, imagery, infrastructure, workflow state, and other contextual data.
- Validation categories for serialization, structural conformance, semantic conformance, and geometric QA.
- Draft 2020-12 JSON Schema for PTR v0.1.
- Minimal and complete examples.
- Public conformance fixtures for valid, invalid, ambiguous, bearing, tie-point, closure, and edge cases.
- Contribution, licensing, citation, and release-governance materials.

### Compatibility

This is the first published PTR baseline. There are no earlier released PTR versions with which compatibility is required.

PTR v0.1 is intentionally pre-stable. Future incompatible format changes will use a new PTR format version rather than silently changing the meaning of conforming `0.1` records.
