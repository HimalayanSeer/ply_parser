# ply_parser

[![Tests](https://github.com/HimalayanSeer/ply_parser/actions/workflows/test.yml/badge.svg)](https://github.com/HimalayanSeer/ply_parser/actions/workflows/test.yml)
[![PyPI](https://img.shields.io/pypi/v/ply_parser)](https://pypi.org/project/ply_parser/)
[![Python](https://img.shields.io/pypi/pyversions/ply_parser)](https://pypi.org/project/ply_parser/)
[![Downloads](https://static.pepy.tech/badge/ply_parser)](https://pepy.tech/projects/ply_parser)

**Read PLY (Polygon File Format) files in Python, with zero dependencies.**

`ply_parser` reads ASCII and binary PLY meshes into plain Python lists: vertices, faces, colors and normals. It follows the header, so unusual vertex layouts and extra elements are handled, and malformed files fail with a readable `ValueError` instead of silent garbage.

```bash
pip install ply_parser
```

Requires Python 3.9+.

## Quick start

```python
from ply_parser import parse_ply_file

obj = parse_ply_file("cube.ply")

print(obj)                 # PLYObject(name='cube.ply', vertices=8, faces=12, colors=0, normals=0)
print(obj.vertices[0])     # [-1.0, -1.0, 1.0]
print(obj.faces[0])        # [0, 1, 2]
```

## What you get

`parse_ply_file()` takes a path (`str` or `pathlib.Path`) and returns a `PLYObject` with:

| Attribute  | Contents                                                              |
| ---------- | --------------------------------------------------------------------- |
| `name`     | The path you passed, or the `name` you gave `parse_ply()`             |
| `vertices` | `[x, y, z]` floats for every vertex                                   |
| `faces`    | Vertex indices for each face (any number of vertices per face)        |
| `colors`   | `[r, g, b]` per vertex, as stored in the file. Empty if none          |
| `normals`  | `[nx, ny, nz]` per vertex. Empty if none                              |

Colors keep their file values: a `uchar` channel is `0-255`.

## Features

- **ASCII, binary little-endian and big-endian** formats
- **Any header layout**: properties in any order, extra properties and extra elements are skipped
- **Polygons, not just triangles**: faces keep every vertex index
- **Colors and normals** picked up from `red/green/blue`, `r/g/b`, `diffuse_*`, `nx/ny/nz` or `normal_*` properties
- **Strict by default**: header and body must agree, indices must be in range, coordinates must be finite
- **Parse from memory** as well as from disk

## Parsing data you already have

```python
from ply_parser import parse_ply

with open("scan.ply", "rb") as f:
    obj = parse_ply(f.read(), name="scan")
```

`parse_ply()` takes `str` for ASCII data or `bytes` for any format.

For line-based input, `parse_ply_file_data()` returns the older `[vertices, faces, colors]` list:

```python
from ply_parser import parse_ply_file_data

vertices, faces, colors = parse_ply_file_data(lines)
```

## Handling bad files

```python
from ply_parser import parse_ply_file

try:
    obj = parse_ply_file("broken.ply")
except ValueError as err:
    print(err)  # e.g. "PLY: vertex index 9 out of range (only 8 vertices) in face 0"
```

## Development

```bash
git clone https://github.com/HimalayanSeer/ply_parser
cd ply_parser
pip install .
python -m unittest tests/test_ply_parser.py -v
```

## Contributing

Issues and pull requests are welcome, especially sample PLY files that the parser gets wrong. Please include a small file that reproduces the problem.

## License

MIT. See [LICENSE](LICENSE).
