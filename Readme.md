## MosaicJSON

MosaicJSON is an open standard for representing
metadata about a mosaic of Cloud-Optimized GeoTIFF (COG) files.

![](https://user-images.githubusercontent.com/10407788/68706772-7539ac00-055e-11ea-8c15-5ee4f30b143e.jpg)

MosaicJSON can be seen as a Virtual raster (see GDAL's [VRT](https://gdal.org/drivers/raster/vrt.html)) enabling spatial and temporal processing for a list of Cloud-Optimized GeoTIFF.

Official announcement: https://medium.com/devseed/cog-talk-part-2-mosaics-bbbf474e66df

## Features
- simple **JSON** format (enabling high ratio compression) 
- **quadkey** based file index

## Implementations 
- [cogeo-mosaic](https://github.com/developmentseed/cogeo-mosaic)


## API Example
<details>


```python
    def fetch_mosaic_definition(url: Union[str, Path]) -> Dict:
        """Fetch mosaic definition file."""
        ...
        return mosaic_definition


    def _fetch_and_find_asset(url: str, x: int, y: int, z: int):
        mdef = fetch_mosaic_definition(url)
        return get_assets(mdef, x: int, y: int, z: int)


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
        min_zoom = mosaic_definition["minzoom"]

        mercator_tile = mercantile.Tile(x=x, y=y, z=z)
        quadkey_zoom = mosaic_definition.get("quadkey_zoom", min_zoom)  # 0.0.2

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
                    fetch_and_find_assets(asset, x, y, z)
                    if os.path.splitext(asset)[1] in [".json", ".gz"]
                    else [asset]
                    for asset in assets
                ]
            )
        )
```

</details>

## License

The text of this specification is licensed under the
[MIT License](https://github.com/developmentseed/mosaicjson-spec/blob/master/LICENSE).
The use of this spec in products and code is entirely free:
there are no royalties, restrictions, or requirements.

## Authors

* Vincent Sarago
* Sean Harkins
* Drew Bollinger
