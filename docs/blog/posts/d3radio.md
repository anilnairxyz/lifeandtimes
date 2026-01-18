---
title: Transitioning maps with d3
date: 2015-08-21
slug: d3radio
categories:
  - dataviz
tags:
  - d3
  - data visualization
draft: false
---

When displaying maps using d3, we often need to transition the map between multiple selections.
In some of our previous posts on cartograms and choropleth maps we do so using the standard radio buttons.
This post provides a brief template for transitioning maps based on user selected parameters.
Ideally we need the maps to transition instantaneously on the user toggling the radio button.

<!-- more -->

So what exactly does this flow look like. We prefetch all the data - which could simply be multiple columns in a csv file.
Initially we render the default map based on the default checked radio button.
Here we have used a function `selectFilter` to render the radio buttons.

``` javascript
// Radio HTML
d3.select("#select").call(selectFilter());

function selectFilter() {
 function render(selection) {
   selection.each(function() {
     d3.select(this).html("<form>"+
        "<input type='radio' name='gen' value='A' checked> ALL<br>"+
        "<input type='radio' name='gen' value='F'> FEMALE<br>"+
        "<input type='radio' name='gen' value='M'> MALE"+
        "</form>");
   });
 } // render
return render;
} // selectFilter
```

Based on the selected filter we then map the colours of the various subunits of the map. Here we do this using the function `colorCode`.

``` javascript
// Color codes for districts based on Literacy Rates
var filter  = d3.select('#select input[name="gender"]:checked')
                       .node().value;
colorCode(districts.features, filter);

function colorCode(data, filter) {
  var color = d3.scale.threshold()
                .domain([65, 72, 78, 85])
                .range(["#111", "#222", "#333", "#444", "#555"]);
  data.forEach(function(d) { 
      if (isNaN(d.properties[filter])) {d.properties[filter] = 77;}
      d.color = color(d.properties[filter]);
  });
}
```
Then we render the map by calling our main rendering functions.
``` javascript
// Map render
var map     = districtMap(districts, disputed)
                         .width(800).height(700)
                         .scale(1200).propTag(filter);
d3.select("#map").call(map);
```
On the user toggling the radio button, we change the colours of the various subunits and re-render the map.
``` javascript
// On change of selection re-render
d3.selectAll("#select input[name=gender]")
  .on("change", function() {
    filter  = d3.select('#select input[name="gender"]:checked')
                .node().value;
    colorCode(districts.features, filter);
    map     = districtMap(districts, disputed)
                         .width(800).height(700)
                         .scale(1200).propTag(filter);
    d3.select("#map").call(map);
});
```
The code above is used in the cartogram at [**GitHub**](https://gist.github.com/anilnairxyz/11190f144a89b54c6698699f3a83b315) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/11190f144a89b54c6698699f3a83b315)
