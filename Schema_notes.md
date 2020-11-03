# Blog
<img width="749" alt="Screen Shot 2020-11-03 at 10 01 33 AM" src="https://user-images.githubusercontent.com/59414750/98016789-a2a8e680-1dbb-11eb-9840-d5f384ec095e.png">

### Schema

![Screen Shot 2020-11-03 at 10 13 31 AM](https://user-images.githubusercontent.com/59414750/98017978-3dee8b80-1dbd-11eb-97f1-38c86bab18dc.png)

- goal is to identify the schema to model the relations in this blog

```
db.users.insertMany([{name: "Lanie", age: 20, email: "1234@email.com"}, {name: "Bob", age: 34, email: "email@.com"}])

db.posts.insertOne({title: "My first", text: "This is my first post", tags: ["new", "firsttime"], creator: ObjectId("5fa190289a59858cd0ca5187"), comments: [{text: "I like this", author: ObjectId("5fa190289a59858cd0ca5186")}]

{
	"_id" : ObjectId("5fa190a29a59858cd0ca5188"),
	"title" : "My first",
	"text" : "This is my first post",
	"tags" : [
		"new",
		"firsttime"
	],
	"creator" : ObjectId("5fa190289a59858cd0ca5187"),
	"comments" : [
		{
			"text" : "I like this",
			"author" : ObjectId("5fa190289a59858cd0ca5186")
		}
	]
}
```

# Data Schemas and Data Modeling
<img width="749" alt="Screen Shot 2020-11-02 at 4 17 13 PM" src="https://user-images.githubusercontent.com/59414750/97929679-e6054580-1d26-11eb-9946-c0471064934f.png">

- mongodb doesn't enforce any schemas where documents don't have to follow the same

```
{
	"_id" : ObjectId("5fa0915e9a59858cd0ca516e"),
	"name" : "Book",
	"price" : 23
}
{
	"_id" : ObjectId("5fa091919a59858cd0ca516f"),
	"title" : "Harry Potter",
	"seller" : {
		"name" : "Book Store"
	}
}
```

- each entry has a different schema which works in mongodb, but better to have a schema when building out the BE to cycle and output into the view for the user

## Data Types

- **text:** strings 

- **booleans** true or false

- **numbers** Integer, NumberDecimal, NumberLong, ObjectId, ISODate, Timestamp

      - numbers are stored as float in the shell but are rounded where NumberDecimal is very precise and not rounded

example:

```
db.companies.insertOne({name: 'Fresh Apples Inc', isStartup: true, employees: 30, funding: 123432546473234156, details: {ceo: "MoMo"}, tags: [{title: "fresh"}, {title: "apples"}, {title: "local"}], foundingDate: new Date(), insertedAt: new Timestamp()})

db.companies.findOne()
{
	"_id" : ObjectId("5fa17c5f9a59858cd0ca5172"),
	"name" : "Fresh Apples Inc",
	"isStartup" : true,
	"employees" : 30,
	"funding" : 123432546473234160, //this number got cut off and can't be stored
	"details" : {
		"ceo" : "MoMo"
	},
	"tags" : [
		{
			"title" : "fresh"
		},
		{
			"title" : "apples"
		},
		{
			"title" : "local"
		}
	],
	"foundingDate" : ISODate("2020-11-03T15:50:55.252Z"),
	"insertedAt" : Timestamp(1604418655, 1)
}
```

- run `db.stats()` to view information about the db and the size of it

- use `db.numbers.insertOne({a: NumberInt(1)})` and it will require less storage; might not work with other drivers

## Relations
<img width="749" alt="Screen Shot 2020-11-03 at 9 53 49 AM" src="https://user-images.githubusercontent.com/59414750/98015853-7d67a880-1dba-11eb-92f2-7962d386af5a.png">

**One to One with Embedded**

```
db.patients.insertOne({name: "Chelsey", age: 34, diseaseSummary: {diseases: ["needs to find herself her own man", "stop texting sex dreams"]}})

db.patients.findOne()
{
	"_id" : ObjectId("5fa1818e9a59858cd0ca5177"),
	"name" : "Chelsey",
	"age" : 34,
	"diseaseSummary" : {
		"diseases" : [
			"needs to find herself her own man",
			"stop texting sex dreams"
		]
	}
}
```

**One to One with References**

*One person has one car and one car belongs to one person*

```
db.persons.insertOne({name: "Max", age: 29, salary: 3000})

db.cars.insertOne({model: "BMW", price: 40000, owner:  ObjectId("5fa1829f9a59858cd0ca5179")})
```

**One to Many with Embeddedd**

`db.questionThreads.insertOne({creator: "max", question: "What is life?", answers: [{text: "like that"}, {text: "thanks"}]})`

**One to Many with Resources**

*One city has many citizens, one citizen belongs to one city*

```
db.cities.insertOne({name: "New York City", coordinates: {lat: 21, lng: 55}})

db.citizens.insertMany([{name: "MoMo Tran", cityId: ObjectId("5fa186b59a59858cd0ca517d")}, {name: "Lin Miranda", cityId: ObjectId("5fa186b59a59858cd0ca517d")}])
```

**Many to Many with Embeddedd**

```
db.customers.updateOne({}, {$set: {orders: [{productId: ObjectId("5fa1878b9a59858cd0ca5180"), quantity: 2}]}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.customers.findOne()
{
	"_id" : ObjectId("5fa1879d9a59858cd0ca5181"),
	"name" : "Max",
	"age" : 29,
	"orders" : [
		{
			"productId" : ObjectId("5fa1878b9a59858cd0ca5180"),
			"quantity" : 2
		}
	]
}
```

- how often do you need to change the data

- how will the data be duplicated throughout had you used product info instead of productId

**Many to Many with References**

*One book has many authors and an author belongs to many books*

```
db.authors.insertMany([{name: "Melanie Tran"}, {name: "MoMo"}])

{
	"_id" : ObjectId("5fa189799a59858cd0ca5183"),
	"name" : "My favorite book",
	"authors" : [
		ObjectId("5fa189bb9a59858cd0ca5184"),
		ObjectId("5fa189bb9a59858cd0ca5185")
	]
}
```

- if the author's name changes or if age was included, we only need to change it in the authors to get it updated on the books information instead of going into the db and changing multiple documents

## lookUp() in Aggregate

- helpful tool that allows you to fetch two related documents and merge them in one step mitigating the disadvantages of splitting documents

*from:* which other collection to want to relate documents

*localField:* where can the relation to the other collection be found in

*foreignField:* where is the connection between the two collections (similar to Joins table)

*as:* the alias name

```
db.books.aggregate([{$lookup: {from: "authors", localField: "authors", foreignField: "_id", as: "creators"}}]).pretty()

{
	"_id" : ObjectId("5fa189799a59858cd0ca5183"),
	"name" : "My favorite book",
	"authors" : [
		ObjectId("5fa189bb9a59858cd0ca5184"),
		ObjectId("5fa189bb9a59858cd0ca5185")
	],
	"creators" : [
		{
			"_id" : ObjectId("5fa189bb9a59858cd0ca5184"),
			"name" : "Melanie Tran"
		},
		{
			"_id" : ObjectId("5fa189bb9a59858cd0ca5185"),
			"name" : "MoMo"
		}
	]
}
```

## Schema Validation
**validationLevel**

    - which documents get validated?

    - strict: all inserts and updates

    - moderate: all inserts and updates to correct documents

**validationAction**

    - what happens if validation fails?

    - error: throw error and deny insert/update

    - warn: log warning but proceed

'posts' --> name of collection

required --> fields that must be part of the document before insertion

properties --> describe what type you want it to be and what must the item has to be be

```
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
});
```

- edit a collection to change the validation action

```
db.runCommand({
  collMod: 'posts',
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  },
  validationAction: 'warn'
});
```

## Summary
- in which format witll you fetch your data

- how often will you fetch and change your data

      - if you write data, avoid duplicates

- how much data will you save and how big is it

- how is your data related

- will duplicates hurt you such as many updates or do you have snapshots of data

- will hit any data/storage limits
