# Linking Arches data to ArcGIS

## Creating Spatial Views
1. Spatial views were introduced into Arches 6.1.0 so ensure the version of you Arches is at minimum this.

2. Navigate to `/admin/models/spatialview/`

3. Add spatial view:
    * Ensure schema is public
    * Add a name in the slug box
    * Select the correct geometry node UUID
    * Leave is mixedgeometrytypes unchecked
    * Attribute nodes can be included in the spatial view. This data will be included in the final table. Format this as such:
      ```
      [
          {
              "nodeid": "a6823fac-545c-11ed-8b38-00155daa752b",
              "description": "name"
          }, {
              "nodeid": "a6a5e326-545c-11ed-8b38-00155daa752b",
              "description": "number"
          }
      ]
      ```

## ArcGIS Pro
1. Install ArcGIS pro desktop and log in.

2. Create a new project.

3. Nativate to the insert tab, go to connections and add 'New database connection'.

4. If the postgres database is locally run, put "localhost" in the instance box, otherwise put the IP.

5. In the connected database scroll down until you see the spatial view table formatted as such:
```
database_name.public.sp_attr_slug_name
```
and the geometry data formatted as such:
```
database_name.public.slug_name_point
database_name.public.slug_name_polygon
database_name.public.slug_name_linestring
```

6. Right click the geometry data(s) and select "Add to current map"

### If you wish to add additional fields not included in Arches we will have to make some changes. Here we will add the arches URL as an additional field.
7. On the left hand of the screen in "Drawing Order" right click the current map geometry and go to data > export features

8. Right click the new geometry and click attribute table.

9. Click to add new field - put in field name (I have called it "archesURL") and change Data Type to text. Save the changes. Close the add fields tab and navigate back to attribute table.

10. Click on the new field and select the Calculate tab.

11. Make sure expression type is Python3. In the archesURL = box add the following python code:
```
"http://127.0.0.1:8000/report/" + !resourceinstanceid!
```



