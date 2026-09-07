# Parcel Truth Records (PTR)

**Status:** Draft specification, v0.1  
**Maintainer:** Spatialdom  
**File extension:** `.ptr`

Parcel Truth Records (PTR) is an open, human-readable format for representing **one land parcel from its ordered bearing-distance boundary description**.

PTR is designed for parcel work first. It takes inspiration from simple interchange formats such as GeoJSON, but it is intentionally contextualized for land parcels and for the measurements commonly found in land titles, survey records, and technical descriptions.

> GeoJSON says: “Here is a geometry.”  
> PTR says: “Here is a parcel, and this is how its boundary was described.”

The `.ptr` file is only the foundation. PTR-aware software can reconstruct geometry, validate closure, compute parcel metrics, compare parcels, georeference descriptions, and connect parcels to external urban and land-information datasets.

---

## Why PTR?

Common GIS formats are intentionally generic. They can represent parcels, roads, rivers, buildings, points, and many other geographic objects.

PTR is deliberately narrower.

It represents a **parcel as a parcel**.

That allows software to attach parcel-native behavior to the data while keeping the underlying file small, portable, readable, and application-independent.

PTR is intended to support workflows such as:

- plotting a technical description;
- checking closure;
- computing area and perimeter;
- validating and normalizing bearing-distance courses;
- comparing parcel descriptions;
- converting a parcel into conventional GIS geometry;
- working with parcel collections through PTRC; and
- linking parcel geometry to external contextual datasets.

PTR itself should remain simple. Ownership, taxation, zoning, hazards, buildings, valuation, imagery, and other contextual information are **not automatically part of PTR** merely because they relate to a parcel. Those datasets should remain external and be linked to the parcel when needed.

---

## Design goals

PTR v0.1 is guided by the following principles:

1. **Parcel-native** — the format represents a land parcel, not an arbitrary geometry.
2. **Survey-native** — boundary courses are stored as bearings and distances rather than requiring coordinates.
3. **Human-readable** — a `.ptr` file should be understandable and editable in a basic text editor.
4. **ASCII-safe input** — canonical bearing notation should not require typing a degree symbol.
5. **Minimal** — store the documentary parcel description; derive computational properties when needed.
6. **Portable** — the file should not depend on a particular application, database, or service.
7. **Deterministic** — the same valid record should reconstruct the same relative parcel geometry.
8. **Extensible through software, not field accumulation** — advanced behavior belongs primarily in PTR Core, PTRC, and applications rather than by turning `.ptr` into a giant cadastral schema.

---

## PTR v0.1 data model

PTR v0.1 uses UTF-8 JSON.

### Required fields

| Field | Type | Meaning |
|---|---|---|
| `ptr_version` | string | PTR format version. For this draft: `"0.1"`. |
| `lines` | array | Ordered parcel boundary courses. Each course is `[bearing, distance]`. |

### Optional fields

| Field | Type | Meaning |
|---|---|---|
| `name` | string | Human-readable parcel name, such as `Lot 2173` or `Lot 6 Block 72`. |
| `record_id` | string | Optional application/database identifier. It is not required to define the parcel itself. |
| `tie_point` | string | Human-readable reference to the tie point used by the source description. |
| `tie_line` | array | Bearing-distance course from the tie point to the parcel reference point. |
| `declared_area` | number | Documentary parcel area in square metres. |

---

## Example

```json
{
  "ptr_version": "0.1",
  "name": "Lot 2173",
  "tie_point": "BLLM No. 1, Cad-123",
  "tie_line": ["S11-44W", 2351.00],
  "lines": [
    ["S04-47E", 79.70],
    ["S89-37W", 67.41],
    ["N03-07W", 27.93]
  ],
  "declared_area": 10000.00
}
```

This example illustrates the structure only; it is not intended to represent a complete real-world parcel.

---

## Boundary courses

The canonical course representation is:

```text
[bearing, distance]
```

Example:

```json
["N68-28E", 25.40]
```

### Bearings

PTR v0.1 uses an ASCII-safe canonical notation.

Examples:

```text
N
E
S
W
N68-28E
S11-44W
```

Applications may display a canonical value such as:

```text
N68-28E
```

as:

```text
N 68°28' E
```

PTR Core is expected to normalize common unambiguous input forms into the canonical representation while rejecting ambiguous input. The normative grammar and normalization rules are defined in the v0.1 specification.

### Distances

Distances are expressed in **metres**.

Human-readable decimal values are preferred. Title-style output may use 0.01 m precision, but precision and serialization rules will be defined formally in the specification.

---

## Course order and parcel closure

Boundary lines are stored in parcel order. Line and point numbering are implicit from the array order rather than repeated in every record.

The intended convention is:

- the first course starts at Point 1;
- points proceed in order around the parcel;
- boundary lines are described clockwise; and
- the final course returns to Point 1.

A parcel description may still contain numerical misclosure because of source precision, transcription, or documentary inconsistencies. PTR stores the documentary courses; closure diagnostics are derived by software.

---

## Declared vs. computed values

`declared_area` is documentary information from the source record.

A computed area is different: it is derived from the reconstructed parcel geometry.

PTR therefore does not store computed geometry or metrics as authoritative fields in v0.1.

Typical derived properties include:

- vertices;
- geometry;
- computed area;
- perimeter;
- closure and misclosure;
- centroid;
- bounding box; and
- other geometric or quality diagnostics.

These belong to PTR Core or consuming applications.

---

## PTR is not a complete land-administration database

PTR v0.1 intentionally does **not** attempt to embed every fact associated with a parcel.

For example, these normally remain outside the `.ptr` record:

- owners or taxpayers;
- tax declarations;
- market values;
- zoning classifications;
- hazard exposure;
- buildings and permits;
- imagery;
- infrastructure;
- transaction history; and
- application-specific workflow state.

Those datasets can be linked to a PTR-derived parcel through spatial joins, identifiers, network relationships, temporal relationships, or other application-level mechanisms.

This separation keeps the parcel representation stable while allowing many different systems to use it.

---

## Ecosystem

PTR is intended as the common parcel foundation for a broader ecosystem.

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
- **PTRC** — a collection format for many PTR records;
- **Parcel Plotter** — lightweight plotting and inspection;
- **PTR Studio** — future professional parcel workspace;
- **QGIS integrations** — parcel-native workflows inside GIS; and
- **institutional systems** such as land-administration and property applications.

The standard stays deliberately small so that the software above it can evolve independently.

---

## Repository scope

This repository is intended to contain the **PTR standard**, not the full application stack.

Expected contents include:

```text
README.md
CHANGELOG.md
CITATION.cff
CONTRIBUTING.md
LICENSE.md
RELEASE_CHECKLIST.md
spec/
  ptr-v0.1.md
schema/
  ptr-v0.1.schema.json
examples/
tests/
```

The schema provides machine-readable structural validation for PTR v0.1. The examples and tests directories provide reusable conformance fixtures, including valid records, invalid records, ambiguous input cases, and geometric QA cases.

Implementation libraries and applications should live in their own repositories and consume the specification defined here.

---

## Current v0.1 roadmap

The immediate goals are to:

- review the normative PTR v0.1 specification before release;
- validate the JSON Schema and conformance fixtures;
- finalize any release-blocking errata;
- publish the `v0.1.0` release tag; and
- archive a stable release citation if a DOI is desired.

See the repository issues for the working backlog.

---

## Status and stability

PTR v0.1 is an early baseline intended for implementation, testing, and refinement.

The design is intentionally small enough to use now, but details may change before a stable v1.0 specification is published.

Do not treat a `.ptr` file or PTR-derived computation as a substitute for an authoritative survey, cadastral record, land title, or professional determination where one is legally required.

---

## License

The PTR specification, schemas, examples, tests, and documentation are licensed under CC BY 4.0. See `LICENSE.md`.

If future repository contents include executable source code, that code should declare its own source-code license explicitly.

---

## Maintainer

PTR is initiated and maintained by **Spatialdom**. Change proposals should be opened as GitHub issues and reviewed against the compatibility policy in the v0.1 specification and `CONTRIBUTING.md`.
