gislib
==========================================

Using pyramids
--------------

**Command line**

Say you want to add a tif test data set to a new pyramid, assuming you want to set your NODATA value to -9999::
    
    mkdir /path/to/pyramid
    pyramid -b 256 256 -t 1024 1024 -n -9999  /path/to/pyramid /path/to/test.tif

Then if you want to add data to that pyramid you can just::

    pyramid /path/to/pyramid /path/to/additional_data.tif

**Code**

Instantiate a pyramid::
    
    pyramid = pyramids.pyramid(path_to_pyramid)

Adding raster data to a pyramid::

    pyramid.add(gdal_dataset1)
    pyramid.add(gdal_dataset2)

The first dataset added to a new pyramid determines the pyramid's
datatype, projection, tilesize and nodatavalue from the dataset being
added. This can be overridden by supplying other properties as keyword
arguments::
    
    pyramid.add(gdal_dataset, projection=3857, tilesize=(1024, 1024))

Keyword arguments are silently ignored on subsequent additions.

Retrieving rasterdata from a pyramid::

    pyramid.warpinto(gdal_dataset)

Warp algorithms test for gdal
-----------------------------
Gdalwarp on the commandline uses an error tolerance of 0.125, but the python bindings default it to 0! Don't forget to set it to something else than 0, because this makes the warping really slow.

Some tests with downsampling of landuse imagery:
near:     0.390s
bilinear: 0.735s <= This one gave a better antialias effect than cubic
cubic:	  1.153s
lanczos: 43.388s

Usage, etc.

Using gdal_retile.py for the geoserver pyramid plugin
-----------------------------------------------------
Create a bunch of symlinks in <myDirWithSymlinks>::
    
    find <mySourceDir> | grep tif$ | xargs -I {} ln -s {}

Create the pyramid using gdal::

    gdal_retile.py -v -co COMPRESS=DEFLATE -co TILED=YES -of gtiff -ps 2000 2500 -s_srs epsg:28992 -levels 10 -r near -targetDir <myTargetDir> <myDirWithSymLinks>
