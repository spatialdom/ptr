# PTR v0.1 Release Checklist

Use this checklist before publishing the first public `v0.1.0` release.

## Specification

- [ ] Confirm `spec/ptr-v0.1.md` is internally consistent.
- [ ] Confirm all normative `MUST`, `SHOULD`, and `MAY` requirements are intentional.
- [ ] Confirm PTR v0.1 does not imply legal authority or replace professional survey, cadastral, title, or land-administration determinations.
- [ ] Confirm future v1.0 compatibility path is clear.

## Schema And Fixtures

- [ ] Validate `schema/ptr-v0.1.schema.json` with a Draft 2020-12 JSON Schema implementation.
- [ ] Confirm valid examples pass the schema.
- [ ] Confirm invalid examples fail at the expected validation layer.
- [ ] Confirm geometric QA fixtures remain structurally and semantically valid.

## Repository Metadata

- [ ] Confirm `LICENSE.md` is present and accurate.
- [ ] Confirm `CONTRIBUTING.md` describes the change-proposal process.
- [ ] Confirm `CHANGELOG.md` has a `v0.1.0` entry.
- [ ] Confirm `CITATION.cff` matches the final release tag, date, and DOI if one is minted.
- [ ] Confirm copyright and maintainer information are current.

## Release

- [ ] Create the release tag `v0.1.0`.
- [ ] Publish a GitHub release named `PTR v0.1.0`.
- [ ] Archive the release with Zenodo or another DOI provider if a DOI is desired.
- [ ] Update `CITATION.cff` and the GitHub release notes with the DOI.
- [ ] Verify that public links to the specification, schema, examples, and tests resolve.
