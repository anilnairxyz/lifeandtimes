---
draft: false
date: 2016-09-18
slug: gis_distance
categories:
  - gis
tags:
  - gis
---

# Geographic distances

Geographic distances are those on spherical geometry, but not quite.

<!-- more -->

When dealing with IoT in the connected car space, most tasks require comparison between routes or segments of the journey ([lat, long] pair strings) for similarity. We also need accurate distance measurements to calculate higer order functions like speed and acceleration. There are two primary distances we need to compute when making sense of GPS traces - all others are a function of these two.

 * The first is the distance between two points and 
 * The second one is the minimum distance between a point and a line segment.

### Geographic Distance Approximations

The methods described below are summarized from [Movable Type Scripts](http://www.movable-type.co.uk/scripts/latlong.html).

### Equirectangular projection

In this method the geographic coordinates on the surface of the earth are projected onto an equirectangular plane and then the distances are measured as if on a plane surface. For distances of even a few hundred km this gives negligible errors compared to the more complicated haversine and should serve our purpose very well. Where this method fails is when we need to compute variables that are a function of more than one distance measure like the angle between segments. In this case the tiny errors magnify and make the resultant computation inaccurate and sometimes absurd.

```python
def equirectangular(pointA, pointB):
    """
    Find the distance between two points on earth.
    """
    R = 6371000
    latA = radians(pointA[1])
    lngA = radians(pointA[0])
    latB = radians(pointB[1])
    lngB = radians(pointB[0])
    x = (lngA - lngB)*cos((latA + latB)/2)
    y = (latA - latB)
    distance = R * sqrt(x*x + y*y)
    return distance
```

### Great circle distance or haversine 

The most popular approximation for geographic distances is to assume the earth to be a regular sphere and calculate distance using the haversine formula. We have felt the need to use this only in measuremnts of angles where we use the bearing of a segment computed using the haversine formula.

### Other functions based on geographic distance

### Minimum distance between a point and a line

Since we are projecting onto a plane, we use the regular 2D method of determining the height of the triangle with base as the line. We use Heron's formula to get the area and then the height - which is the minimum distance. Before doing this we check whether the projection of the point lies on the line segment or outside it. If outside we calculate the minimum distance as the minimum distance to the end points of the line.

```python
def check_projection(point, start, end):
    """
    Check whether the perpendicular projection of the point falls 
    on the line segment defined by start and end coordinates. 
    """
    dx = end[0] - start[0]
    dy = end[1] - start[1]
    dot = (point[0] - start[0])*dx + (point[1] - start[1])*dy
    return (0 < dot and dot < (dx*dx + dy*dy))

def point2segment(point, segment):
    """
    Find the minimum distance between a point and line segment
    """
    start = segment[0]
    end = segment[1]
    b = equirectangular(start, end)
    a = equirectangular(start, point)
    c = equirectangular(end, point)
    if not check_projection(point, start, end):
        min_distance = min(a, c)
    elif b == 0:
        min_distance = a
    else:
        s = (a+b+c)/2
        A = sqrt(max((s*(s-a)*(s-b)*(s-c)), 0))
        min_distance = 2*A/b
    return min_distance
```

### Hausdorff Distance

Hausdorff distance between the target route and a reference route is the distance of the point on the target route that is farthest away from the reference route.

### Angle between two line segments

```python
def angle_degrees(prev_pt, point, next_pt):
    """
    Find angle between two segments (three points)
    """
    latA = radians(prev_pt[1])
    lngA = radians(prev_pt[0])
    latB = radians(point[1])
    lngB = radians(point[0])
    latC = radians(next_pt[1])
    lngC = radians(next_pt[0])

    y1 = cos(latB)*sin(lngB-lngA)
    x1 = cos(latA)*sin(latB) - sin(latA)*cos(latB)*cos(lngB-lngA)
    bearing_1 = (degrees(atan2(y1, x1)) + 360) % 360

    y2 = cos(latC)*sin(lngC-lngB)
    x2 = cos(latB)*sin(latC) - sin(latB)*cos(latC)*cos(lngC-lngB)
    bearing_2 = (degrees(atan2(y2, x2)) + 360) % 360

    if (bearing_1 == 0) or (bearing_2 == 0):
        angle = 0
    else:
        angle = min((bearing_1-bearing_2+360)%360, (brng2-bearing_1+360)%360)
    return angle
```
