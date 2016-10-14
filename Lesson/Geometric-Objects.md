# Geometric Objects - Spatial Data Model

### Contents:

1. [Overview of geometric objects and Shapely -modules](#1-overview-of-geometric-objects-and-shapely--module)
2. [Point](#2-point)
  - [Creating a Point -object](#2-point)
  - [Point attributes and functions](#point-attributes-and-functions)
3. [LineString (i.e. a line)](#3-linestring)
  - [Creating a LineString -object](#3-linestring)
  - [LineString attributes and functions](#linestring-attributes-and-functions)
4. [Polygon](#4-polygon)
  - [Creating a Polygon -object](#4-polygon)
  - [Polygon attributes and functions](#polygon-attributes-and-functions)
5. [Pro -tips](#5-pro--tips-optional)
  - [Geometry collections and minimum bounding box](#geometry-collections)

### Sources

These materials are partly based on [Shapely -documentation](http://toblerity.org/shapely/manual.html) and to Lawhead 
  
## 1. Overview of geometric objects and Shapely -module

<img src="http://www.helsinki.fi/science/accessibility/maintenance/Kuvia/SpatialDataModel.PNG">
*Fundamental geometric objects that can be used in Python with <a href="http://toblerity.org/shapely/manual.html"><strong>Shapely</strong></a> module*


The most fundamental geometric objects are **Points**, **Lines** and **Polygons** which are the basic ingredients when working with spatial data in vector format. 
Python has a specific module called **[Shapely](http://toblerity.org/shapely/manual.html#)** that can be used to create and work with *Geometric Objects*. 
There are many useful functionalities that you can do with Shapely such as:

  - Create **Line**(s) or **Polygon**(s) from a _**Collection**_ of **Point** geometries
  - Calculate areas/length/bounds etc. of input geometries
  - Make geometric operations based on the input geometries such as **Union**, **Difference**, **Distance** etc.
  - Make spatial queries between geometries such **Intersects**, **Touches**, **Crosses**, **Within** etc.
  
**Geometric Objects consist of coordinate tuples where:**

  - **Point** -object represents a single point in space. Points can be either two-dimensional (x, y) or three dimensional (x, y, z). 
  - **LineString** -object (i.e. a line) represents a sequence of points joined together to form a line. Hence, a line consist of a list of at least two coordinate tuples 
  - **Polygon** -object represents a filled area that consists of a list of at least three coordinate tuples that forms the outerior ring and a (possible) list of hole polygons. 
  
**It is also possible to have a collection of geometric objects (e.g. Polygons with multiple parts):**
  
  - **MultiPoint** -object represents a collection of points and consists of a list of coordinate-tuples 
  - **MultiLineString** -object represents a collection of lines and consists of a list of line-like sequences 
  - **MultiPolygon** -object represents a collection of polygons that consists of a list of polygon-like sequences that construct from exterior ring and (possible) hole list tuples

## 2. Point

- Creating point is easy, you pass x and y coordinates into Point() -object (+ possibly also z -coordinate) :

```python
 # Import necessary geometric objects from shapely module
 from shapely.geometry import Point, LineString, Polygon

 # Create Point geometric object(s) with coordinates
 point1 = Point(2.2, 4.2)
 point2 = Point(7.2, -25.1)
 point3 = Point(9.26, -2.456)
 point3D = Point(9.26, -2.456, 0.57)
 
 # What is the type of the point?
 point_type = type(point1)
```
 - Let's see what the variables look like
 
 ```python
 >>> print(point1)
 POINT (2.2 4.2)
 
 >>> print(point3D)
 POINT Z (9.26 -2.456 0.57)
 
 >>> print(point_type)
 shapely.geometry.point.Point
```

We can see that the type of the point is shapely's Point which is represented in a specific format that is based on [GEOS](https://trac.osgeo.org/geos/) C++ library that is one of the standard libraries in GIS. It runs under the hood e.g. in [Quantum GIS](http://www.qgis.org/en/site/). 3D-point can be recognized from the capital Z -letter in front of the coordinates.

### Point attributes and functions

Point -object has some built-in attributes that can be accessed and also some useful functionalities. One of the most useful ones are the ability to extract the coordinates of a Point and calculate the Euclidian distance between points.

- Extracting the coordinates of a Point can be done in a couple of different ways

```python
 # Get the coordinates
 point_coords = point1.coords

 # What is the type of this?
 >>> type(point_coords)
 shapely.coords.CoordinateSequence
```

Ok, we can see that the output is a Shapely CoordinateSequence. Let's see how we can get out the actual coordinates:

```python
 # Get x and y coordinates
 xy = point_coords.xy

 # Get only x coordinates of Point1
 x = point1.x

 # Whatabout y coordinate?
 y = point1.y
```
 - What is inside?
 
 ```python
 >>> print(xy)
 (array('d', [2.2]), array('d', [4.2]))
 
 >>> print(x)
 2.2
 
 >>> print(y)
 4.2
 ```
Okey, so we can see that the our xy variable contains a tuple where x and y are stored inside of a numpy arrays. However, our x and y variables are plain decimal numbers.

- It is also possible to calculate the distance between points which can be useful in many applications
  - the returned distance is based on the projection of the points (degrees in WGS84, meters in UTM)

```python
 # Calculate the distance between point1 and point2
 point_dist = point1.distance(point2)
 
 >>> print("Distance between the points is {0:.2f} decimal degrees".format(point_dist))
 Distance between the points is 29.72 decimal degrees
```

## 3. LineString

- Creating a LineString -object is fairly similar to how Point is created. Now instead using a single coordinate-tuple we can construct the line using either a list of shapely Point -objects or pass coordinate-tuples:

```python
 # Create a LineString from our Point objects
 line = LineString([point1, point2, point3])

 # It is also possible to use coordinate tuples having the same outcome
 line2 = LineString([(2.2, 4.2), (7.2, -25.1), (9.26, -2.456)])
 ```
 - Let's see how our LineString looks like
 
 ```python
 >>> print(line)
 LINESTRING (2.2 4.2, 7.2 -25.1, 9.26 -2.456)
 
 >>> print(line2)
 LINESTRING (2.2 4.2, 7.2 -25.1, 9.26 -2.456)
 
 >>> type(line)
 shapely.geometry.linestring.LineString
```

Ok, now we can see that variable line constitutes of multiple coordinate-pairs and the type of the data is shapely LineString.

### LineString attributes and functions

LineString -object has many useful built-in attributes and functionalities. It is for instance possible to extract the coordinates or the length of a LineString (line), calculate the centroid of the line, create points along the line at specific distance, calculate the closest distance from a line to specified Point and simplify the geometry. See full list of functionalities from [Shapely documentation](http://toblerity.org/shapely/manual.html#). Here, we go through a few of them.

- We can extract the coordinates of a LineString similarly as with Point

```python
 # Get x and y coordinates of the line
 lxy = line.xy
 
 >>> print(lxy)
(array('d', [2.2, 7.2, 9.26]), array('d', [4.2, -25.1, -2.456]))
```

Okey, we can see that the coordinates are again stored as a numpy arrays where first array includes all x-coordinates and the second all the y-coordinates respectively.

- We can extract only x or y coordinates by referring to those arrays as follows

```python
 # Extract x coordinates
 line_x = lxy[0]

 # Extract y coordinates straight from the LineObject by referring to a array at index 1
 line_y = line.xy[1]

 >>> print(line_x)
 array('d', [2.2, 7.2, 9.26])
 
 >>> print(line_y)
 array('d', [4.2, -25.1, -2.456])
```

- We can get specific attributes such as lenght of the line and center of the line (centroid) straight from the LineString object itself

```python
 # Get the lenght of the line
 l_length = line.length

 # Get the centroid of the line
 l_centroid = line.centroid
 
 # What type is the centroid?
 centroid_type = type(l_centroid)
```

```python
 >>> print("Length of our line: {0:.2f}".format(l_length))
 Length of our line: 52.46
 
 >>> print("Centroid of our line: ", l_centroid)
 Centroid of our line:  POINT (6.229961354035622 -11.89241115757239)
 
 >>> print("Type of the centroid:", centroid_type)
 shapely.geometry.point.Point
```

Okey, so these are already fairly useful information for many different GIS tasks, and we didn't even calculate anything yet! 
These attributes are built-in in every LineString object that is created. Notice that the centroid that is returned is Point -object 
that has its own functions as was described earlier.

## 4. Polygon

- Creating a Polygon -object continues the same logic of how Point and LineString were created but Polygon object only accepts coordinate-tuples as input. Polygon needs at least three coordinate-tuples:

```python
 # Create a Polygon from the coordinates
 poly = Polygon([(2.2, 4.2), (7.2, -25.1), (9.26, -2.456)])

 # We can also use our previously created Point objects (same outcome)
 # --> notice that Polygon object requires x,y coordinates as input
 poly2 = Polygon([[p.x, p.y] for p in [point1, point2, point3]])
 
 # Geometry type can be accessed as a String
 poly_type = poly.geom_type
  
 # Using the Python's type function gives the type in a different format
 poly_type2 = type(poly)
```

- Let's see how our Polygon looks like

```python
 >>> print(poly)
 POLYGON ((2.2 4.2, 7.2 -25.1, 9.26 -2.456, 2.2 4.2))
 
 >>> print(poly2)
 POLYGON ((2.2 4.2, 7.2 -25.1, 9.26 -2.456, 2.2 4.2))
 
 >>> print("Geometry type as text:", poly_type)
 Geometry type as text: Polygon
 
 >>> print("Geometry how Python shows it:", poly_type2")
 shapely.geometry.polygon.Polygon
```

Notice that Polygon has double parentheses around the coordinates. This is because Polygon can also have holes inside of it. As the help of Polygon -object tells, 
a Polygon can be constructed using exterior coordinates and interior coordinates (optional) where the interior coordinates creates a hole inside the Polygon:

```python
 Help on Polygon in module shapely.geometry.polygon object:
 class Polygon(shapely.geometry.base.BaseGeometry)
  |  A two-dimensional figure bounded by a linear ring
  |
  |  A polygon has a non-zero area. It may have one or more negative-space
  |  "holes" which are also bounded by linear rings. If any rings cross each
  |  other, the feature is invalid and operations on it may fail.
  |
  |  Attributes
  |  ----------
  |  exterior : LinearRing
  |      The ring which bounds the positive space of the polygon.
  |  interiors : sequence
  |      A sequence of rings which bound all existing holes.
```

- Let's create a Polygon with a hole inside

```python
 # Let's create a bounding box of the world and make a whole in it

 # First we define our exterior
 world_exterior = [(-180, 90), (-180, -90), (180, -90), (180, 90)]

 # Let's create a single big hole where we leave ten decimal degrees at the boundaries of the world
 # Notice: there could be multiple holes, thus we need to provide a list of holes
 hole = [[(-170, 80), (-170, -80), (170, -80), (170, 80)]]

 # World without a hole
 world = Polygon(shell=world_exterior)

 # Now we can construct our Polygon with the hole inside
 world_has_a_hole = Polygon(shell=world_exterior, holes=hole)
```

- Let's see what we have now:

```python
 >>> print(world)
 POLYGON ((-180 90, -180 -90, 180 -90, 180 90, -180 90))
 
 >>> print(world_has_a_hole)
 POLYGON ((-180 90, -180 -90, 180 -90, 180 90, -180 90), (-170 80, -170 -80, 170 -80, 170 80, -170 80))
 
 >>> type(world_has_a_hole)
 shapely.geometry.polygon.Polygon
```

Now we can see that the polygon has two different tuples of coordinates. The first one represents the outerior and the second one represents the hole inside of the Polygon.

### Polygon attributes and functions

- We can again access different attributes that are really useful such as area, centroid, bounding box, exterior, and exterior-length of the Polygon

```python
 # Get the centroid of the Polygon
 world_centroid = world.centroid

 # Get the area of the Polygon
 world_area = world.area

 # Get the bounds of the Polygon (i.e. bounding box)
 world_bbox = world.bounds

 # Get the exterior of the Polygon
 world_ext = world.exterior

 # Get the length of the exterior
 world_ext_length = world_ext.length
```

 - Let's see what we have now

```python
 >>> print("Poly centroid: ", world_centroid)
 Poly centroid:  POINT (-0 -0)
 
 >>> print("Poly Area: ", world_area)
 Poly Area:  64800.0
 
 >>> print("Poly Bounding Box: ", world_bbox)
 Poly Bounding Box:  (-180.0, -90.0, 180.0, 90.0)
 
 >>> print("Poly Exterior: ", world_ext)
 Poly Exterior:  LINEARRING (-180 90, -180 -90, 180 -90, 180 90, -180 90)
 
 >>> print("Poly Exterior Length: ", world_ext_length)
 Poly Exterior Length:  1080.0
```

## Next steps ..

Now you know all of the basic geometrical objects that are used when doing GIS using vector data.

Next, you can **continue with the [Exercise 1]()** or read [Pro -tips materials]() where there is still some extra material concerning geometry collections and some useful attributes that can be used when dealing with geometric objects in Python using Shapely -module.

# 5. Pro -tips (optional)

This part is not obligatory but it contains some useful information related to construction and usage of geometry collections and some special geometric objects -such as bounding box.

## Geometry collections

In some occassions it is useful to store e.g. multiple lines or polygons under a single feature (i.e. a single row in a Shapefile represents more than one line or polygon object). 
Collections of points are implemented by using a MultiPoint -object, collections of curves by using a MultiLineString -object, and collections of surfaces by a MultiPolygon -object. 
These collections are not computationally significant, but are useful for modeling certain kinds of features. A Y-shaped line feature (such as road), or multiple polygons 
(e.g. islands on a like), can be presented nicely as a whole by a using MultiLineString or MultiPolygon accordingly. Creating and visualizing a minimum [bounding box](https://en.wikipedia.org/wiki/Minimum_bounding_box) 
e.g. around your data points is a really useful function for many purposes (e.g. trying to understand the extent of your data), here we demonstrate how to create one using Shapely. 

- Geometry collections can be constructed in a following manner:

```python
 # Import collections of geometric objects + bounding box
 from shapely.geometry import MultiPoint, MultiLineString, MultiPolygon, box

 # Create a MultiPoint object of our points 1,2 and 3
 multi_point = MultiPoint([point1, point2, point3])

 # It is also possible to pass coordinate tuples inside
 multi_point2 = MultiPoint([(2.2, 4.2), (7.2, -25.1), (9.26, -2.456)])

 # We can also create a MultiLineString with two lines
 line1 = LineString([point1, point2])
 line2 = LineString([point2, point3])
 multi_line = MultiLineString([line1, line2])

 # MultiPolygon can be done in a similar manner
 # Let's divide our world into western and eastern hemispheres with a hole on the western hemisphere
 # --------------------------------------------------------------------------------------------------

 # Let's create the exterior of the western part of the world
 west_exterior = [(-180, 90), (-180, -90), (0, -90), (0, 90)]

 # Let's create a hole --> remember there can be multiple holes, thus we need to have a list of hole(s). 
 # Here we have just one.
 west_hole = [[(-170, 80), (-170, -80), (-10, -80), (-10, 80)]]

 # Create the Polygon
 west_poly = Polygon(shell=west_exterior, holes=west_hole)

 # Let's create the Polygon of our Eastern hemisphere polygon using bounding box
 # For bounding box we need to specify the lower-left corner coordinates and upper-right coordinates
 min_x, min_y = 0, -90
 max_x, max_y = 180, 90

 # Create the polygon using box() function
 east_poly_box = box(minx=min_x, miny=min_y, maxx=max_x, maxy=max_y)

 # Let's create our MultiPolygon. We can pass multiple Polygon -objects into our MultiPolygon as a list
 multi_poly = MultiPolygon([west_poly, east_poly_box])
 ```

 - Let's see what do we have:
 
 ```python
 >>> print("MultiPoint:", multi_point)
 MultiPoint: MULTIPOINT (2.2 4.2, 7.2 -25.1, 9.26 -2.456)
 
 >>> print("MultiLine: ", multi_line)
 MultiLine:  MULTILINESTRING ((2.2 4.2, 7.2 -25.1), (7.2 -25.1, 9.26 -2.456))
 
 >>> print("Bounding box: ", east_poly_box)
 Bounding box:  POLYGON ((180 -90, 180 90, 0 90, 0 -90, 180 -90))
 
 >>> print("MultiPoly: ", multi_poly)
 MultiPoly:  MULTIPOLYGON (((-180 90, -180 -90, 0 -90, 0 90, -180 90), (-170 80, -170 -80, -10 -80, -10 80, -170 80)), ((180 -90, 180 90, 0 90, 0 -90, 180 -90)))
```

We can see that the outputs are similar to the basic geometric objects that we created previously but now these objects contain multiple features of those points, lines or polygons.

### Geometry collection -objects' attributes and functions

- We can also get many useful attributes from those objects:

```python
 # Convex Hull of our MultiPoint --> https://en.wikipedia.org/wiki/Convex_hull
 convex = multi_point.convex_hull

 # How many lines do we have inside our MultiLineString?
 lines_count = len(multi_line)

 # Let's calculate the area of our MultiPolygon
 multi_poly_area = multi_poly.area

 # We can also access different items inside our geometry collections. We can e.g. access a single polygon from
 # our MultiPolygon -object by referring to the index

 # Let's calculate the area of our Western hemisphere (with a hole) which is at index 0
 west_area = multi_poly[0].area

 # We can check if we have a "valid" MultiPolygon. MultiPolygon is thought as valid if the individual polygons 
 # does notintersect with each other. Here, because the polygons have a common 0-meridian, we should NOT have 
 # a valid polygon. This can be really useful information when trying to find topological errors from your data
 valid = multi_poly.is_valid
```
 
 - Let's see what do we have:
 
```python
 >>> print("Convex hull of the points: ", convex)
 Convex hull of the points:  POLYGON ((7.2 -25.1, 2.2 4.2, 9.26 -2.456, 7.2 -25.1))

 >>> print("Number of lines in MultiLineString:", lines_count)
 Number of lines in MultiLineString: 2
 
 >>> print("Area of our MultiPolygon:", multi_poly_area)
 Area of our MultiPolygon: 39200.0
 
 >>> print("Area of our Western Hemisphere polygon:", west_area)
 Area of our Western Hemisphere polygon: 6800.0
 
 >>> print("Is polygon valid?: ", valid)
 Self-intersection at or near point 0 -90
 Is polygon valid?:  False
```

From the above we can see that MultiPolygons have exactly the same attributes available as single geometric objects but now the information such as area calculates the area of 
ALL of the individual -objects combined. There are also some extra features available such as *is_valid* attribute that tells if the polygons or lines intersect with each other.
