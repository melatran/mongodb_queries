# Read Operations
- query & projection (Read)

    - query selectors

        - comparison, evaluation

        - logical, array

        - element, comments, geospatial

    - projection operators

        - `$`, `$eleMatch`, `$meta`, `$slice`

- query operators are used to locate data and doesn't change data

- projection operators allows you to modify data presentation but doesn't change the data

- [Click here for the operators](https://docs.mongodb.com/manual/reference/operator/query/)

## Comparison Operators
- *$ne* means not equal to

        - `db.movies.find({runtime: {$ne: 60}}).pretty()`

- *$lt* mean lower than ____

- *$gt* mean greater than _____

- *$lte* lower than or equal to _____

- *$gte* greater than or equal to _____

## Querying Embedded Fields and Array

`db.movies.find({"rating.average": {$gt: 7}}).pretty()`

- the embedded path can be as deep to dig into the data you want such as `rating.total.average`

- `db.movies.find({genres: "Drama"}).pretty()db.movies.find({"rating.average": {$gt: 7}}).pretty()`  but it has to be Drama and drama will not show up


## Understanding $in, $or, $and
find all documents with runtime equal to 30 or 42 `db.movies.find({runtime: {$in: [30, 42]}}).pretty()`

`db.movies.find({runtime: {$nin: [30, 42]}}).pretty()` not equal to either 30 or 42

`db.movies.find({$or: [{"rating.average": {$lt:5}}, {"rating.average": {$gt: 9.3}}]})`

`db.movies.find({$and: [{"rating.average": {$lt:5}}, {"rating.average": {$gt: 9.3}}]}`

