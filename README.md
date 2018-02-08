# Introduction
I've been working on this shapefile python class since 2010. It allows for the easy reading, creation and updating of shapefiles using OGR.

OGR is a prerequisite:
`from osgeo import ogr`
OR
`import ogr`
I extensively used the documentation from http://www.gis.usu.edu/~chrisg/python/2009/docs.html, especially http://www.gis.usu.edu/~chrisg/python/2009/docs/ogr.pdf

# Installation
Simply save shapefile.py in your work folder and call:
`from shapefile import shapefile`

# Read shapefile
`````python
shpfile = "filename.shp"
shp = shapefile("read", shpfile)
for feat in shp.features: # shp.features is a simple python list with all the features loaded.
    attr_dict = shp.attr_dict(feat)
    # do something
shp.finish()
`````

# attr_dict example
`{"FID": 1 , "NAME": "John"}`

# Write shapefile
`````python
shpfile = "filename.shp"
inshp = shapefile("read", shpfile)
outshp = shapefile("write", shpfile[:-4] + "_new.shp", inshp.type, inshp.fieldslist, inshp.projection)
for feat in inshp.features:
    attr_dict = inshp.attr_dict(feat)
    # do something with feat or attr_dict
    outshp.createfeat(feat, attr_dict)
inshp.finish()
outshp.finish()
`````

# Projections
Projections are internally saved as ESRI format strings:
`shp.projection = ["ESRI", 'projection string']`
But can be provided as:
`projection = ["PROJ4", 'projection string']`
`projection = ["EPSG", number]`

# Update shapefile

With new field:
`````python
shpfile = "filename.shp"
shp = shapefile("update", shpfile)
if not shp.fieldexist('NAME'):
    shp.createfield(['NAME', 2, 10, 8])

for feat in shp.features:
    shp.updatefield(feat, 'NAME', 1)
shp.finish()
`````

By deleting feature:
`````python
shpfile = "filename.shp"
shp = shapefile("update", shpfile)

for feat in shp.features:
    if <some logic>:
        shp.deletefeat(feat)
shp.finish()
`````

# Select
Loop through all features with a certain field name value:
`````python
shp = shapefile("read", shpfile)
values = shp.fieldvaluelist('NAME', doubles=0) # default doubles=1 
for value in values:
    layer_select = shp.selectfeats('NAME', value)
    for feat in layer_select:
        # do something
shp.finish()
`````