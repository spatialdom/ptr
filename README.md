# Parcel Truth Records (PTR)

**Status:** Published v0.1 baseline (pre-stable)  
**PTR format version:** `0.1`  
**Repository release:** `v0.1.0`  
**File extension:** `.ptr`  
**Author / originator:** Dominic C. Fargas Jr. — ORCID: https://orcid.org/0009-0000-4088-7476  
**Maintainer:** Spatialdom

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22641884.svg)](https://doi.org/10.5281/zenodo.22641884)

Parcel Truth Records (PTR) is an open, human-readable format for representing **one land parcel from its ordered bearing-distance boundary description**.

PTR is parcel-native and survey-native. It is designed for the measurements commonly found in land titles, survey records, and technical descriptions while remaining small enough to read and edit in a basic text editor.

> GeoJSON says: “Here is a geometry.”  
> PTR says: “Here is a parcel, and this is how its boundary was described.”

The `.ptr` file is deliberately only the foundation. PTR-aware software can reconstruct geometry, validate a parcel description, compute metrics, compare parcels, georeference them when external control is available, and connect the resulting parcel to external contextual datasets.

---

## Why PTR?

General GIS formats are intentionally broad. They can describe parcels, roads, rivers, buildings, points, and many other geographic objects.

PTR is intentionally narrower: **it represents a parcel as a parcel**.

That allows software to provide parcel-native behavior while keeping the underlying record portable and application-independent.

Typical PTR-aware workflows may include:

- plotting a technical description;
- checking closure and misclosure;
- computing area and perimeter;
- validating and normalizing bearing-distance courses;
- comparing parcel descriptions;
- converting derived parcel geometry to conventional GIS formats;
- working with many parcels through PTRC; and
- linking parcels to external land, urban, environmental, infrastructure, or market information.

PTR itself should remain simple. Ownership, taxation, zoning, hazards, buildings, valuation, imagery, infrastructure, and other contextual information are **not automatically part of PTR** merely because they relate to a parcel.

---

## PTR v0.1 data model

PTR v0.1 uses UTF-8 JSON.

### Required fields

| Field | Type | Meaning |
|---|---|---|
| `ptr_version` | string | PTR format version. For this specification: `"0.1"`. |
| `lines` | array | Ordered parcel boundary courses. Each course is `[bearing, distance]`. |

### Optional fields

| Field | Type | Meaning |
|---|---|---|
| `name` | string | Human-readable parcel name, such as `Lot 2173` or `Lot 6 Block 72`. |
| `record_id` | string | Optional application/database identifier; not required to define the parcel. |
| `tie_point` | string | Documentary reference to the tie point used by the source description. |
| `tie_line` | array | Bearing-distance course from the tie point to Point 1 of the parcel. |
| `declared_area` | number | Documentary parcel area in square metres. |

### Example

```json
{
  "ptr_version": "0.1",
  "name": "Lot 2173",
  "tie_point": "BLLM No. 1, Cad-123",
  "tie_line": ["S11-44W", 2351.00],
  "lines": [
    ["N", 100.00],
    ["E", 80.00],
    ["S", 100.00],
    ["W", 80.00]
  ],
  "declared_area": 8000.00
}
```

---

## Bearings and distances

The canonical course representation is:

```text
[bearing, distance]
```

Example:

```json
["N68-28E", 25.40]
```

Canonical bearings are ASCII-safe. Examples include:

```text
N
E
S
W
N68-28E
S11-44W
N68-28-30E
```

Applications may display:

```text
N68-28E
```

as:

```text
N 68°28' E
```

The normative bearing grammar and normalization behavior are defined in [`spec/ptr-v0.1.md`](spec/ptr-v0.1.md). Non-canonical but unambiguous human input may be normalized by software before serialization; ambiguous input must be rejected rather than guessed.

All distances are expressed in **metres**. `declared_area` is expressed in **square metres**.

---

## Boundary order and closure

Boundary courses are stored in parcel order. Point and line numbering are implicit from array order.

The PTR v0.1 convention is:

- the first course starts at Point 1;
- points proceed around the parcel in order;
- clockwise order is the canonical writing convention; and
- the final course is the documentary course intended to return to Point 1.

A technically recorded parcel may still have numerical misclosure because of source precision, transcription, rounding, or documentary inconsistency. PTR preserves the documentary courses. Software must not silently rewrite or force-adjust the stored record simply to close the geometry.

Closure, self-intersection, winding, area differences, and similar geometry checks are **derived QA findings**.

---

## Documentary truth vs. derived computation

PTR stores the documentary parcel description.

Typical stored documentary values are:

- parcel name or record identity when supplied;
- tie point and tie line;
- ordered boundary courses; and
- declared documentary area.

Typical derived values include:

- vertices;
- geometry;
- computed area;
- perimeter;
- closure and misclosure;
- centroid;
- bounding box; and
- other geometric or quality diagnostics.

Derived values belong to PTR Core or consuming applications. They are not authoritative PTR v0.1 fields.

---

## Context stays external

PTR v0.1 intentionally does **not** attempt to become a complete cadastral or land-administration database.

Information such as the following normally remains outside `.ptr`:

- owners or taxpayers;
- tax declarations and assessments;
- title or transaction history;
- zoning and land use;
- hazard exposure;
- buildings and permits;
- imagery;
- market valuation;
- roads and infrastructure; and
- application workflow state.

External systems can relate these datasets to a PTR-derived parcel through spatial joins, identifiers, temporal relationships, network relationships, or other application-level mechanisms.

This is a central design principle: **contextual information is not part of PTR merely because it relates to a parcel.**

---

## Validation model

PTR v0.1 distinguishes file conformance from parcel-quality analysis:

1. **Serialization** — valid UTF-8 JSON.
2. **Structural** — required fields, types, course shapes, and version.
3. **Semantic** — valid canonical values and field relationships.
4. **Geometric QA** — derived findings such as closure, self-intersection, orientation, and area discrepancy.

Serialization, structural, or semantic failures make a record non-conforming. Geometric QA findings do not automatically make an otherwise conforming documentary PTR invalid.

The shared fixture expectations are documented in [`tests/README.md`](tests/README.md).

---

## Specification and schema

The normative specification is:

- [`spec/ptr-v0.1.md`](spec/ptr-v0.1.md)

The machine-readable schema is:

- [`schema/ptr-v0.1.schema.json`](schema/ptr-v0.1.schema.json)

The schema uses JSON Schema Draft 2020-12. It validates structural and expressible semantic requirements but intentionally does not perform geometric reconstruction or QA.

Examples and reusable conformance fixtures are provided under:

```text
examples/
tests/
```

---

## PTR ecosystem

PTR is intended to be the common parcel foundation for a broader ecosystem:

```text
Land title / technical description
            ↓
           .PTR
            ↓
         PTR Core
            ↓
  derived parcel object
            ↓
        applications
            ↓
 contextual data / decisions
```

Related components may include:

- **PTR Core** — parsing, normalization, validation, computation, comparison, transformation, topology, and export;
- **PTRC** — PTR Collection for working with many parcel records;
- **Parcel Plotter** — lightweight plotting and inspection;
- **PTR Studio** — future professional parcel workspace;
- **QGIS integrations** — parcel-native workflows inside GIS; and
- **institutional systems** such as land-administration and property applications.

The standard stays deliberately small so that the software above it can evolve independently.

---

## Release and versioning

PTR format versions use the `major.minor` value stored in `ptr_version`. Repository releases use semantic-style tags such as `v0.1.0`.

The first published repository baseline is:

**Parcel Truth Records (PTR) v0.1.0 — 7 September 2026**

PTR v0.1 is intentionally pre-stable and is being published so it can be implemented, tested, cited, and scrutinized. The meaning of a released `0.1` record should not be silently changed. Future incompatible format changes require a new PTR format version.

See [`CHANGELOG.md`](CHANGELOG.md).

---

## Cite PTR

Citation metadata is provided in [`CITATION.cff`](CITATION.cff).

Preferred author attribution:

**Dominic C. Fargas Jr.**  
ORCID: https://orcid.org/0009-0000-4088-7476

Preferred release citation:

**Dominic C. Fargas Jr. (2026). Parcel Truth Records v0.1 Specification. Zenodo. https://doi.org/10.5281/zenodo.22641884**

Version DOI: https://doi.org/10.5281/zenodo.22641884  
Concept DOI for all PTR releases: https://doi.org/10.5281/zenodo.22641883

Cite the version DOI for work that depends on PTR v0.1.0. Use the concept DOI only when intentionally referring to the latest archived PTR release.

---

## License and implementation freedom

The PTR specification, schemas, examples, tests, and documentation are licensed under **CC BY 4.0**. See [`LICENSE.md`](LICENSE.md).

This allows sharing, adaptation, implementation, and commercial reuse subject to the attribution requirements of CC BY 4.0.

Third parties may accurately state that their products support or implement PTR. The license does not grant rights to use Spatialdom branding in a way that implies certification, sponsorship, endorsement, or official approval.

The open standard is intentionally separate from implementation licensing. **PTR Core** and other executable software may use their own software licenses.

---

## Governance and contributions

PTR is initiated by **Dominic C. Fargas Jr.** and maintained through **Spatialdom**.

Change proposals should be opened as GitHub issues and reviewed against the compatibility and scope rules in the specification and [`CONTRIBUTING.md`](CONTRIBUTING.md).

The long-term goal is an independently implementable parcel standard, not a format that only Spatialdom software can understand.

---

## Important limitation

PTR is a technical parcel-record format. A `.ptr` file or PTR-derived computation does **not** by itself establish legal ownership, official boundary location, title validity, cadastral authority, tax liability, or an official survey determination.

Where law or professional practice requires an authoritative survey, cadastral record, title, or licensed professional determination, PTR does not replace it.
