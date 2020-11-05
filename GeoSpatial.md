# GeoSpatial Data

- to calculate geometry over Earth, store your location data as GeoJSON object

- to specify GeoJSON data, use an embedded documents with:

    - a field named *type* that spcifies the [GeoJSON Object](https://docs.mongodb.com/manual/reference/geojson/)

    - if specifying lat and lon coordinates, list the long first and then the lat

          - valid longitude values (-180 and 180)

          - valid latitude values (-90 and 90)

```
db.places.insertOne({name: "California Academy of Sciences", location: {type: "Point", coordinates: [-122.4724356, 37.7672544]}})

GeoJSON Object
{
	"_id" : ObjectId("5fa41ea3b3f64e64a56f6846"),
	"name" : "California Academy of Sciences",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
```

## Running Geo Queries
- `$near` is another mongodb operator for workign with geospatial data and you need a geospatial index

**THIS ERROR MESSAGE**

```
"error processing query: ns=wanderlust.placesTree: GEONEAR  field=location maxdist=1.79769e+308 isNearSphere=0\nSort: {}\nProj: {}\n planner returned error :: caused by :: unable to find index for $geoNear query"
```

**SOLUTION**

```
db.places.createIndex({location: "2dsphere"})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

- `2dsphere` indexes support queries that calculate geometires on an earth like sphere; if possible use this and not `$nearSphere` queries

#### Adding a Geospatial Index to Track the Distance

```
db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: [-122.471114, 37.771104]}, $maxDistance: 500, $minDistance: 10}}}).pretty()

{
	"_id" : ObjectId("5fa41ea3b3f64e64a56f6846"),
	"name" : "California Academy of Sciences",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
```

- `$near` returns geospatial objects in proximity to a point; requires a geospatial index; the 2dsphere and 2d indexes support $near

## Finding Places Inside a Certain Area
- I have this area, which points are located near this area

```
> const p1 = [-117.8915378, 33.8350101]
> const p2 = [-117.8938484, 33.8425616]
> const p3 = [-118.0026459, 33.8449008]
> const p4 = [-117.971695, 33.8580631]
> db.places.find({location: {$geoWithin: {$geometry: {type: "Polygon", coordinates: [[p1, p2, p3, p4], p1]}}}})
```

- instead of using a point, use a polygon to get availble places within that shape/distance

- `$geoWithin` selects geometries within a bounding geoJSON geometry; the 2dsphere and 2d indexes support geoWithin

    ```
    $geoWithin: {$geometry:...}            spherical
    $geoWithint: {$polygon:...}            flat
    ```

## Finding If User Is Inside a Specific Area
```
db.areas.insertOne({name: "Golden Gate Park", area: {type: "Polygon", coordinates: [[p1, p2, p3, p4, p1]]}})

db.areas.createIndex({area: "2dsphere"})

FIND NEARBY PLACES OR NEIGHBORHOODS
------------------------------------
db.areas.find({area: {$geoInterscts: {$geometry: {type: "Point", coordinates: [-122.490, 37.699]}}}})
```

- `geoIntersects` returns all areas that  have a common point/area

- does this point intersect with this area? if yes, the user is within that area

- can intersect two areas where geometry is polygon and find which neighborhood intersects

- use intersects when I have a point and I want to find everything that surrounds the point

## Finding Places Within a Certain Radius
- find all places within my area ($geoWithin)

- in this example, I have 5 different points in my db

      - Disneyland Resort, a hotel next to Disneyland, Knott's Berry Farm, a resturant near Knotts, and some random neighborhood in OC

      - I am at Disney District and want to find places within my area

`propertyName: {$geoWithin: {$centerSphere: [[lon, lat], mile/km conversion to radians]}}`

- click [here](https://docs.mongodb.com/manual/tutorial/calculate-distances-using-spherical-geometry-with-2d-geospatial-indexes/) for the conversion to radians

```
db.places.find({location: {$geoWithin: {$centerSphere: [[-117.9258509, 33.8091327], 1 /6378.1]}}})

{
	"_id" : ObjectId("5fa42877b3f64e64a56f6850"),
	"name" : "Anaheim Hotel",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-117.9186566,
			33.8065587
		]
	}
}
{
	"_id" : ObjectId("5fa42886b3f64e64a56f6851"),
	"name" : "Disneyland Resort",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-117.9250571,
			33.8091019
		]
	}
}
```

- `$centerSphere` allows you to quickly get a circle around the point

## Practice
1. pick 3 points on google Maps and store them in a collection

2. pick a point and find nearest points with a min and max distance

3. pick an area and see which points it contains and store at least one area in a different collection

4. pick a point and find out which areas in your collection contain that point

**TASK 1**

```
db.places.insertOne({name: "Tony Pho Vietnamese Restaurant", location: {type: "Point", coordinates: [-105.0268483, 39.6984034]}})

db.places.insertOne({name: "Dairy Queen", location: {type: "Point", coordinates: [-105.0250577, 39.7115957]}})

db.places.insertOne({name: "Hammond's Candies", location: {type: "Point", coordinates: [-104.9866825, 39.8035332]}})

const myLocation = [-105.0244773, 39.697842]

db.places.createIndex({location: "2dsphere})
```

**TASK 2**

```
THIS LISTS FROM NEAREST TO FARTHEST ON EVERYTHING IN DB
--------------------------------------------------------
db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: myLocation}}}}).pretty()

THIS LISTS PLACES NEAR A MAX AND MIN DISTANCE (max and and min in meters)
---------------------------------------------
db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: myLocation}, $minDistance: 1000, $maxDistance: 2000}}}).pretty()

{
	"_id" : ObjectId("5fa4375ab3f64e64a56f6858"),
	"name" : "Dairy Queen",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-105.0250577,
			39.7115957
		]
	}
}
```

**TASK 3**

```
> const pla1 = [-105.0286458, 39.698834]
> const pla2 = [-105.0291688, 39.7012237]
> const pla3 = [-105.024129, 39.6997379]
> const pla4 = [-105.0223976, 39.697703]
> const polygonArea0 = [[pla1, pla2, pla3, pla4, pla1]]
> const polyObj = {type: "Polygon", coordinates: polygonArea0}
> db.places.find({location: {$geoWithin: {$geometry: polyObj}}}).pretty()
```

**TASK 4**

```
db.areas.find({area: {$geoIntersects: {$geometry: {type: "Point", coordinates: [-105.0268483, 39.6984034]}}}})
```

## Summary
<img width="968" alt="Screen Shot 2020-11-05 at 12 53 46 PM" src="https://user-images.githubusercontent.com/59414750/98289861-0a9b3080-1f66-11eb-94a4-482d3e922769.png">

- $geoWithin returns an unsorted list and you just return a list of places around that area; can use sort method to dictate what results returns first

- $near sorts by proximity with the nearest first

