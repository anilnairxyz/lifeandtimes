---
title: Choropleth map using d3
date: 2015-05-11
slug: choropleth
categories:
  - dataviz
tags:
  - d3
  - gis
  - javascript
  - data visualization
draft: false
---

Choropleth maps are thematic maps in which areas are shaded according to the strength of a particular feature. Its is a good visualisation to quickly differentiate different regions based on the the property of interest.

<!-- more -->

Here we generate a choropleth map showing district-wise male and female literacy. This is based on similar maps by [Mike Bostock](https://bl.ocks.org/mbostock/4060606). The district map of India is from [DIVA GIS](http://www.diva-gis.org/) while the disputed areas of Jammu and Kashmir have been merged from [Natural Earth](http://www.naturalearthdata.com/). The district-wise literacy data is that of the [2011 Census](http://censusindia.gov.in/).


![](../images/choropleth.webp)
<br>

The geojson / topjson polygons are rendered to the svg element using d3.

``` javascript
svg.selectAll(".district")
            .data(districts.features)
          .enter().append("path")
            .attr("class", "district")
            .style("fill", function(d) { return d.colour; })
            .attr("d", path)
```
The colour filling is done by using d3.scale.threshold() function.
```javascript
function colorCode(data, filter) {
    var colour = d3.scale.threshold()
                  .domain([65, 72, 78, 85])
                  .range(["#111", "#222", "#333", "#444", "#555"]);
    data.forEach(function(d) { 
        if (isNaN(d.properties[filter])){d.properties[filter]=77;}
        d.colour       = colour(d.properties[filter]);
    });
}
```
The map above clearly shows the regions where the literacy rate is high concentrated around the western coast and some states in the north east. The differences are even more stark when we see the female literacy rate alone.

Here we see that only the state states of Kerala, Mizoram and Tripura have very high literacy rates.

The code and the results can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/11190f144a89b54c6698699f3a83b315)
