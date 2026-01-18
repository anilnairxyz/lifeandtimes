---
title: Brickwork Cartogram for relative influence visualizations
date: 2015-07-14
slug: cartogram
categories:
  - dataviz
tags:
  - d3
  - gis
  - javascript
  - data visualization
draft: false
---

Most cartograms that you find on the web are continuous area cartograms like [**this excellent one by Max Galka**](http://metrocosm.com/world-population-history-map/). Although it has many positives, the problem with this form of the cartogram is that the shapes are distorted and the smaller components are difficult to observe. The relative areas are not clearly visible since the rendition has to remain loyal to the original shape of the components to a certain extent. On the other hand it is better than simple scaling of the components as it makes more use of the visible areas.

<!-- more -->

What if we could completely do away with the shapes and render only the relative areas of the components. The relative positions of the components need to remain the same for quick identification of the components. Here we make one such attempt using a **brickwork** cartogram.

#### Input format
For the input we use:

  * A csv file to input the relevant parameter - in our case population / GDP etc
  * A json file which gives the relative position of the components in the rendition - our module implements a custom protocol

#### Cartogram
The d3 module then sizes the individual components based on the parameter values provided in the csv file and positions them based on the values in the json file. Since the positions are relative, the resultant rendition is compact and does not have any spaces between the components.

#### Rendition

This sample is based on the map of India with the cartograms showing the relative influence of different states based on parameters like population, GDP, Lok Sabha seats and Rajya Sabha seats.

![](../images/cartogram.gif)

#### Source
The code and the results can be viewed at [**GitHub**](https://gist.github.com/anilnairxyz/79ec2746e0c5468edb3a85e5e4da1595) or [**bl.ocks**](https://bl.ocks.org/anilnairxyz/79ec2746e0c5468edb3a85e5e4da1595)
