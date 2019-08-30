## MosaicJSON

MosaicJSON is an open standard for representing
metadata about a mosaic of Cloud-Optimized GeoTIFF (COG) files.

# License

The text of this specification is licensed under the
[MIT License](https://github.com/developmentseed/mosaicjson-spec/blob/master/LICENSE).
The use of this spec in products and code is entirely free:
there are no royalties, restrictions, or requirements.

## Authors

* Vincent Sarago
* Sean Harkins
* Drew Bollinger
<<<<<<< Updated upstream
=======

## API Example
```python
def fetch_mosaic_definition(url: Union[str, Path]) -> Dict:
    """Fetch mosaic definition file."""
    ...
    return mosaic_definition


def get_assets(mosaic_definition: Dict, x: int, y: int, z: int) -> list[str]:
    """Get asset list for a Z/X/Y index and a mosaic definition.
    
    Parameters
    ----------
    mosaic_definition : dict
        mosaic definition content.
    x : int
        Mercator tile X index.
    y : int
        Mercator tile Y index.
    z : int
        Mercator tile ZOOM level.

    Returns
    -------
    assets : list
        list of assets intersecting with the tile index. 
   
    """
    def _fetch_and_find_asset(url: str, **args):
        mdef = fetch_mosaic_definition(url)
        return get_assets(mdef, **args)

    min_zoom, max_zoom = mosaic_definition["minzoom"], mosaic_definition["maxzoom"]
    if z > max_zoom or z < min_zoom:
        return []  # return empty asset

    mercator_tile = mercantile.Tile(x=x, y=y, z=z)
    
    quadkey_zoom = mosaic_definition.get("quadkey_zoom", min_zoom)

    # get parent
    if mercator_tile.z > quadkey_zoom:
        depth = mercator_tile.z - quadkey_zoom
        for i in range(depth):
            mercator_tile = mercantile.parent(mercator_tile)
        quadkey = [mercantile.quadkey(*mercator_tile)]

    # get child
    elif mercator_tile.z < quadkey_zoom:
        depth = quadkey_zoom - mercator_tile.z
        mercator_tiles = [mercator_tile]
        for i in range(depth):
            mercator_tiles = sum([mercantile.children(t) for t in mercator_tiles], [])

        mercator_tiles = list(filter(lambda t: t.z == quadkey_zoom, mercator_tiles))
        quadkey = [mercantile.quadkey(*tile) for tile in mercator_tiles]

    else:
        quadkey = [mercantile.quadkey(*mercator_tile)]

    assets = list(
        itertools.chain.from_iterable(
            [mosaic_definition["tiles"].get(qk, []) for qk in quadkey]
        )
    )

    # check if we have a mosaic in the url (.json/.gz)
    return list(
        itertools.chain.from_iterable(
            [
                _fetch_and_find_asset(asset, x, y, z)
                if asset.endswith(".json") or asset.endswith(".json.gz")
                else [asset]
                for asset in assets
            ]
        )
    )
```

## Implementations 
- [cogeo-mosaic](https://github.com/developmentseed/cogeo-mosaic)
>>>>>>> Stashed changes
