# MosaicJSON 0.0.3

## 1. Purpose

This specification attempts to create a standard for representing
metadata about a mosaic of Cloud-Optimized GeoTIFF (COG) files.

## 2. File format

MosaicJSON is intentionally based on the TileJSON specification and uses
the JSON format.

Implementations MUST treat unknown keys as if they weren't present.
However, implementations MUST expose unknown key/values in their API
so that API users can optionally handle these keys. Implementations MUST
treat invalid values for keys as if they weren't present. If the key is
required and not valid or present, implementations MUST treat the entire
MosaicJSON manifest file as invalid and refuse operation.


```json
{
    // REQUIRED. A semver.org style version number. Describes the version of
    // the MosaicJSON spec that is implemented by this JSON object.
    "mosaicjson": "0.0.3",

    // OPTIONAL. Default: null. A name describing the tileset. The name can
    // contain any legal character. Implementations SHOULD NOT interpret the
    // name as HTML.
    "name": "compositing",

    // OPTIONAL. Default: null. A text description of the tileset. The
    // description can contain any legal character. Implementations SHOULD NOT
    // interpret the description as HTML.
    "description": "A simple, light grey world.",

    // OPTIONAL. Default: "1.0.0". A semver.org style version number. When
    // changes across files are introduced, the minor version MUST change.
    // This may lead to cut off labels. Therefore, implementors can decide to
    // clean their cache when the minor version changes. Changes to the patch
    // level MUST only have changes to tiles that are contained within one tile.
    // When tiles change significantly, the major version MUST be increased.
    // Implementations MUST NOT use tiles with different major versions.
    "version": "1.0.0",

    // OPTIONAL. Default: null. Contains an attribution to be displayed
    // when the map is shown to a user. Implementations MAY decide to treat this
    // as HTML or literal text. For security reasons, make absolutely sure that
    // this field can't be abused as a vector for XSS or beacon tracking.
    "attribution": "<a href='http://openstreetmap.org'>OSM contributors</a>",

    // REQUIRED.
    // An integer specifying the minimum zoom level.
    "minzoom": 0,

    // REQUIRED.
    // An integer specifying the maximum zoom level. MUST be >= minzoom.
    "maxzoom": 11,

    // OPTIONAL.
    // The zoom value for the quadkey index. MUST be =< maxzoom.
    // If quadkey_zoom is > minzoom, then on each tile request from zoom between
    // minzoom and quadkey_zoom, the tiler will merge each quadkey asset lists.
    // The use of quadkey_zoom can be beneficial when dealing with a high number
    // of files and a large area.
    "quadkey_zoom": 0,

    // REQUIRED. Default: [-180, -90, 180, 90].
    // The maximum extent of available map tiles. Bounds MUST define an area
    // covered by all zoom levels. The bounds are represented in WGS:84
    // latitude and longitude values, in the order left, bottom, right, top.
    // Values may be integers or floating point numbers.
    "bounds": [ -180, -85.05112877980659, 180, 85.0511287798066 ],

    // OPTIONAL. Default: null.
    // The first value is the longitude, the second is latitude (both in
    // WGS:84 values), the third value is the zoom level as an integer.
    // Longitude and latitude MUST be within the specified bounds.
    // The zoom level MUST be between minzoom and maxzoom.
    // Implementations can use this value to set the default location. If the
    // value is null, implementations may use their own algorithm for
    // determining a default location.
    "center": [ -76.275329586789, 39.153492567373, 8 ],

    // REQUIRED. A dictionary of per quadkey assets in form of {quadkeys: [asset]} pairs.
    // Keys MUST be valid quadkeys index with zoom level equal to mosaic `minzoom` (or `quadkey_zoom` if present).
    // Values MUST be arrays of strings (url or sceneid) pointing to an
    // asset with bounds intersecting with the quadkey bounds.
    "tiles": {
        "030130": [
            "file1.tif",
            "file2.tif",
        ]
    },

    // OPTIONAL. Default: null.
    // A string describing the type of the asset found in `tiles`.
    "asset_type": "COG",

    // OPTIONAL. Default: null.
    // A string describing the prefix to add to the asset string.
    "asset_prefix": "http://hostname.io/cog/",

    // OPTIONAL. Default: null.
    // Asset's datatype info.
    // One of:
    // int8: 8-bit integer
    // int16: 16-bit integer
    // int32: 32-bit integer
    // int64: 64-bit integer
    // uint8: unsigned 8-bit integer (common for 8-bit RGB PNG's)
    // uint16: unsigned 16-bit integer
    // uint32: unsigned 32-bit integer
    // uint64: unsigned 64-bit integer
    // float16: 16-bit float
    // float32: 32-bit float
    // float64: 64-big float
    // cint16: 16-bit complex integer
    // cint32: 32-bit complex integer
    // cfloat32: 32-bit complex float
    // cfloat64: 64-bit complex float
    // other: Other data type than the ones listed above (e.g. boolean, string, higher precision numbers)
    "data_type": "uint8",

    // OPTIONAL. Default: null.
    // GDAL like colormap in form of {key: [r, g, b, a], ...}
    // In GDAL
    "colormap": {},

    // OPTIONAL. Default: null.
    // TileMatrixSet definition.
    "tilematrixset": {
        "title": "LINZ NZTM2000Quad Map Tile Grid",
        "abstract": "See https://github.com/linz/NZTM2000TileMatrixSet",
        "id": "NZTM2000Quad",
        "crs": "urn:ogc:def:crs:EPSG::2193",
        "orderedAxes": [
            "Y",
            "X"
        ],
        "tileMatrices": [
            {
                "id": "0",
                "scaleDenominator": 139770566.007179,
                "pointOfOrigin": [
                    10438190.1652,
                    -3260586.7284
                ],
                "tileWidth": 256,
                "tileHeight": 256,
                "matrixWidth": 1,
                "matrixHeight": 1,
                "cellSize": 39135.75848201011
            },
            ...
            {
                "id": "21",
                "scaleDenominator": 66.64779949530553,
                "pointOfOrigin": [
                    10438190.1652,
                    -3260586.7284
                ],
                "tileWidth": 256,
                "tileHeight": 256,
                "matrixWidth": 2097152,
                "matrixHeight": 2097152,
                "cellSize": 0.018661383858685546
            }
        ]
    }
}
```
