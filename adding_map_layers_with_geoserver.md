# Adding new map layers in Arches with Geoserver
Prerequisites:
- Geoserver installed and available at `/geoserver`
- Map layer files in appropriate format `tif, png, jpeg`

## Geoserver   
### 1. Hosting the files
Host the files in your projects `uploadedfiles/` directory. 
For completeness create a `map_layers/` subdirectory and make sure apache has access to it.

`sudo chmod 775 map_layers/`

`sudo chgrp www-data map_layers/`

### 2. Create a new workspace in Geoserver

`name: arches_map_layers`
`Namespace URI: arches_map_layers`

### 3. Create a store for each maplayer
Select the right data source type

For .tiff files use Raster Data Source: GeoTIFF

Select the arches_map_layers workspace

Set a Data source name to match the layer name

Set the URL to be the file path to the .tiff 

### 4. Create a layer

Choose `arches_map_layers:<source name>`

Then click publish!

### 5. Publish layer

Most of this config should be automatic

You may need to set a transparent colour to white or black depending on the layer

Save!

Preview the layer in 'Layer Preview' 'Open Layers'

## Arches
 
Go to /admin

### 1. Add a map source
Match the name with the layer name

In the source add this:
`{"type": "raster", "tiles": ["<url>/geoserver/<workspace name>/wms?bbox={bbox-epsg-3857}&format=image/png&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&width=256&height=256&layers=<workspace:layer_name>&transparent=true"], "tileSize": 256}`

But change the `<url>` and `<workspace:map_layer>` to match your implementation and layer you are adding.


### 2. Adding a map layer
Add a name for the layer

Layerdefinition: `[{"id": <id>, "type": "raster", "source": <source>, "maxzoom": 22, "minzoom": 0}]`

`<id>` matches layer name and `<source>` matches the source name from the previous step. 

Select isoverlay

Add  `fa fa-globe` for the icon. 

# You can now add this maplayer to your arches map !
check the transparency settings,  opacity may need tweeking.



## Useful links
https://groups.google.com/g/archesproject/c/vaBMwbYp6IY/m/nqL6xO-PAwAJ
