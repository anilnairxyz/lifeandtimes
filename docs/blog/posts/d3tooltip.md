---
title: Tooltips in d3 SVGs
date: 2015-08-09
slug: d3tooltip
categories:
  - dataviz
tags:
  - d3
  - data visualization
draft: false
---

In some of our previous posts on cartograms and choropleth maps we were able to display dynamic tooltips using d3.
D3 being a visualization package, well designed tooltips provide a way to pack a lot more information without being overly intrusive.
Suitable transitions can also be built into the tooltip generation to aid in the smoothness of the view.
This post provides a quick template for generating those tooltips.
Here the `div` which contains our SVG is named `map`.
The javascript to generate the tooltip with appropriate transitions:

``` javascript
selectRect
    .on("mouseover", function(d) {      
           d3.select("#tooltip").transition()        
              .duration(200)      
              .style("opacity", .9);      
           d3.select("#tooltip").html("<h3>"+(d.name)
              +"</h3><table>"
              +"<tr><td>"+parameter+"</td><td>"
              +(d[parameter])+"</td></tr>"
              +"</table>")
              .style("left",
                     (d3.event.pageX-document.getElementById('map')
                      .offsetLeft + 20) + "px") 
              .style("top",
                     (d3.event.pageY-document.getElementById('map')
                      .offsetTop - 60) + "px");
    })  
    .on("mouseout", function(d) {       
           d3.select("#tooltip").transition()        
              .duration(500)      
              .style("opacity", 0);   
    });
```
<br>
The styling can be done using the follwoing css.

```css
#tooltip h3 {
        margin:2px;
        font-size:14px;
}
#tooltip h4 {
        margin:2px;
        font-size:10px;
}
#tooltip {
        position: absolute;           
        background:rgba(0,0,0,0.8);
        text-align: left;
        border:1px;
        border-radius:5px;
        font: 12px sans-serif;        
        width:auto;
        padding:4px;
        color:white;
        opacity:0;
        pointer-events: none;         
}
#tooltip table{
        table-layout:fixed;
}
#tooltip tr td{
        padding:2px;
        margin:0;
}
```

And here is how it would appear on a map.

![](../images/tooltips.png)


The code above is used in the cartogram at [**GitHub**](https://gist.github.com/anilnairxyz/79ec2746e0c5468edb3a85e5e4da1595) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/79ec2746e0c5468edb3a85e5e4da1595)
