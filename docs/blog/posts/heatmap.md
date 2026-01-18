---
title: Stock heat-map
date: 2015-11-18
slug: heatmap
categories:
  - dataviz
tags:
  - d3
  - javascript
  - data visualization
draft: false
---

Heat maps are a popular way of visualizing data in a matrix where the color / brightness of the various cells indicate the strength of a prticular parameter.
Other aspects of the cells like their size and relative position can also be used to relay information to the user.

Heat maps are quite popular in the trading and investing worlds where they are used to indicate the 'hotness' of a stock or security based on parameters like price, volatility or volume.
In recent times heatmaps have also been used to indicate whether a particular stock is in the news or being mentioned in social media.

Our heat map is for stocks and it is rather simple. It is almost completely inspired by the [Zoomable Treemap](https://bost.ocks.org/mike/treemap) by Mike Bostock.

This heatmap has two levels of hierarchy:

* The 'SECTOR' wise heatmap
* The map of 'STOCKS' within the sector

The heatmap is generated using two parameters;

* The color is determined by the gain / loss of the stock during the trading day
* The size of the cells is determined by the relative market cap of the stock / sector

<iframe src="http://cdn.rawgit.com/anilnairxyz/754723d31dd14d50a3142e65d47c1057/raw/8a270174fb71903d1f15890c23adc40d6d29dfa3/index.html" marginwidth="0" marginheight="0" style="height:500px;width:900px;" scrolling="no"></iframe>
<br>

The code and its rendering can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/754723d31dd14d50a3142e65d47c1057) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/754723d31dd14d50a3142e65d47c1057)
