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

- `db.products.find().pretty()` will list all objects in this db shop and pretty makes it easier to read

