# Why Vector Tiles and Web-GL Based JavaScript Map Clients?

## MapBox Vector Tiles

MapBox Vector Tiles are a modern way of storing and transmitting the same sort
of feature data you might ordinarily find in a Shapefile, GeoJSON or TopoJSON
file.

There are two features of vector tiles that make them particularly interesting
as a source format for displaying data on maps:

* In preparing the tiles, the data for the features the tileset represents is
  chopped up into individual tiles. This means the data for a large feature like
  a coastline will no longer exist as a single complex shape, but instead each
  tile that needs to display a portion of it will contain just the information
  needed to render the part of the coastline it is responsible for.
* Mapbox Vector Tiles are a compact binary encoding of the vector data called
  [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) that
  is smaller than corresponding JSON, and usually smaller than a corresponding
  traditional raster tile too.

Because of these innovations, vector tiles have a major advantage over
non-tiled formats like GeoJSON and TopoJSON:

* Map clients only need to download the data for the tiles they need to
  display, not all the data for all features that have some part on the map (as
  they would with GeoJSON or TopoJSON)
* Even if source GeoJSON or TopoJSON was prepared as separate tiles, Vector
  tiles can be obtained by browsers more quickly because they are a more compact
  binary format so file sizes are lower.

Being a *vector* format (containing portions of the actual source data), vector
tiles have advantages over traditional *raster* tiles (just pictures of the
pre-coloured data) too:

* Map clients can access the vector data directly so have the ability to
  re-colour or style the data dynamically themselves without needing to load more
  data from the server (something that can't be done with raster tiles, which are
  just images of the data)

* Map client can zoom in and out smoothly between the different zoom levels
  because the data can be easily scaled while new data is loaded. This is known
  as *overzooming* or *underzooming*.
 
This all adds up to:

* A faster and smoother experience for the end user
* Lower bandwidth costs for the hosting
* The potential for building more advanced map applications that make use of
  the data transmitted

## WebGL-Based Map Clients

There are various map client libraries that can run in a browser or in a hybrid
mobile app (such as an Ionic 2 app or a React app). These include OpenLayers,
Leaflet and Mapbox GL JS.

Each of these clients uses different browser technologies to actually draw the
map. They might use SVG, Canvas or WebGL for example.

The advantage of libraries that use WebGL is that they can delegate the work to
the computer's GPU. The benefit of this is that modern GPUs can process data
very quickly which results in high performance for the user.

WebGL itself is very good at rendering animated 3D scenes, so by choosing a
WebGL as a basis for a map you are leaving the door open to more advanced
visualisations later.

At the moment we've found MapBox GL JS the most stable WebGL enabled map client
for rendering vector tiles. As an example there is a list of
[plugins](https://www.mapbox.com/mapbox-gl-js/plugins/) on the MapBox site.
[DECK.GL](http://uber.github.io/deck.gl/#/) is one such plugin worth looking
at.

There are also lots of options for [styling the vector
tiles](https://www.mapbox.com/mapbox-gl-js/style-spec/).


## Google Cloud Storage

Provides a place to host static files such as a MapBox Vector Tile tileset so
that they can be retrieved by a map client. The advantage over running your own
server is that Google will look after keeping the files accessible so that you
don't need hosting expertise or 24 hour monitoring and response to keep your
tiles available.
