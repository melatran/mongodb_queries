# What is MongoDB?
<img width="1022" alt="Screen Shot 2020-10-30 at 1 18 18 PM" src="https://user-images.githubusercontent.com/59414750/97748224-bf3ed900-1ab2-11eb-9dee-c0e082be86d1.png">

**mongoDB:** store lots of data and work with it; db server

- collections instead of tables

- inside collections, you have documents that look like JS objects to store data (JSON)

- no schemas with few relations

- data is stored together so when application is reaching out to db, it goes to collection A and mongodb queries through data quickly and finds the document with little to no merging

- popular with read, write applications (online shops, blogs, etc.)

# MongoDB Ecosystem
<img width="837" alt="Screen Shot 2020-10-30 at 1 26 00 PM" src="https://user-images.githubusercontent.com/59414750/97748675-76d3eb00-1ab3-11eb-9826-f2e019a313ca.png">

- Stitch is a serverless query API with serverless functions

- need to keep mongodb running on a separate terminal `mongo`

- to run mongodb as a macOS service, `brew services start mongodb-community@4.4` 

- to stop running mongodb, `brew services stop mongodb-community@4.4`

# Getting Started

- `use shop` creates a new db or access a db

- inserting an object `db.products.insertOne({name: "Super Junior LightStick", price: 60.00})`

- lets you know the object was created successfully and unique id was created

```
{
	"acknowledged" : true,
	"insertedId" : ObjectId("5fa062bf9a59858cd0ca5151")
}
```

- `db.products.find().pretty()` will list all objects in this db shop and pretty makes it easier to read

```
{
	"_id" : ObjectId("5f9c6de1f17164a140ed7df6"),
	"name" : "Super Junior LightStick",
	"price" : 60
}
{
	"_id" : ObjectId("5f9c6e4ff17164a140ed7df7"),
	"name" : "Dog Toy",
	"price" : 15
}
{
	"_id" : ObjectId("5f9c6e7ef17164a140ed7df8"),
	"name" : "Moose",
	"price" : 15,
	"description" : "A dog toy that looks like Sven"
}
{
	"_id" : ObjectId("5fa02d2c3f26b00951b84371"),
	"name" : "Waterbottle",
	"price" : 15,
	"description" : "Never be thirsty again"
}
```

- **delete db**

    - use "database name"

    -db.dropDatabase()

# Working with MongoDB + Clients (The Big Picture)
![Screen Shot 2020-11-02 at 9 26 34 AM](https://user-images.githubusercontent.com/59414750/97892686-922a3a80-1ced-11eb-96df-64d0f4a89377.png)

- The drivers interact with the mongodb server (started when running `mongod`)

- the mongodb server will not directly write files but will communicate with the storage engine; unless otherwise specified

      - default is Wired Tiger

- the mongodb server gets the query from the driver or shell and shovels info to the storage engine

- mongodb server talks to storage engine which manages the data and stores it in files(slow) and memory(fast) in between to allow you to work with data in a fast way
