About half a year ago I developed a simple data visualization [tool](https://github.com/jootse84/bubble-map) for the representation of data based on countries.

The tool is in charge of a world map render in an HTML container, where the data corresponding to each country is displayed represented by a bubble atop every country in the map.

I quite enjoyed working in this idea, so to celebrate *its anniversary* I decided to write this post about that work, which will help me review the concepts and theory I learnt on that time.


# GeoJSON, TopoJSON and d3-geo

During my research at the time I started the project, the first concept I bump into was [GeoJSON](http://geojson.org/), which is probably one of the basic things you should know first when developing a tool like this.

GeoJSON is a standard open format based on JSON and used to represent simple graphical elements. It supports the following geometric types:

* Point / Multipoint (which can be used for addresses, locations, points of interest, etc).
* LineString / MultiLineString (can represent streets, roads, borders, etc).
* Polygon / MultiPolygon (countries, states, etc).

[TopoJSON](https://github.com/mbostock/topojson) is an extension of GeoJSON that encodes topology, it uses the previous mentioned geometric types, but offering compact representations that saves around 80% of file space.

On top of that, I also discovered [d3-geo](https://github.com/d3/d3-geo) that provides, among other features, with the proper tools for **map projections** (the transformation of latitudes and longitudes on the surface of a sphere into locations on a plane).

The render of the tool I developed it is basically an extraction of a world map topology via TopoJSON. Then, from the features provided by [d3](https://d3js.org/) and [d3-geo projections](https://github.com/d3/d3/wiki/Geo-Projections) the tool creates the visualization of the desired map.


# Preparing the Data

At this point, we already choose the environment we need to develop our tool, but we still miss the very basic: the world map topology.

A **map topology** creates topological relationships between the parts of features that are coincident, which allows you to simultaneously edit features that share geometry.

Finding the topology of the map can be tedious if you need to research from scratch. For the ones with experience in the area, the most convenient source of free geographic data is [Natural Earth](http://www.naturalearthdata.com/), a portal that provides public domain map dataset in different scales.

The geographic data files you find in Natural Earth are usually too large to use or manually treat. As the tool I developed did not cover [thematic mapping](https://en.wikipedia.org/wiki/Thematic_map), I had to modify the map to light the size of the file.

I used [ogr2ogr](http://www.gdal.org/ogr2ogr.html) open-source binary from [osgeo](http://www.osgeo.org/) to manipulate the world map file shapefiles, and convert it into GeoJSON: 

```script
ogr2ogr -f GeoJSON -simplify 0.2 -where "SU_A3 <> 'ATA'" your_new_local_file.json your_shapefile_from_natural_earth.shp
```

The previous script excludes the Antarctica as a country (*select all countries that its attribute "SU_A3" is different than 'ATA'*), as it is not really necessary for the tool. In addition, it uses the tag *-simplify* to smooth polygons and reduce the file size significantly.

Afterwards I converted the file from GeoJSON to TopoJSON format:

```script
topojson --id-property SU_A3 -p name=NAME -p name -o your_new_topojson_file.json your_geojson_file.json
```

At the time we do this conversion, we can select which attributes are the ones we need for our tool. The above commad selects only the attributes *NAME* and *SU_A3*.

Once we have ready  data, it is time to create the visualization of our map with d3. You can directly check the code of the [tool I developed on github](https://github.com/jootse84/bubble-map), or rather continue reading this post, as I will introduce some new concepts that can be useful if you are planning to create a similar map visualization.


## How to print the map

First, we need to choose and initialize our [projection](https://github.com/d3/d3-3.x-api-reference/blob/master/Geo-Projections.md) with d3-geo.

The projection we choose will provide the final representation of a location coordinates in a plane. From the [longitude, latitude], the projection will return a similar two-element array representing the projected pixel position [x, y].

```javascript
var projection = d3.geo.mercator();
var path = d3.geo.path();
var tile = d3.geo.tile();

projection
  .scale(150)
  .translate([width / 2, height / 1.45]);

path.projection(projection);

tile
  .scale(projection.scale() * 2 * Math.PI)
  .translate(projection([0, 0]))
  .zoomDelta((window.devicePixelRatio || 1) + 2.5);
```

In the previous example we use the projection in combination with **tiles** (typically 256×256 pixel images that represents pieces of map), to layout the map by using standard map tiles.

On the other hand, **d3.geo.path()** creates a new geographic path generator with default settings. The previous code sets the projection used by the path generator to the specified projection function (mercator, in this case).

## Load our TopoJSON data and print tiles

This is the way we can load our data with d3 and topojson, and print our tiles by using any provider (e.g. [MapBox](https://www.mapbox.com/) tiles):

```javascript
d3.json('your_topojson_file.json', function (error, data) {

    var topodata = topojson.feature(data, data.objects.countries).features;
    var tiles = tile();

    our_d3_svg_element
        .append("g")
        .attr("id","image-list")
        .attr("clip-path", "url(#clip)")
        .selectAll("image")
        .data(tiles)
      .enter()
        .append("image")
        .attr("xlink:href", (d) => {
            return "mapbox_tiles_url" + d[2] + "/" + d[0] + "/" + d[1] +
              ".png?access_token=" + our_mapbox_access_token;
        })
        attr("width", tiles.scale + 1)
        .attr("height", tiles.scale + 1)
        .attr("x", (d) => {
            return (d[0] + tiles.translate[0]) * tiles.scale;
        })
        .attr("y", (d) => {
            return (d[1] + tiles.translate[1]) * tiles.scale;
        });

}.bind(this));
```
