# Parcel Truth Records v0.1 Specification

**Status:** Draft normative specification  
**Version:** 0.1  
**File extension:** `.ptr`  
**Encoding:** UTF-8 JSON

## 1. Purpose and Scope

Parcel Truth Records (PTR) v0.1 defines a small, human-readable file format for representing one land parcel from its documentary bearing-distance boundary description.

A `.ptr` file represents exactly one parcel. It stores the source parcel description needed to reconstruct the parcel boundary as ordered courses. It does not store computed geometry as authoritative data.

The key words `MUST`, `MUST NOT`, `REQUIRED`, `SHOULD`, `SHOULD NOT`, and `MAY` in this document are to be interpreted as normative requirements.

PTR v0.1 is independent of PTR Core, PTRC, Spatialdom applications, and any specific database. A conforming implementation can read, write, and validate PTR v0.1 records without relying on undocumented application behavior.

## 2. File Format

A PTR v0.1 file:

- MUST be encoded as UTF-8.
- MUST contain a single JSON object at the top level.
- MUST use the `.ptr` file extension when stored as a file.
- MUST represent one parcel only.
- MUST NOT require any application-specific database identity to define the parcel boundary.

Object member order is not significant, except that array order is significant wherever this specification defines ordered arrays.

## 3. Data Model

The top-level object defines the parcel record. The following fields are defined by PTR v0.1:

| Field | Type | Required | Semantics |
| --- | --- | --- | --- |
| `ptr_version` | string | Yes | PTR format version. For this specification the value MUST be `"0.1"`. |
| `lines` | array | Yes | Ordered boundary courses for the parcel. Each item MUST be a course represented as `[bearing, distance]`. |
| `name` | string | No | Human-readable parcel name or label from a record or user workflow. |
| `record_id` | string | No | Application or database identifier. It MAY help external systems identify a record, but it is not part of the parcel boundary definition. |
| `tie_point` | string | No | Human-readable reference to the source tie point, monument, or control point. |
| `tie_line` | array | No | Course from `tie_point` to the parcel reference point, represented as `[bearing, distance]`. |
| `declared_area` | number | No | Documentary area stated by the source record, in square metres. |

Conforming readers MUST apply the required semantics of these fields. Conforming writers MUST NOT write computed geometry or metrics into these fields unless the value is documentary source information as defined here.

PTR v0.1 does not define extension fields. Implementations that encounter unknown fields MAY preserve them for round-tripping, but MUST NOT treat them as normative PTR v0.1 fields.

## 4. Version

`ptr_version` is REQUIRED.

For PTR v0.1, `ptr_version`:

- MUST be a JSON string.
- MUST have the exact value `"0.1"`.

Readers that implement only PTR v0.1 MUST reject records with a different `ptr_version` value.

## 5. Courses

A course is the canonical bearing-distance representation:

```json
["N68-28E", 25.4]
```

Each course:

- MUST be a JSON array.
- MUST contain exactly two items.
- MUST place the bearing at index `0`.
- MUST place the distance at index `1`.

Line numbers and point numbers are implicit from array order. Writers MUST NOT add line numbers or point numbers into each course to make the boundary order authoritative.

## 6. Bearings

A course bearing MUST be a JSON string using the PTR canonical bearing notation.

Canonical bearings are ASCII-safe. Examples of canonical bearing strings include:

```text
N
E
S
W
N68-28E
S11-44W
```

At a high level, PTR v0.1 bearings identify cardinal directions or quadrant bearings. A quadrant bearing starts with `N` or `S`, contains an angular offset, and ends with `E` or `W`.

Applications MAY display a canonical value such as `N68-28E` as a more human-friendly form such as `N 68 deg 28' E`, provided the stored PTR value remains canonical.

The exact serialized grammar, valid ranges, case rules, optional seconds support, whitespace rules, and normalization behavior are tracked by the canonical bearing grammar work in https://github.com/spatialdom/ptr/issues/3. Until that grammar is finalized, conforming PTR v0.1 documents SHOULD use only uppercase cardinal letters and the ASCII hyphenated minute form shown above.

Implementations MUST reject ambiguous bearings rather than guessing the user's intent.

## 7. Distances and Areas

All course distances:

- MUST be JSON numbers.
- MUST be expressed in metres.
- MUST represent documentary course lengths.
- MUST be greater than zero.

`declared_area`, when present:

- MUST be a JSON number.
- MUST be expressed in square metres.
- MUST represent documentary source area, not a computed area.
- MUST be greater than zero.

Writers SHOULD use ordinary decimal JSON numbers for human-authored PTR files. Display precision is a presentation concern and does not change the stored documentary value.

## 8. Boundary Order and Closure

`lines` is REQUIRED and MUST be a JSON array of boundary courses.

The courses in `lines`:

- MUST contain at least three courses.
- MUST be ordered around the parcel boundary.
- SHOULD be ordered clockwise.
- MUST be interpreted as starting at Point 1.
- MUST define each subsequent point by applying the next course in array order.
- MUST interpret the final course as the documentary course intended to return to Point 1.

The first course is Line 1 from Point 1 to Point 2. The second course is Line 2 from Point 2 to Point 3. This pattern continues until the final course, which runs from the final numbered point back to Point 1.

A PTR file stores documentary courses. Numerical reconstruction MAY reveal misclosure because of source precision, transcription error, rounding, or inconsistent records. Implementations MUST NOT silently alter, balance, stretch, rotate, scale, or otherwise change stored documentary courses to force closure.

Implementations MAY compute and report closure diagnostics, adjusted geometry, or quality warnings, but those values are derived computational values and MUST remain distinct from the stored PTR record.

## 9. Tie Point and Tie Line

`tie_point` and `tie_line` are optional documentary reference information.

When present, `tie_point`:

- MUST be a JSON string.
- SHOULD identify the monument, control point, cadastral reference, or other source tie point as written or normalized by the recording application.

When present, `tie_line`:

- MUST be a course represented as `[bearing, distance]`.
- MUST describe the course from `tie_point` to the parcel reference point.
- MUST use metres for distance.
- MUST use canonical PTR bearing notation for bearing.

`tie_line` does not replace or prepend to `lines`. Boundary courses in `lines` still define the parcel boundary.

## 10. Documentary and Derived Values

PTR v0.1 stores documentary values. Documentary values are values taken from, or intentionally recorded as part of, the parcel description.

The following are stored documentary values when present:

- `name`
- `record_id`
- `tie_point`
- `tie_line`
- `lines`
- `declared_area`

Derived computational values include, but are not limited to:

- vertices
- coordinates
- geometry
- computed area
- perimeter
- closure error
- misclosure ratio
- centroid
- bounding box
- adjusted or balanced courses

Derived values MAY be computed by PTR Core or consuming applications. They MUST NOT be treated as authoritative PTR v0.1 fields in a `.ptr` file.

`declared_area` and computed area are different values. Implementations MUST NOT overwrite `declared_area` with computed area unless the user explicitly changes the documentary value outside the scope of automatic computation.

## 11. Out of Scope

PTR v0.1 does not attempt to represent a complete cadastral, legal, tax, or GIS database.

The following are outside the normative PTR v0.1 record:

- ownership and taxpayer data
- title history
- transactions
- tax declarations
- assessed or market values
- zoning
- land use
- hazard exposure
- buildings
- permits
- imagery
- infrastructure
- arbitrary GIS feature attributes
- multi-parcel collections
- topological relationships between parcels
- coordinate reference systems
- authoritative legal determinations
- application workflow state

External systems MAY link any of those datasets to a PTR-derived parcel by identifiers, spatial relationships, temporal relationships, or application-level records.

## 12. Valid Complete Example

The following is a valid complete PTR v0.1 record:

```json
{
  "ptr_version": "0.1",
  "name": "Lot 2173",
  "record_id": "cad-123-lot-2173",
  "tie_point": "BLLM No. 1, Cad-123",
  "tie_line": ["S11-44W", 2351.0],
  "lines": [
    ["N", 100.0],
    ["E", 80.0],
    ["S", 100.0],
    ["W", 80.0]
  ],
  "declared_area": 8000.0
}
```

This example uses a simple rectangular parcel for clarity. A reader can reconstruct relative geometry from `lines`; any resulting vertices, computed area, perimeter, and closure diagnostics are derived values.

## 13. Minimal Valid Example

The following is the smallest shape of a PTR v0.1 record:

```json
{
  "ptr_version": "0.1",
  "lines": [
    ["N", 10.0],
    ["E", 10.0],
    ["S45-00W", 14.1421356237]
  ]
}
```

## 14. Conformance

A conforming PTR v0.1 reader MUST:

- Parse a UTF-8 JSON object with `ptr_version` equal to `"0.1"`.
- Validate the required fields defined by this specification.
- Interpret courses as ordered `[bearing, distance]` arrays.
- Preserve the distinction between documentary stored values and derived computational values.
- Reject records whose required structure or values violate this specification.

A conforming PTR v0.1 writer MUST:

- Emit UTF-8 JSON.
- Emit `ptr_version` with value `"0.1"`.
- Emit `lines` as ordered boundary courses.
- Use canonical course representation.
- Store distances in metres and `declared_area` in square metres.
- Avoid writing derived computational values as authoritative PTR fields.
