---
title: Map of India - State Level
date: 2015-01-18
slug: map-of-india
categories:
  - gis
tags:
  - d3
  - gis
draft: false
---

Here we attempt to generate a map of India showing all the states and the union territories.

#### The source
For the state level administrative map of India, we use [Natural Earth](http://www.naturalearthdata.com). The steps needed to obtain an officially acceptable map of the state of Jammu and Kashmir are already outlined in a the post for [Map of J&K](map-of-jk.md).

#### Conversions
The shapefiles downloaded from Natural Earth are first converted to GeoJSON using [ogr2ogr](http://www.gdal.org/ogr2ogr.html).
```
ogr2ogr -f GeoJSON -where "ADM0_A3 IN ('IND')" \
        india_states.json shapefile.shp
```
The GeoJSON file can be converted to a [TopoJSON](https://github.com/topojson/topojson/wiki) format which is a more compressed one.

#### Rendition
The map is rendered using d3. 

<iframe src="https://cdn.rawgit.com/anilnairxyz/1ca20f47982712cf6d4128064e3a6feb/raw/8d3ea572852ed665a73af1eba07efe931c5e892e/index.html" marginwidth="0" marginheight="0" style="height:700px;width:800px;" scrolling="no"></iframe>

#### Source
The code and the results can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/1ca20f47982712cf6d4128064e3a6feb) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/1ca20f47982712cf6d4128064e3a6feb)
