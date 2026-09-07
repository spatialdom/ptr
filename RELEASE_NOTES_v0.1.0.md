# Parcel Truth Records (PTR) v0.1.0

**Release date:** 7 September 2026  
**PTR format version:** `0.1`  
**Author:** Dominic C. Fargas Jr.  
**ORCID:** https://orcid.org/0009-0000-4088-7476  
**Maintainer:** Spatialdom

This is the first published baseline release of **Parcel Truth Records (PTR)**, an open, human-readable format for representing one land parcel from its ordered bearing-distance boundary description.

PTR v0.1 is intentionally small and pre-stable. It establishes a versioned foundation that can now be independently implemented, tested, cited, and scrutinized before future stable versions.

## What v0.1 defines

- one `.ptr` file represents one parcel;
- UTF-8 JSON serialization;
- required `ptr_version` and `lines` fields;
- optional `name`, `record_id`, `tie_point`, `tie_line`, and `declared_area` fields;
- canonical `[bearing, distance]` courses;
- ASCII-safe cardinal and quadrant bearing notation;
- metre distances and square-metre declared area;
- implicit line and point numbering from ordered courses;
- Point 1 convention for tie-line termination;
- separation of documentary values from derived geometry and metrics;
- layered validation: serialization, structural, semantic, and geometric QA; and
- explicit scope boundaries keeping ownership, taxation, zoning, hazards, buildings, valuation, imagery, infrastructure, workflows, and other contextual data outside the core PTR record.

## Included in this release

- normative specification: `spec/ptr-v0.1.md`;
- JSON Schema Draft 2020-12 schema: `schema/ptr-v0.1.schema.json`;
- minimal and complete `.ptr` examples;
- reusable conformance fixtures for valid, invalid, ambiguous, bearing, tie-point, closure, and edge cases;
- contribution and versioning guidance;
- `CITATION.cff` with author ORCID metadata; and
- CC BY 4.0 licensing for the standard materials.

## License

The PTR specification, schemas, examples, tests, and documentation are licensed under **Creative Commons Attribution 4.0 International (CC BY 4.0)**.

Third parties may implement and commercially use PTR subject to the license terms. Descriptive statements that software supports PTR are permitted; the license does not imply Spatialdom certification, endorsement, or sponsorship.

## Citation

Preferred release citation:

**Dominic C. Fargas Jr. (2026). Parcel Truth Records v0.1 Specification. Zenodo. https://doi.org/10.5281/zenodo.22641884**

Version DOI: https://doi.org/10.5281/zenodo.22641884  
Concept DOI for all PTR releases: https://doi.org/10.5281/zenodo.22641883

Cite the version DOI for work that depends on PTR v0.1.0.

## Important limitation

PTR is a technical parcel-record format. A conforming `.ptr` record does not by itself establish legal ownership, official boundary location, title validity, cadastral authority, tax liability, or an official survey determination.
