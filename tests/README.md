# PTR v0.1 Conformance Fixtures

These fixtures document expected validation outcomes for independent PTR v0.1 implementations.

Validation layers:

- `serialization`: UTF-8 JSON with one top-level object.
- `structural`: required fields, field types, array shapes, and version value.
- `semantic`: PTR value rules such as canonical bearings and positive metre distances.
- `geometric_qa`: derived diagnostics such as closure, orientation, self-intersection, and computed area differences.

## Valid Fixtures

| Fixture | Expected result |
| --- | --- |
| `examples/minimal/minimal.ptr` | Conforming PTR v0.1 record. |
| `examples/complete/complete.ptr` | Conforming PTR v0.1 record. |
| `tests/valid/cardinal-square.ptr` | Conforming record using cardinal bearings. |
| `tests/valid/quadrant-triangle.ptr` | Conforming record using quadrant bearings. |
| `tests/valid/scientific-distance.ptr` | Conforming record using valid JSON scientific notation for distances. |
| `tests/bearings/bearing-seconds.ptr` | Conforming record using nonzero seconds. |
| `tests/tie_points/tie-line.ptr` | Conforming record with tie point and tie line. |
| `tests/edge_cases/minimum-offset-seconds.ptr` | Conforming record using the smallest nonzero quadrant offset. |

## Invalid Fixtures

| Fixture | Expected result |
| --- | --- |
| `tests/invalid/invalid-json.ptr` | Serialization failure; non-conforming. |
| `tests/invalid/missing-lines.ptr` | Structural failure; non-conforming. |
| `tests/invalid/bad-course-shape.ptr` | Structural failure; non-conforming. |
| `tests/invalid/negative-distance.ptr` | Semantic failure; non-conforming. |
| `tests/invalid/zero-distance.ptr` | Semantic failure; non-conforming. |
| `tests/invalid/string-distance.ptr` | Structural failure; non-conforming. |
| `tests/invalid/zero-declared-area.ptr` | Semantic failure; non-conforming. |
| `tests/invalid/tie-line-without-tie-point.ptr` | Semantic field-relationship failure; non-conforming. |
| `tests/bearings/invalid-bearing-minutes.ptr` | Semantic failure; non-conforming. |
| `tests/bearings/invalid-bearing-leading-zero.ptr` | Semantic failure; non-conforming. |
| `tests/bearings/invalid-bearing-zero-seconds.ptr` | Semantic failure; non-conforming. |

## Ambiguous Input Fixtures

| Fixture | Expected result |
| --- | --- |
| `tests/ambiguous/ambiguous-bearing-input.txt` | Reject before storage; no canonical bearing is guessed. |
| `tests/ambiguous/ambiguous-bearing-stored.ptr` | Semantic failure if found in a `.ptr` file. |

## Geometric QA Fixtures

| Fixture | Expected result |
| --- | --- |
| `tests/closure/non-closing.ptr` | Conforming record with geometric QA finding for misclosure. |
| `tests/closure/counterclockwise-order.ptr` | Conforming record with geometric QA finding for winding/order convention. |
| `tests/closure/self-intersecting.ptr` | Conforming record with geometric QA finding for self-intersection. |

Schema validation covers serialization, structural rules, and value constraints that JSON Schema can express. It intentionally does not compute closure or other geometric QA diagnostics.
