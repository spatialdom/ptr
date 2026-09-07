# Contributing

PTR is maintained as a small, independently implementable parcel-record standard. Changes should keep the `.ptr` record focused on documentary parcel descriptions and avoid adding contextual land-administration fields unless they are necessary for interoperable parcel-description exchange.

## Change Proposals

Use GitHub issues for proposed changes. A proposal should describe:

- the problem or ambiguity;
- the proposed normative behavior;
- examples of valid and invalid records, if relevant;
- compatibility impact for existing PTR v0.1 records; and
- whether the change affects the specification, schema, examples, or tests.

Normative changes should be implemented by pull request and include updates to `spec/`, `schema/`, `examples/`, and `tests/` where applicable.

## Compatibility Review

Before merging a normative change, maintainers should classify it as:

- clarification or erratum;
- non-breaking extension;
- breaking format change; or
- experimental work outside the current stable format.

Breaking format changes should not be merged into a released PTR version without defining a new major format version.

## Maintainer

PTR is initiated and maintained by Spatialdom.
