## D3
- D3  https://github.com/d3/d3/releases/tag/v4.13.0
- D3 Geoprojection https://github.com/d3/d3-geo-projection/releases/tag/v2.4.1

## Get the data from Natural Earth
Here’s the [main download page for Natural Earth](http://www.naturalearthdata.com/downloads/). Depending on the level of detail you can choose from:

* 110m is the least detailed and the smallest file size.
* 50m is in the middle — quite detailed and OK file size.
* 10m is the most detailed and the largest file size. It’s “suitable for making zoomed-in maps of countries and regions.”

[“Admin 0 – Countries”](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/50m/cultural/ne_50m_admin_0_countries.zip).

## Edit the shapefiles in QGIS
1. Change ISO_A2 codes: GB to UK, check FR (France) and NO (Norway)
- Layer > Open attribute table
- Change info
- Save edits

FR and NO show as -99. Some kind of explanation here: https://github.com/nvkelso/natural-earth-vector/issues/112

2. Delete Antartica
- right click the layer
- choose “Toggle Editing”
- in the toolbar select the pointer with the area “Select Features …”
- click on Antartica
- press delete on the keyboard

Split shape for France
1. Layer > Filter > "NAME" =  'France'
2. Vector > Geometry Tools > Multipart to singleparts
3. Splits them to a temporary layer
4. Delete France features from main layer
5. Copy new split France features to main layer
6. Paste on to main layer


## Install the tools you need
Mike Bostock’s tutorial has great instructions on [installing gdal and topojson](https://bost.ocks.org/mike/map/#installing-tools) for use on the command line. After following those instructions you should be able to type:
<pre>
  <code>
    which ogr2ogr
    which topojson
  </code>
</pre>
in your terminal and it will print out the path to those programs. If not, start searching on Stack Overflow for your particular error messages.

## Convert to shapefiles to geojson
Natural Earth data comes as shapefiles, which we can convert to geojson using ogr2ogr. Unzip the Natural Earth files you downloaded and cd into that folder in your terminal window. Then paste this into your window:

<pre>
<code>
ogr2ogr \
  -f GeoJSON \
  world.json \
  ne_50m_admin_0_countries_lakes/ne_50m_admin_0_countries_lakes.shp
</code>
</pre>

We’re taking the shapefiles from the folder ne_50m_admin_0_countries and converting them to geojson and saving it as world.json.

## Convert to geojson to topojson
geojson is a great format. You can read it and edit it quite easily as well as cut and paste stuff around. You can also use something like [geojson.io](http://geojson.io/) to display it and edit it. But it makes big files. My world.json is 4.6MB. So we’ll convert it to a format called topojson and then you can convert it back to geojson when you load the data in your map. Here's the command:

<code>
  topojson --id-property ISO_A2 -p name -o world-topo.json world.json --stitch-poles false
</code>

</pre>

We’re taking the geojson file we made (world.json) and turning it into topojson. [Topojson removes all properties by default](https://github.com/mbostock/topojson/wiki/Command-Line-Reference#properties), so it will strip out all the stuff from your geojson unless you tell it not to. Here we’re making the id the ISO 3166-1 alpha-2 country code and we’re keeping the name property. After this conversion, my 4.6MB world.json has come down to 604KB in world-topo.json.

When you load the file make sure the name specified in your JavaScript matches the name at the top of the topojson, e.g. countries.

Encountered an issue with something covering the background, but fixed with --stitch-poles false https://github.com/topojson/topojson/issues/242

