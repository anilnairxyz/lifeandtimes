---
title: Candlestick chart using d3
date: 2015-10-12
slug: candlestick
categories:
  - dataviz
tags:
  - d3
  - javascript
  - data visualization
draft: false
---

Candlestick charts are a style of financial charts which are useful in visualising price movements in stocks, commodities or currencies. It is probably the most commonly used chart among traders because several technical trading patterns are more easily visible on these charts compared to others.

<!-- more -->

Each 'candle' represents the open, high, low and closing prices of a stock or currency for a particular period of time, typically a trading day. However other intervals of time are also valid. 
These are a combination of line charts and bar charts. 

The candlestick chart that we set out to render include:

* A selector to select the period of display (as the periods become longer the chart becomes a weekly and later monthly one)
* A panel displaying the OHLC values of the stock at the date over which the mouse hovers
* The main candlestick chart
* Bar charts for Volume and Volatility

![](../images/candlestick.webp)
<br>

The basic candlestick chart here is made of three overlapping bar charts:

* The first displays the body of the candles (determined by OPEN and CLOSE prices). Notice how the offset of the candle is the higher of OPEN and CLOSE values while the height of the candle is the absolute difference of the two. We also colour the candles and the wicks based on whether the price has risen or fallen during the day by using appropriate classes in css.

```javascript
candle.selectAll("rect")
    .data(function(d) { return d; })
  .enter().append("rect")
    .attr("y", function(d) {return y(d3.max([d.OPEN, d.CLOSE]));})
    .attr("height", function(d) { 
          return y(-Math.abs(d.OPEN - d.CLOSE));})
    .classed("rise", function(d) { return (d.CLOSE>d.OPEN); })
    .classed("fall", function(d) { return (d.OPEN>d.CLOSE); });
```

* The second bar chart displays the wicks of the candle (determined by HIGH and LOW prices). Here the offset of the wick is determined by the HIGH price while the height is the difference between HIGH and LOW prices.

```javascript
wick.selectAll("rect")
    .data(function(d) { return d; })
  .enter().append("rect")
    .attr("y", function(d) { return y(d.HIGH); })
    .attr("height", function(d) { return y(d.LOW) - y(d.HIGH); })
    .classed("rise", function(d) { return (d.CLOSE>d.OPEN); })
    .classed("fall", function(d) { return (d.OPEN>d.CLOSE); });
```

* The third bar chart is hidden and it used to show effects and information upon mouse hover. It has a fixed height.

```javascript
bands.selectAll("rect")
    .data(function(d) { return d; })
  .enter().append("rect")
    .attr("y", 0)
    .attr("height", Bheight)
```

The code and its rendering can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/a51393d7c51342abe8d4e3f4cbab7ae1) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/a51393d7c51342abe8d4e3f4cbab7ae1)
