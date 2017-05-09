# Tiler

Tiler is a user-story driven approach to providing a set of command line
tools for macOS for building your own Mapbox Vector Tile pipeline.

The key learning used by this project comes from [James
Milner's](https://loxodrome.io/) [Tiler](https://github.com/geovation/tiler)
project.

This README will be updated as [user stories tracked in
trello](https://trello.com/b/S3L3a35F/lean-tiler) are implemented.

# README

Tiler is a set of simple commands that you can use together extremely easily to
build your own maps for the web or for hybrid mobile applications on iOS and
Android.

In particular, Tiler can take shapefile or GeoJSON data and generate a
directory made up of [Mapbox Vector
Tiles](https://www.mapbox.com/vector-tiles/specification/) that you can copy to
a server to power a map.

Finally, Tiler has commands for helping you generate a map project that uses
the tiles you have generated.

* TODO: See a demo map covering London using Ordnance Survey Open Local
  data with multiple layers from a single tileset
* [Understand why Vector Tiles and WebGL-based mapping might be benefical to your project](FAB.md)

Note: This initial version of Tiler requires that you are running on macOS 10.12 and
have already installed [Homebrew](http://brew.sh).

## Developers

Tiler follows [the UNIX
philosophy](https://en.wikipedia.org/wiki/Unix_philosophy) of small programs
that each do one thing well and that can be composed into a flexible pipeline
for producing your vector tiles as well as the maps that use them.

Internally the `tiler` commands use [GDAL](http://www.gdal.org/) and
[tippecanoe](https://github.com/mapbox/tippecanoe) to perform much of the
legwork:

```
brew install git gdal tippecanoe
```

If you look in the `bin` directory, you'll see that a lot of the commands are
just thin wrappers around GDAL and tippecanoe so you can read the documentation
for those commands and tweak Tiler to build your own Mapbox Vector Tiles
pipeline.

## Getting Started

To get started, why not download some shapefiles for London to experiment with from
the [Ordnance Survey Open Local download
page](https://www.ordnancesurvey.co.uk/opendatadownload/products.html#OPMPLC)?

You'll need to tick the box to the right of the `National Grid Reference
squares` label, in the `Download` column, and then scroll down to select `TQ`
from the list next to the grid squares diagram. Scroll to the very bottom of
the page, click `Continue` and follow the instructions.

Repeat the process to download the `TR` grid square data too. (You can also
request them both at the same time by selecting them both to start with).

Note: Each download has a `data` directory containing different *shapefiles*
representing the different types of data available. Confusingly, each shapefile
comes as 4 separate files. For example, the shapefile for the roads in the `TQ`
square actually comes as `TQ_Road.shp`, `TQ_Road.shp`, `TQ_Road.shp` and
`TQ_Road.shp`. Tiler will automatically extract the data from the archives
you've downloaded as long as you use the correct name, in this case `Road`.

Now get hold of tiler:

```
git clone https://github.com/thejimmyg/tiler.git
cd tiler
```

and set up your path:

```
export PATH=$PWD/bin:$PATH
```

In order to get high resolution transforms from British National Grid into
WGS84 you also need to download the `OSTN02_NTv2.gsb` file from [OS
here](https://www.ordnancesurvey.co.uk/business-and-government/help-and-support/navigation-technology/os-net/ostn02-ntv2-format.html).
Extract the file and and place it in the `tiler` root directory.

Finally put the two zip files you've downloaded in a directory named `src`.

You are now ready to run tiler, to generate a roads map for London with the
tidal water too using the `tiler` command.

If you look at the `bin/tiler` file you'll see that it just calls other
`tiler-*` commands which themselves call `gdal` or `tippecanoe`. Feel free to
tweak the commands for your own use.

By default, the `bin/tiler` script generated gzip-compressed tiles, so to serve
them, you need a server that adds a `Content-Encoding: gzip` header to the HTTP
responses. This is great for statically hosting on S3 or Google Cloud Storage,
but if you want the raw tiles for testing, just add `--no-compression` to the
`tiler-tile` command.

Run the command like this:

```
printf "Road\nTidalWater\n" | time tiler src dst
```

The command you've just run will:

* Extract the files from the downloads in `src`
* Find the Road shapefiles from each grid square
* Merge them all togehter
* Generate a GeoJSON file
* Run `tippecanoe` with special options for generating a directory of Mapbox Vector Tiles
* Generate a sample Mapbox GL JS web app to use your tiles in the `dst/www` directory.

The default JavaScript conifg in `dst/www/app.js` is already set up to display
a map of London with the Road layer you've just built, but you can customise it
for other layers or locations referring to the [Mapbox GL JS style
documentation](https://www.mapbox.com/mapbox-gl-js/style-spec/).

You can now start a server (it doesn't have to be a Python one, any server that
serves static files such as S3, Nginx or Google Cloud Storage):

```
npm install -g live-server
cd dst
live-server --port=8000 --middleware="$PWD/gzip.js" --host=localhost www
```

Now visit your map at http://localhost:8000

Note: Please visit `localhost` rather than `127.0.0.1` or the browser might
block your tile requests because of the same origin policy.
