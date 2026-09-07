# PTR v0.1 Release Checklist

**Release:** `v0.1.0`  
**Published:** 2026-09-07  
**Status:** GitHub release complete; Zenodo DOI recorded.

## Specification

- [x] Confirm `spec/ptr-v0.1.md` is internally consistent.
- [x] Confirm all normative `MUST`, `SHOULD`, and `MAY` requirements are intentional.
- [x] Confirm PTR v0.1 does not imply legal authority or replace professional survey, cadastral, title, or land-administration determinations.
- [x] Confirm future v1.0 compatibility path is clear.

## Schema and Fixtures

- [x] Validate `schema/ptr-v0.1.schema.json` as JSON Schema Draft 2020-12.
- [x] Confirm valid examples and conformance fixtures pass the schema.
- [x] Confirm invalid fixtures fail at the expected serialization/schema-validation layer.
- [x] Confirm geometric QA fixtures remain structurally and semantically conforming.

The release workflow validated the schema, conforming fixtures, non-conforming fixtures, citation metadata, ORCID checksum, and release-version references before publishing the GitHub release.

## Repository Metadata

- [x] Confirm `LICENSE.md` is present and accurate.
- [x] Confirm `CONTRIBUTING.md` describes the change-proposal process.
- [x] Confirm `CHANGELOG.md` has a `v0.1.0` entry.
- [x] Confirm `CITATION.cff` matches the release tag, release date, and Zenodo DOI.
- [x] Confirm author, ORCID, copyright, and maintainer information are current.
- [x] Clarify that CC BY 4.0 does not imply Spatialdom endorsement or certification of third-party implementations.

## GitHub Release

- [x] Create release tag `v0.1.0`.
- [x] Publish GitHub release **Parcel Truth Records (PTR) v0.1.0**.
- [x] Verify the release is not a draft.
- [x] Verify public links to the specification, schema, examples, tests, license, and citation metadata resolve.

Release: https://github.com/spatialdom/ptr/releases/tag/v0.1.0

## Zenodo / DOI

- [x] Connect or sync the public `spatialdom/ptr` repository with Zenodo.
- [x] Archive GitHub release `v0.1.0` in Zenodo.
- [x] Record the Zenodo version DOI.
- [x] Update `CITATION.cff` with the DOI and final preferred citation.
- [x] Update README and release notes with the DOI.
- [x] Verify the DOI resolves to the archived `v0.1.0` record.

Version DOI: https://doi.org/10.5281/zenodo.22641884  
Concept DOI: https://doi.org/10.5281/zenodo.22641883  
Zenodo record: https://zenodo.org/records/22641884
