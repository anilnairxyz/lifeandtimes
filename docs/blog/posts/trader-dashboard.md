---
title: Trading dashboard using d3
date: 2015-11-20
slug: trader-dashboard
categories:
  - dataviz
tags:
  - d3
  - javascript
  - data visualization
draft: false
---

We have previously seen how to make a responsive candlestick chart using d3. The next step is to have multiple responsive charts arranged to form a dashboard such that every chart synchronously varies state depending on mouse hover, clicks and selections.

Here we demonstrate one such dashboard which has:

* various trading bands superimposed on the main candlestick chart
* bar charts on the side which indicate the level of these bands at various dates

The trading bands that we use for this demonstration are Bollinger Bands, Auto correlation and Cross correlation with the index.
These bar charts display the level of the bands (at 1.5 x std deviation) superimposed over the candlestick chart.

At the same time, the individual bars on the side reflect the values of Volume, Volatility and the the cumulative probability at the date over which the mouse hovers on the candlestick chart.

<iframe src="http://cdn.rawgit.com/anilnairxyz/9ec4b56f33ce0cd072b7f40504ed7858/raw/7aa14a4e4f99238142dbdfa212695116f9eba51a/index.html" marginwidth="0" marginheight="0" style="height:550px;width:1050px;" scrolling="no"></iframe>
<br>

The code and its rendering can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/9ec4b56f33ce0cd072b7f40504ed7858) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/9ec4b56f33ce0cd072b7f40504ed7858)
