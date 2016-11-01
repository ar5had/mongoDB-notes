# mongoDB-notes
simple mongo-db notes

## mongoDB
MongoDB is a scalable, high-performance, open source NoSQL database. MongoDB is written in C++.

## Installing mongoDB

For installation, visit [here](https://docs.mongodb.com).

To verify that mongod is installed, you can try running `mongod --version`.

mongod is the primary daemon process for the MongoDB system. It handles data requests, manages data access, and performs background management operations. This document provides a complete overview of all command line options for mongod.

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
###Project Specific Array Elements in the Returned Array

For fields that contain arrays, MongoDB provides the following projection operators: $elemMatch, $slice, and $.

The following example uses the $slice projection operator to return just the last element in the scores array.

db.users.find( { status: "A" }, { name: 1, status: 1, points: { $slice: -1 } } )
The operation returns the following documents:

    { "_id" : 2, "name" : "bob", "status" : "A", "points" : [ { "points" : 64, "bonus" : 12 } ] }
    { "_id" : 3, "name" : "ahn", "status" : "A", "points" : [ { "points" : 55, "bonus" : 20 } ] }
    { "_id" : 6, "name" : "abc", "status" : "A", "points" : [ { "points" : 57, "bonus" : 7 } ] }
$elemMatch, $slice, and $ are the only way to project specific elements to include in the returned array. For instance, you cannot project specific array elements using the array index; e.g. { "ratings.0": 1 } projection will not project the array with the first element.

Last program but with only name and age properties:

``` js
    var mongo = require("mongodb").MongoClient,
        // learnyoumongo is db name  here.
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
