# 0.0.3 (2023-05-31)

- add `asset_type` to describe the type of asset (e.g COG or STAC)
- add `asset_prefix` to use to construct a full asset url
- add `data_type`
- add `colormap`, which can be useful if the assets have the same internal colormap
- add `tilematrixset` to support more than the default WebMercatorQuad TMS
- add `layers` to define a set of configurations which can be used to create tiles

#### Misc
- added pre-commit to validate the JSON schema

# 0.0.2 (2019-11-20)

- add `bounds` as a required key (#4)
- add `quadkey_zoom` as an optional key (#2)

# 0.0.1 (2019-03-26)

- Initial release
