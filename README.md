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
