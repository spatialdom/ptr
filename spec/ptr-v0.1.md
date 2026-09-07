# Parcel Truth Records v0.1 Specification

**Status:** Draft normative specification  
**Version:** 0.1  
**File extension:** `.ptr`  
**Encoding:** UTF-8 JSON

## 1. Purpose and Scope

Parcel Truth Records (PTR) v0.1 defines a small, human-readable file format for representing one land parcel from its documentary bearing-distance boundary description.

A `.ptr` file represents exactly one parcel. It stores the source parcel description needed to reconstruct the parcel boundary as ordered courses. It does not store computed geometry as authoritative data.

PTR describes the parcel boundary record itself, not the broader administrative, legal, fiscal, market, environmental, or application context that may be associated with the parcel.

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

`record_id` is optional application or database identity. It MAY be used to correlate a PTR record with external systems, but it MUST NOT be required to reconstruct the parcel boundary and MUST NOT be treated as part of the parcel's canonical boundary definition.

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
N68-28-30E
```

PTR v0.1 bearings identify cardinal directions or quadrant bearings.

### 6.1 Canonical Bearing Grammar

The canonical stored bearing syntax is:

```abnf
bearing          = cardinal / quadrant
cardinal         = "N" / "E" / "S" / "W"
quadrant         = ns degrees "-" minutes [ "-" seconds ] ew
ns               = "N" / "S"
ew               = "E" / "W"
degrees          = "0" / nonzero-digit / nonzero-digit digit
minutes          = digit digit
seconds          = digit digit
digit            = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9"
nonzero-digit    = "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9"
```

The grammar above is further constrained by these numeric rules:

- `degrees` MUST be an integer from `0` through `89`.
- `degrees` MUST NOT contain leading zeroes, except for the single value `0`.
- `minutes` MUST be an integer from `00` through `59` and MUST contain exactly two digits.
- `seconds`, when present, MUST be an integer from `01` through `59` and MUST contain exactly two digits.
- A quadrant bearing's angular offset MUST be greater than 0 degrees and less than 90 degrees.

The canonical syntax is case-sensitive. Canonical bearings MUST use uppercase `N`, `E`, `S`, and `W`, ASCII hyphen-minus (`-`) separators, and no whitespace.

### 6.2 Cardinal Bearings

The only legal canonical cardinal bearings are:

```text
N
E
S
W
```

Cardinal bearings are not quadrant bearings. Equivalent quadrant forms such as `N0-00E`, `S0-00W`, `N90-00E`, or `S90-00E` are not canonical and MUST be rejected as stored PTR values.

### 6.3 Quadrant Bearings

A quadrant bearing starts with `N` or `S`, contains an angular offset east or west from that north-south axis, and ends with `E` or `W`.

Canonical quadrant examples include:

```text
N0-01E
N68-28E
S11-44W
S89-59-59E
```

Seconds are supported in PTR v0.1 only when they are nonzero. A bearing with zero seconds MUST omit the seconds component; for example, `N68-28E` is canonical and `N68-28-00E` is not.

### 6.4 Input Normalization

Conforming PTR v0.1 files MUST store canonical bearing strings. Applications MAY accept and normalize common unambiguous input forms before writing a PTR file. Normalization is a parser feature; it does not change the canonical stored syntax.

Implementations MAY normalize all of the following input variations when the result is unambiguous:

- Lowercase or mixed-case direction letters.
- Leading and trailing whitespace around the whole input.
- Whitespace between direction letters and numeric components.
- A degree symbol (`deg`), lowercase `d`, or uppercase `D` between degrees and minutes.
- An apostrophe (`'`), typographic prime, lowercase `m`, or uppercase `M` after minutes.
- A quotation mark (`"`), typographic double-prime, lowercase `s`, or uppercase `S` after seconds.
- Hyphen-minus separators in place of degree, minute, and second markers.
- Omitted seconds when the bearing has only degrees and minutes.

Accepted input examples and their canonical normalized values:

| Input | Canonical value |
| --- | --- |
| `n68-28e` | `N68-28E` |
| `N 68 28 E` | `N68-28E` |
| `N 68 deg 28' E` | `N68-28E` |
| `N68d28m30sE` | `N68-28-30E` |
| `S 11 D 44 M 00 S W` | `S11-44W` |
| ` e ` | `E` |

Applications MAY display a canonical value such as `N68-28E` as a more human-friendly form such as `N 68 deg 28' E`, provided the stored PTR value remains canonical.

### 6.5 Invalid or Ambiguous Bearings

Implementations MUST reject ambiguous bearings rather than guessing the user's intent.

The following examples are invalid as canonical stored PTR values and SHOULD be rejected or normalized before storage, as indicated:

| Input | Reason |
| --- | --- |
| `n68-28e` | Not canonical because direction letters are lowercase; may normalize to `N68-28E`. |
| `N 68 28 E` | Not canonical because it contains whitespace; may normalize to `N68-28E`. |
| `N068-28E` | Not canonical because degrees contain leading zeroes. |
| `N68-7E` | Minutes MUST contain exactly two digits. |
| `N68-60E` | Minutes are out of range. |
| `N68-28-00E` | Zero seconds MUST be omitted; may normalize to `N68-28E`. |
| `N68-28-60E` | Seconds are out of range. |
| `N0-00E` | Zero angular offset is represented by a cardinal bearing, not a quadrant bearing. |
| `N90-00E` | Ninety-degree offset is represented by a cardinal bearing, not a quadrant bearing. |
| `E68-28N` | Quadrant bearings MUST start with `N` or `S` and end with `E` or `W`. |
| `NE68-28` | Direction letters are not in canonical quadrant positions. |
| `68-28NE` | Direction letters are not in canonical quadrant positions. |
| `N68E28` | Component order is ambiguous. |

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

The following are examples of parcel context, not PTR documentary values:

- current owner or taxpayer
- tax declaration number
- zoning classification
- flood-hazard classification
- building footprint
- market valuation
- title transaction history
- application review status

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

Computed geometry and metrics are derived because they depend on interpretation, numeric precision, reconstruction algorithms, and QA policy. Storing those values as authoritative PTR fields would make it unclear whether the documentary courses or the computed geometry controls when they disagree.

## 11. Scope Boundaries

PTR v0.1 does not attempt to represent a complete cadastral, land-registration, taxation, land-administration, legal, or GIS database.

This section is normative. Contextual information is not part of PTR merely because it relates to a parcel. Such information normally belongs in applications, databases, GIS layers, registries, or linked datasets outside the `.ptr` file.

The following non-exhaustive examples are outside the normative PTR v0.1 record:

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

Future PTR versions MAY define additional fields when they are necessary for interoperable parcel-description exchange. PTR v0.1 intentionally avoids speculative fields whose primary purpose is application workflow, land-administration context, or derived analysis.

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

## 14. Validation

PTR v0.1 validation distinguishes conformance from derived parcel-quality analysis. Implementations MAY expose their own exception classes, API response shapes, severities, or diagnostic codes, but those implementation details are outside this specification.

The validation categories are:

| Category | Purpose | Non-conforming on failure? |
| --- | --- | --- |
| Serialization | Confirms that the file is UTF-8 JSON containing one top-level object. | Yes |
| Structural | Confirms required fields, JSON types, course array shape, and version value. | Yes |
| Semantic | Confirms PTR value rules such as canonical bearings, positive metre distances, ordered boundary course requirements, and valid field relationships. | Yes |
| Geometric QA | Reports derived diagnostics such as closure, degeneracy, self-intersection, computed area difference, or orientation. | No |

A record with serialization, structural, or semantic failures is not a conforming PTR v0.1 record. A record with only geometric QA findings remains a conforming PTR v0.1 record unless another requirement in this specification is violated.

Ambiguous human input and invalid canonical PTR serialization are different cases:

- Ambiguous human input is text supplied to a parser or application before a PTR value is written. It MUST be rejected rather than guessed.
- Invalid canonical PTR serialization is a value already present in a `.ptr` file that violates the canonical syntax or another PTR requirement. The containing record is non-conforming.
- Non-canonical but unambiguous input MAY be normalized before storage. Once stored, the `.ptr` file MUST contain the canonical value.

Numerical misclosure does not by itself make a PTR record non-conforming. A valid documentary description MAY fail to close because of source precision, transcription, rounding, or inconsistent source records. Implementations SHOULD report closure diagnostics as geometric QA findings and MUST NOT silently modify stored documentary courses to force closure.

Self-intersection, duplicate vertices, zero-area reconstruction, unusually short courses, counterclockwise order, large difference between `declared_area` and computed area, and similar geometry-derived conditions are geometric QA findings. They do not by themselves make a record non-conforming because PTR stores the documentary description, not an authoritative computed geometry. Implementations MAY classify such findings as warnings, errors, review blockers, or informational diagnostics in application-specific workflows.

Representative validation outcomes:

| Case | Example condition | Expected outcome |
| --- | --- | --- |
| Minimal valid record | Has `ptr_version` and at least three valid `lines` courses. | Conforming PTR v0.1 record. |
| Complete valid record | Uses all defined optional fields with valid documentary values. | Conforming PTR v0.1 record. |
| Invalid JSON | File cannot be parsed as UTF-8 JSON. | Serialization failure; non-conforming. |
| Missing `lines` | Top-level object omits required `lines`. | Structural failure; non-conforming. |
| Bad course shape | A course has three items or stores distance before bearing. | Structural failure; non-conforming. |
| Invalid canonical bearing | Stored bearing is `N68-60E`. | Semantic failure; non-conforming. |
| Ambiguous bearing input | User input is `N68E28`. | Reject before storage; no canonical value is guessed. |
| Non-canonical bearing input | User input is `n68-28e`. | May normalize to `N68-28E` before storage. |
| Non-closing parcel | All stored courses are valid, but reconstructed endpoint misses Point 1. | Conforming record with geometric QA finding. |
| Self-intersecting parcel | Valid courses reconstruct a crossing boundary. | Conforming record with geometric QA finding. |
| Declared area mismatch | `declared_area` differs from computed area. | Conforming record with geometric QA finding. |

## 15. Conformance

A conforming PTR v0.1 reader MUST:

- Parse a UTF-8 JSON object with `ptr_version` equal to `"0.1"`.
- Validate the required fields defined by this specification.
- Distinguish serialization, structural, semantic, and geometric QA outcomes.
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
