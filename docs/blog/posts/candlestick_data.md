---
title: Input data wrangling in d3
date: 2015-11-05
slug: candlestick_data
categories:
  - dataviz
tags:
  - d3
  - javascript
  - data visualization
draft: false
---

Very often we may need to process input data before visualizing it using d3. 
This is particularly true when we need to show charts over different time frames.
We encountered a similar problem when we tried to generate a candlestick chart. One of the features that we wanted built-in there was the abilility to view the chart over different time frames.
But when we attempt to display charts over a wide range of time like 2 years or 4 years, our candlesticks bars become very thin and are indistinguishable lines. What we need is to do is to convert the chart into a weekly chart when the range extends beyond 6 months and then to convert it into a monthly chart when the range extends beyond 2 years.

Fortunately this can be conveniently acccomplished using the d3.rollup function.

First the csv file containing the stock price and other information is read using d3.csv and the data is preprocessed using an accessor function (here genType) and the returned data object is passed on to the callback function.

```javascript
(function() {
  d3.csv("stockdata.csv", genType, function(data) {
    compressed_data = dataCompress(data, "week")
    candlestickMain(compressed_data);
  }); 
}());
```
The accessor function simply parses the data and typecasts the values appropriately.

```javascript
function genType(d) {
  d.TIMESTAMP  = parseDate(d.TIMESTAMP);
  d.LOW        = +d.LOW;
  d.HIGH       = +d.HIGH; 
  d.OPEN       = +d.OPEN;
  d.CLOSE      = +d.CLOSE;
  d.TURNOVER   = +d.TURNOVER;
  d.VOLATILITY = +d.VOLATILITY;
  return d;
}
```
The dataCompress function then compresses the data and sets the timestamps according to the range. It uses the rollup function in d3 to aggregate the data columns over the given time period (week or month). For example in the case of our candlestick chart, the aggregate open price is the price at the beginning of the interval and therefore we use the d3.shift function to get it. The CLOSE price is the one at the end of our interval and so we use d3.pop. For HIGH we use the d3.max and for LOW we use d3.min functions respectively. For columns like VOLATILITY and TURNOVER we use mean values. 

One other problem here is the label we provide to the x-axis (timestamp). This we accomplish using the timeCompare function shown below. We use the date on the Monday for weekly rollups and the first day of the month in case of a monthly rollup for our example.

```javascript
function dataCompress(data, interval) {
  var compressedData  = d3.nest()
      .key(function(d) { return timeCompare(d.TIMESTAMP, interval); })
      .rollup(function(v) { return {
         TIMESTAMP:   timeCompare(d3.values(v).pop().TIMESTAMP, interval),
         OPEN:        d3.values(v).shift().OPEN,
         LOW:         d3.min(v, function(d) { return d.LOW;  }),
         HIGH:        d3.max(v, function(d) { return d.HIGH; }),
         CLOSE:       d3.values(v).pop().CLOSE,
         TURNOVER:    d3.mean(v, function(d) { return d.TURNOVER; }),
         VOLATILITY:  d3.mean(v, function(d) { return d.VOLATILITY; })
      }; })
      .entries(data).map(function(d) { return d.values; });
  return compressedData;
}

function timeCompare(date, interval) {
  if (interval == "week")       { var xtick = d3.time.monday(date); }
  else if (interval == "month") { var xtick = d3.time.month(date); }
  else { var xtick = d3.time.day(date); } 
  return xtick;
}
```
The rendering is clean and intuitive.

![](../images/candlestick_data.png)
The entire code and its rendering can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/a51393d7c51342abe8d4e3f4cbab7ae1) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/a51393d7c51342abe8d4e3f4cbab7ae1)
