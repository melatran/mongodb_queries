# Understanding the Basics and CRUD Operations
<img width="691" alt="Screen Shot 2020-11-02 at 9 35 01 AM" src="https://user-images.githubusercontent.com/59414750/97893588-b5a1b500-1cee-11eb-8576-333c61091bc1.png">

- documents are the data pieces you are storing the db

- shell will not be used when building deployable apps and will be built in the drivers

- run `sudo brew services start mongodb-community` on my computer for mongo to work

- run `show dbs` to see list of databases

- `db.collectionName.queryCommands({data passed we want to store with key value pairs})` to create JSON documents/objects

# CRUD Operations and MongoDB
<img width="749" alt="Screen Shot 2020-11-02 at 12 55 15 PM" src="https://user-images.githubusercontent.com/59414750/97912891-b9433500-1d0a-11eb-95d9-554a58eb8629.png">

<img width="749" alt="Screen Shot 2020-11-02 at 1 08 35 PM" src="https://user-images.githubusercontent.com/59414750/97914072-87cb6900-1d0c-11eb-9cc0-2c9f91c74d66.png">

- commands are executed on the collection(flightData)

# Delete and Create Data

- to delete one document `db.flightData.deleteOne({departureAirport: "DEN"})` enter the argument in the {} and it will delete the first document that returns truthy

- `$set:{}` sets this value so if it doesn't exist, it will create a new field

- to add a new field to all documents `db.flightData.updateMany({}, {$set:{marker: 'toDelete'}})`

- to delete many documents `db.flightData.deleteMany({marker: "toDelete"})`

- to insertMany documents at once, it takes in an array

```
db.flightData.insertMany([
  {
    "departureAirport": "MUC",
    "arrivalAirport": "SFO",
    "aircraft": "Airbus A380",
    "distance": 12000,
    "intercontinental": true
  },
  {
    "departureAirport": "LHR",
    "arrivalAirport": "TXL",
    "aircraft": "Airbus A320",
    "distance": 950,
    "intercontinental": false
  }
])
```

# Finding Data

- pass the filter in the {}

- run `db.flightData.find({departureAirport: "LHR"}).pretty()` and it will return a subset of the data

- run `db.flightData.find({distance: {$gt: 900}}).pretty()` to find flights with a distance greater than 900; equivalent to find_all in ruby

- run `db.flightData.findOne({distance: {$gt: 900}})` to find the first document that evaluates true; pretty does not work with findOne

- `db.passengers.find()` does not give an array of documents in collection but returns a cursor object with meta data to cycle through results

- `db.passengers.find().toArray()` will fetch all documents and not stop after 20 documents

- `db.passengers.find().forEach((passengerData)=> {printjson(passengerData)})` this will be the command for JS to execute (different depending on the type of driver)

      - cursor will go and fetch through all documents and execute the function and print it out as JSON, render it as HTML, etc.

      - forEach with large sets of data; fetches data on demand and not overusing bandwidtch and loading to memory

      - `pretty` is a method that exists on the cursor so it doesn't work

      - cursor objects do not exist on update, create, delete

# Updating Data

- to update one document such as `PATCH` to just update the data and add a new field `db.flightData.update({_id: ObjectId("5fa06a589a59858cd0ca5155")}, {$set: {delayed: true}})`

- this command is similar to `PUT` and replace the data `db.flightData.update({_id: ObjectId("5fa06a589a59858cd0ca5155")}, {delayed: true})`

- this command will replace data (safer way)

```
db.flightData.replaceOne({_id: ObjectId("5fa06a589a59858cd0ca5155")}, {
 "departureAirport": "MUC",
 "arrivalAirport": "SFO",
 "aircraft": "Airbus A380",
 "distance": 12000,
 "intercontinental": true
})
```

# Projection
- filter on mongodb server before being shipped back

- only get back what you need from the server

- `db. passengers.find({}, {name: 1, _id: 0}).pretty()` where 0 means don't show this field to me

# Embedded Documents

- up to 100 levels of nesting with documents max 16mb

- updating current data to make it embedded `db.flightData.updateMany({}, {$set: {status: {description: "on-time", lastUpdated: "1 hour ago", details:{responsible: "MoMo"}}}})`

```
{
	"_id" : ObjectId("5fa06a589a59858cd0ca5155"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true,
	"status" : {
		"description" : "on-time",
		"lastUpdated" : "1 hour ago",
		"details" : {
			"responsible" : "MoMo"
		}
	}
}
{
	"_id" : ObjectId("5fa06a589a59858cd0ca5156"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastUpdated" : "1 hour ago",
		"details" : {
			"responsible" : "MoMo"
		}
	}
}
```

# Arrays of Data

- arrays of embedded documents (list of data in documents)

- `db.passengers.updateOne({name: "Albert Twostone"}, {$set: {hobbies: ["cooking", "sports"]}})` will show a list of embedded data

```
{
	"_id" : ObjectId("5fa06e169a59858cd0ca516a"),
	"name" : "Albert Twostone",
	"age" : 68,
	"hobbies" : [
		"cooking",
		"sports"
	]
}
```

# Access Structured Data

- `db.passengers.findOne({name: "Albert Twostone"}).hobbies` to list out hobbies

- `db.passengers.find({hobbies: "sports"}).pretty()` to find all passengers with sports as a hobby

- to access nested embedded documents `db.flightData.find({"status.description": "on-time"}).pretty()`

- `.` notation allows you to look at embedded documents and access them

# Summary

- Database holds multiple collections where each collection can then hold multiple documents

- db and collections are created lazily( when a document is inserted)

- a document can't be directly inserted into a db- need to use a collection

- each document needs a unique id

- use filters and operators to limit the number of documents you retrieve; indicated by `$`