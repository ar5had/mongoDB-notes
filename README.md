# mongoDB-notes
Reading these notes while doing learnyoumongo exercises will benifit you the most.

## mongoDB
MongoDB is a scalable, high-performance, open source NoSQL database. MongoDB is written in C++.

## Installing mongoDB

For installation, visit [here](https://docs.mongodb.com).

To verify that mongod is installed, you can try running `mongod --version`.

mongod is the primary daemon process for the MongoDB system. It handles data requests, manages data access, and performs background management operations.

In multitasking computer operating systems, a daemon is a computer program that runs as a background process, rather than being under the direct control of an interactive user. Traditionally, the process names of a daemon end with the letter d, for clarification that the process is, in fact, a daemon, and for differentiation between a daemon and a normal computer program. For example, syslogd is the daemon that implements the system logging facility, and sshd is a daemon that serves incoming SSH connections.

## Searching documents

To connect to the database, one can use something like this:
``` js
    var mongo = require('mongodb').MongoClient
    mongo.connect(url, function(err, db) {
      // db gives access to the database
    })
```
To get a collection, one can use `db.collection('<collection name>')`.

To find a document or documents, one needs to call find() on the collection.

Find is a little bit different than what we are used to seeing.

To convert to an integer, you could use parseInt().

Here is an example:
``` js
    collection.find({
      name: 'foo'
    }).toArray(function(err, documents) {
    
    })
```
If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.

If the database name is learnyoumongo.
So, the url would be something like: mongodb://localhost:27017/learnyoumongo

Use the parrots collection to find all documents where age
is greater than the first argument passed to script.

Using console.log, print the documents to stdout.

Code for above search: 
``` js
    var url = 'mongodb://localhost:27017/learnyoumongo';

    var mongo = require("mongodb").MongoClient;

    mongo.connect(url, function(err,  db){
        if (err) console.error(err);

        db.collection('parrots')
            .find({
                age : {$gt : +(process.argv[2])}
            }).toArray(function(err, docs) {
                if (err) console.error(err);
                console.log(docs);
                db.close(); 
            });
    });
```
**NOTE:** mongod should be running ...

For more comparison query operators - visit [here](https://docs.mongodb.com/v3.2/reference/operator/query-comparison/).

## Searching documents with relevant fields only - Projection 

### Return the Specified Fields and the _id Field Only

A projection can explicitly include several fields. In the following operation, the db.collection.find() method returns all documents that match the query. In the result set, only the name, status and, by default, the _id fields return in the matching documents.

    db.users.find( { status: "A" }, { name: 1, status: 1 } )
The operation returns the following documents:

    { "_id" : 2, "name" : "bob", "status" : "A" }
    { "_id" : 3, "name" : "ahn", "status" : "A" }
    { "_id" : 6, "name" : "abc", "status" : "A" }
### Return Specified Fields Only

You can remove the _id field from the results by specifying its exclusion in the projection, as in the following example:

    db.users.find( { status: "A" }, { name: 1, status: 1, _id: 0 } )
In the result set, only the name and status fields return in the matching documents:

    { "name" : "bob", "status" : "A" }
    { "name" : "ahn", "status" : "A" }
    { "name" : "abc", "status" : "A" }
### Return All But the Excluded Field

To exclude a field or multiple fields, instead of listing the fields to include in the matching document, you can use a projection to exclude specific fields as in the following example:

    db.users.find( { status: "A" }, { favorites: 0, points: 0 } )
In the result set, the favorites and the points fields do not return in the matching documents:

    {
       "_id" : 2,
       "name" : "bob",
       "age" : 42,
       "type" : 1,
       "status" : "A",
       "finished" : [ 11, 25 ],
       "badges" : [ "green" ]
    }
    {
       "_id" : 3,
       "name" : "ahn",
       "age" : 22,
       "type" : 2,
       "status" : "A",
       "finished" : [ 6 ],
       "badges" : [ "blue", "red" ]
    }
    {
       "_id" : 6,
       "name" : "abc",
       "age" : 43,
       "type" : 1,
       "status" : "A",
       "finished" : [ 18, 12 ],
       "badges" : [ "black", "blue" ]
    }
With the exception of the _id field you cannot combine inclusion and exclusion statements in projection documents.

### Return Specific Fields in Embedded Documents

Use the dot notation to return specific fields in an embedded document.

The following example specifies a projection to return: the _id field, name field, status field, and the food field inside the favorites document; the food field remains embedded in the favorites document.

    db.users.find(
       { status: "A" },
       { name: 1, status: 1, "favorites.food": 1 }
    )
The operation returns the following documents:

    { "_id" : 2, "name" : "bob", "status" : "A", "favorites" : { "food" : "meringue" } }
    { "_id" : 3, "name" : "ahn", "status" : "A", "favorites" : { "food" : "cake" } }
    { "_id" : 6, "name" : "abc", "status" : "A", "favorites" : { "food" : "pizza" } }
### Suppress Specific Fields in Embedded Documents

Use dot notation to suppress specific fields of an embedded document using a 0 instead of 1.

The following example specifies a projection to exclude the food field inside the favorites document. All other fields are returned in the matching documents:

    db.users.find(
       { status: "A" },
       { "favorites.food": 0 }
    )
The operation returns the following document:

    {
       "_id" : 2,
       "name" : "bob",
       "age" : 42,
       "type" : 1,
       "status" : "A",
       "favorites" : { "artist" : "Miro" },
       "finished" : [ 11, 25 ],
       "badges" : [ "green" ],
       "points" : [ { "points" : 85, "bonus" : 20 }, { "points" : 64, "bonus" : 12 } ]
    }
    {
       "_id" : 3,
       "name" : "ahn",
       "age" : 22,
       "type" : 2,
       "status" : "A",
       "favorites" : { "artist" : "Cassatt" },
       "finished" : [ 6 ],
       "badges" : [ "blue", "red" ],
       "points" : [ { "points" : 81, "bonus" : 8 }, { "points" : 55, "bonus" : 20 } ]
    }
    {
       "_id" : 6,
       "name" : "abc",
       "age" : 43,
       "type" : 1,
       "status" : "A",
       "favorites" : { "artist" : "Picasso" },
       "finished" : [ 18, 12 ],
       "badges" : [ "black", "blue" ],
       "points" : [ { "points" : 78, "bonus" : 8 }, { "points" : 57, "bonus" : 7 } ]
    }
    
### Projection on Embedded Documents in an Array

Use dot notation to project specific fields inside documents embedded in an array.

The following example specifies a projection to return the name field, status field, and just the bonus field in the documents in the points array. The _id field is returned by default.

    db.users.find( { status: "A" }, { name: 1, status: 1, "points.bonus": 1 } )
The operation returns the following documents:

    { "_id" : 2, "name" : "bob", "status" : "A", "points" : [ { "bonus" : 20 }, { "bonus" : 12 } ] }
    { "_id" : 3, "name" : "ahn", "status" : "A", "points" : [ { "bonus" : 8 }, { "bonus" : 20 } ] }
    { "_id" : 6, "name" : "abc", "status" : "A", "points" : [ { "bonus" : 8 }, { "bonus" : 7 } ] }
### Project Specific Array Elements in the Returned Array

For fields that contain arrays, MongoDB provides the following projection operators: $elemMatch, $slice, and $.

The following example uses the $slice projection operator to return just the last element in the scores array.

    db.users.find( { status: "A" }, { name: 1, status: 1, points: { $slice: -1 } } )
The operation returns the following documents:

    { "_id" : 2, "name" : "bob", "status" : "A", "points" : [ { "points" : 64, "bonus" : 12 } ] }
    { "_id" : 3, "name" : "ahn", "status" : "A", "points" : [ { "points" : 55, "bonus" : 20 } ] }
    { "_id" : 6, "name" : "abc", "status" : "A", "points" : [ { "points" : 57, "bonus" : 7 } ] }
`$elemMatch`, `$slice`, and `$` are the only way to project specific elements to include in the returned array. For instance, you cannot project specific array elements using the array index; e.g. { "ratings.0": 1 } projection will not project the array with the first element.

Last program but with only name and age properties:

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name here.
        url = 'mongodb://localhost:27017/learnyoumongo' ;
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection("parrots")
            .find({
                age: {$gt: +process.argv[2]}
            },
            {
                    name: 1, 
                    age: 1, 
                    _id: 0
            }).toArray(function(err, docs) {
               if (err) console.error(err);
               console.log(docs);
               db.close();
            });
    });
```
**NOTE:** mongod should be running ...

## Inserting document in a collection

To connect, use the connect() function of MongoClient.

Ex.
``` js
    MongoClient.connect(url, function(err, db) {
      if (err) throw err
    
    })
```
If you get a Connection Refused error, make sure that mongod is still
running.

After you have successfully connected, you will need to specify a collection.
That can be done by calling the collection() function on the db returned
in the callback to connect.

Say you wanted to specify a collection named users:
``` js
    var collection = db.collection('users')
```
To insert a document, one would need to call insert() on the collection, like this:

```js    
    // inserting document
    // { a : 2 }
    collection.insert({
      a: 2
    }, function(err, data) {
      // handle error
    
      // other operations
    })
```

If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.


Program to connect to MongoDB on port 27017.
You should connect to the database named learnyoumongo and insert
a document into the docs collection.

The document should be a json document with the following properties:

  * `firstName`
  * `lastName`

firstName will be passed as the first argument to the lesson.

lastName will be passed as the second argument to the lesson.

Use console.log to print out the object used to create the document.

Make sure you use JSON.stringify convert it to JSON.

Code:

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name  here.
        url = 'mongodb://localhost:27017/learnyoumongo' ;
    var obj = {firstName: process.argv[2], lastName: process.argv[3]};
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection("users")
        .insert(obj, function(err, data) {
           if (err) console.error(err);
           console.log(JSON.stringify(obj));
           db.close();
        });
    });
```

## Updating a document in a collection

To update a document, one would need to call update() on the collection.

Ex.

    
    // document
    // { a: 2, b: 3 }
    
    collection.update({
      a: 2
    }, {
      $set: {
        b: 1
      }
    }, callback)
    
    // document was updated
    // { a: 2, b: 1 }

The first argument to update() is the query. This query is what filters the documents that we are wanting to update. The second argument is an object of the properties to update. Pay close attention to the $set property. If we were to omit $set, the document would be replaced with the object represented by the second argument.

If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.

Here we are going to update a document in the users collection.

The database name will be accessible via process.argv[2].

Say we have a user defined like:

    {
      "name": "Tina",
      "age": 30,
      "username": "tinatime"
    }

We want to change Tina's age from 30 to 40.

For the purpose of this lesson, assume that the username property is unique.

Program Code: 

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name here.
        url = 'mongodb://localhost:27017/learnyoumongo' ;
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection("users").update({
            username: "tinatime"

        },
        {
            $set : {
                age: 40

            }
        }, function(err) { console.error(err); db.close(); });
    });
```

## Removing a document from collection

To remove a document, one would need to call remove() on the collection.

Ex.

    collection.remove({
      name: 'foo'
    }, callback)

The first argument to remove() is the query.

If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.

Write a program to remove a document with the given _id.

The database name will be accessible via process.argv[2].

The collection name will be passed as the second argument to your script.

The _id will be passed as the third argument to your script.

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name here.
        url = 'mongodb://localhost:27017/' + process.argv[2];
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection(process.argv[3])
            .remove({ _id: process.argv[4]}, function(err) {
                if (err) throw err;
                db.close();
            });
    });
```

## Counting no of documents

To count the number of documents meeting certain criteria,
we must use the collection.count() function.

Here is an example:

    collection.count({
      name: 'foo'
    }, function(err, count) {
    
    })

If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.

Here we will write a program to count the number of documents that
meet certain criteria.

Use the parrots collection from the database named learnyoumongo to
count all documents where age is greater than the first argument
passed to your script.

Using console.log, print the number to stdout.

``` js 
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name here.
        url = 'mongodb://localhost:27017/learnyoumongo';
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection("parrots")
            .count({
                age : {
                    $gt: +process.argv[2]
                }
            }, function(err, count) {
                if (err) console.error(err);
                console.log(count);
                db.close();
            });
    });
```

## Finding aggregate of fields of multiple documents in a collection

Aggregation allows one to do things like
calculate the sum of a field of multiple documents or the average
of a field of documents meeting particular criteria.

To find aggregate we use the aggregate() function, one first needs the collection.
The aggregate() function takes an array of objects as the first argument.

This array will contain the different pipelines for the aggregation.
To read more about pipelines, please visit [Aggregation](http://docs.mongodb.org/manual/core/aggregation-introduction/).

The two main pipeline stages we will use will be $match and $group.

### $match

`$match` is used similar to the way a query is done. It allows us to select
the documents that meet certain criteria.

Ex.

    var match = { $match: { status: 'A' } }

The above example will match all of the documents that have a status
property equal to A.

### $group

`$group` is what allows us to run operations on certain properties.

So, say we wanted to get the sum of the values of the property value
where status is equal to A and have it placed in the total property.

Ex.

    // [
    //  { status: 'A', value: 1 },
    //  { status: 'B', value: 2 },
    //  { status: 'A', value: 10 }
    // ]
    
    collection.aggregate([
      { $match: { status: 'A' }}
    , { $group: {
        _id: 'total' // This can be an arbitrary string in this case
      , total: {
          // $sum is the operator used here
          $sum: '$value'
        }
      }}
    ]).toArray(function(err, results) {
      // handle error
      console.log(results)
      // => [
      // =>   { _id: 'total', total: 11 }
      // => ]
    })

Other operators used in the $group stage include:

  * `$avg`
  * `$first`
  * `$last`
  * `$max`
  * `$min`
  * `$push`
  * `$addToSet`

### Rounding

The Number prototype contains a function toFixed(), which accepts the
number of decimal places you would like to round to, and returns a string
representation.

      var value = "1"
      Number(value).toFixed(5)
      // => '1.00000'

If your program does not finish executing, you may have forgotten to
close the db. That can be done by calling db.close() after you
have finished.

Say you have a collection named prices. Each price document is modeled
like so:

    {
      "name": "Tshirt",
      "size": "S",
      "price": 10,
      "quantity": 12
      "meta": {
        "vendor": "hanes",
        "location": "US"
      }
    }

Write a program to calculate the average price for all documents
in the prices collection in the database named learnyoumongo that have
the size that will be passed as the first argument to your script.

Use console.log() to print the average price rounded to 2 decimal places
to stdout after you have found it.

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name here.
        url = 'mongodb://localhost:27017/learnyoumongo';
    mongo.connect(url, function(err, db) {
        if (err) console.error(err);
        db.collection("prices")
            .aggregate([
            { $match: {size: process.argv[2]}},    
            { $group: {
                _id: 'aggregate',
                price: {
                    $avg: '$price'
                }
            }}    
            ]).toArray(function(err, res) {
                if (err) console.error(err);
                console.log(res[0].price.toFixed(2));
                db.close();
            });
    });
```

## Creating database

  * Start the mongod daemon in one terminal window.
  
  * Open another teminal window and type `mongo` to enter mongo shell.
  
  * Type `use <DATABASE_NAME>` to create a database named \<DATABASE_NAME>.
  
  * Use `db` to know which database you are in.
 
  * To check databases list, you can use `show dbs`. Note: Your created database will not be present in list.
    To display database, you need to insert at least one document into it.
    
  * To create a collection lets say `cold`, type `db.cold.insert({_id: "temp", temp: "32 C"})`  

## Dropping database

Switch to the database that you want to drop using `use <DATABASE_NAME>` command and then type `db.dropDatabase()` command.

## Showing collections

Switch to database and then type `show collections`

## Dropping collections

`db.COLLECTION_NAME.drop()`

## Printing collection

`db["3test"].find()`
or
`db.getCollection("3test").find()`

For formated results:

`db.myCollection.find().pretty()`

**Note:**`db.myCollection.find()`method returns a cursor to the results; however, in the mongo shell, if the returned cursor is not assigned to a variable using the var keyword, then the cursor is automatically iterated up to 20 times to print up to the first 20 documents that match the query. 
