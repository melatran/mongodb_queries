# Diving Into Create Operations

- if you have an insert without writing an object id, it would be difficult to search and locate that person for example when using `db.person.insert`

- better to use insertOne and insertMany to prevent errors and for readability

- could create your owns `_.id` but they cannot be duplicated so you wouldn't be able to have two documents with an id of `baby`

# Atomicity
- mongodb guarantees an atomic transaction meaning it either succeeds or fails completely and rolls back and nothing is saved

- however if you have a fail on insertMany, it will only fail on documents that it doesn't work but won't rollback

## Importing Data
`mongoimport tv-shows.json -d movieData -c movies --jsonArray --drop`

- this will create a new db called movieData with a collection called movies

- the jsonArray lets mongodb know that theer are multiple documents being imported

- drop the import if it already exists in the db

```
2020-11-03T13:56:38.643-0700	connected to: mongodb://localhost/
2020-11-03T13:56:38.643-0700	dropping: movieData.movies
2020-11-03T13:56:38.671-0700	240 document(s) imported successfully. 0 document(s) failed to import.
```

## Practice
- insert multiple companies into a collection using insertOne and insertMany

- deliberately insert duplicate ID and fix failing additions with unordered inserts

`db.companies.insertMany([{name: "Build a Bear", stock: 13, _id: 2}, {name: "Free", stock: 23, _id: 1}], {ordered: false})`

**Ordered Inserts:** by default, inserts are ordered so it will stop when the error occurs but if you change it to false, it is unordered and the inserting process can continue even if there are errors

This will insert the data and not include the ones that have failed.

`db.companies.insertOne({name: "Pikachu", stock: 2300, _id: 4}, {writeConcern: {w:1, j: true}})`

With the j in journal, it will write to the db when the server issues are fixed

**Journal:** write list of tasks; if you want to wait for journal to finish writing or not wait for the server, specify it