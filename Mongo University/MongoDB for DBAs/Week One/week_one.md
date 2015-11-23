Good questions:
    By default, which database does the mongo shell connect to?
    test


MongoDB was first created in 2007 when the company was still called 10gen
Traditionally called a NoSQL database, but we should think of it as "Not Only SQL"

Why is MongoDB trying to accomplish
    Parallelism: As we are moving to an infrastructure with highly concurent systems, how do we design a database to take advantage?  MongoDB was designed to be able to take advantage of multiple cores and servers.

    Scaling: 
        Trying to get away from vertical scaling.  
        Vertical scaling: If I need a DB that does 10x more stuff, I just buy a 10x bigger box.
            Cons: Cost?
        Horizontal scaling: Instead of buying a 10x bigger box, I utilize 10 servers.
            Cons: Higher probability of a server outage.
                Latency of communicating throughout the distributed servers
                If our data is unstructured, we can't do certain large scale design patterns (star schema, etc.)  With this type of scaling, the data is so distributed that communication latency can be a big deal when doing JOINS.
            Due to some of the CONS (JOINS and complex transactions in general) MongoDB doesn't give this functionality.  To do these types of SQL transactions when horizontally scaling is very difficult.

        MongoDB tries to find an sweet spot between scaling and speed, and features.  This is a tradeoff.  
            Best of scaling and speed: MemcacheD
            Best of features: Some sort of relational database

Joins: In SQL, this is "joining" different rows of data which can be complex

MongoDB’s document structure is based off of JSON
    JSON is language independent and has its own RFC, thus it can be a common API
    Each JSON structure can be collorlated to a MongoDB document.  As JSON structures can be nested, and have arrays, etc. we can store a very rich piece of information without having to seperate all of the conplex data pieces (i.e. via traditional SQL model) into their own tables.  This allows MongoDB to mitigate a lot of joins/\.
    We also use JSON to represent the Mongo query
    You can utilize JSON’s 6 Object types (strings,numbers, booleans, null, arrays, objects (other documents))
    The JSON Spec says that you should put quotes around the keys, although in certain contexts, sometimes this isn’t inforsed if the key begins with a letter
    The primary key for the document is usually represented with “_id”
        the _id value doesn’t have to have an object type of ObjectId, although it usually does.  The only requirement is that the _id field is unique and it doesn't change over the life of the documen[.

Mongo BSON (aka serialization format):
    A binary representon of JSON
    It allows for high scanability, which is crucial when documents are large
    Allows for datatypes not in JSON
        This includes Date and BinData (which is a byte array)
        ObjectId
    If the application asks for the whole document, Mongo will transmit the whole thing over the wire as BSON. The BSON then gets stored on the client in RAM.  After that, the mongo driver in the application can then construct the BSON into a native object for a given programming language.  So you can do a REST API that outputs BSON. 

The Mongo import/export can also utilize JSON as well.
     Import can utilize CSV, TSV or JSON
     For import, make sure that each JSON document is it's OWN line in the file.
     Ex: mongoimport --db sales --collection toothbrush --host=127.0.0.1 < sales.json


It’s not accurate to define MongoDB as schemaless, we collections, etc.  A better way to describe everything is “dynamic schema“.
    This helps us with iteration because we don’t have as many schema migrations to do.  In other words, if we add add a field to our collection and, we dont have to do a large DB migration.  The fields in a mongo collection are allowed to vary and the types of data they contain are allowed to vary.
    Can handle polymorphic data


/data structure
    /journal redo logs for crash recovery
    mongod.lock indicates that the database is running
    DBNAME.ns is the system catelog - Important information about the database
    Each part of the database will be stored in a 2 GB max file
        Ex: DBNAME.0, DBNAME.1, etc.

Mongo Shell
    db is a variable to the current database
    it
        After a cursor returns a result, typing this into the shell will interate the cursor

Mongo queries are actually executed as BSON

Covered commands ()
    show dbs <-- to show databases
    show collections
        the "local" database is reserved and it is utilized for things such as replication
    db
        the current database
    help connect
        shows you how to connect to multiple mongo dbs at a time
    db.users.find({ price: 34 })
        Find all products where price = 34. The first object in find is main query, the second object will show which fields to select, etc.
    db.users.find({}).limit(4).skip(2)
    db.users.findOne({})
    db.products.find({}, {name:1})
        Find all documents and show their name field.  0 would not show the field.
        db.products.find({}, {name:1, _id:0, brand:1})
            Show name and brand field (if they exist) but not _id field (which is default).
             
Null evaluation
    Conceptually in mongo, if a field isn't there, it has a value of null

Lazy Evaluation
    db.products.find({SOME QUERY}).limit(2)
    Mongo does a "lazy evaluation" of this query. (So the query isn't run untill everything is applied).  In other words, when queries are chained, the server doesn't return all of the results of find and then limit those results.  In other words, the query is efficient.


MongoDB’s document structure is based off of JSON
JSON is language independent and has its own RFC, thus it can be a common API
Each JSON structure can be collorlated to a MongoDB document.  As JSON structures can be nested, and have arrays, etc. we can store a very rich piece of information without having to seperate all of the conplex data pieces (i.e. via traditional SQL model) into their own tables.  This allows MongoDB to mitigate a lot of joins/\.
We also use JSON to represent the Mongo query
You can utilize JSON’s 6 Object types (strings,numbers, booleans, null, arrays, objects (other documents))
The JSON Spec says that you should put quotes around the keys, although in certain contexts, sometimes this isn’t inforsed if the key begins with a letter
The primary key for the document is usually represented with “_id”
the _id value doesn’t have to have an object type of ObjectId, although it usually does.  The only requirement is that the _id field is unique.

Mongo BSON (aka serialization format):
A binary representon of JSON
It allows for high scanability, which is crucial when documents are large
Allows for datatypes not in JSON
This includes Date and BinData (which is a byte array)
ObjectId

If the application asks for the whole document, Mongo will transmit the whole thing over the wire as BSON. The BSON then gets stored on the client in RAM.  After that, the mongo driver in the application can then construct the BSON into a native object for a given programming language.  So you can do a REST API that outputs BSON. 

The Mongo import/export can also utilize JSON as well.

It’s not accurate to define MongoDB as schemaless, we collections, etc.  A better way to describe everything is “dynamic schema“.
This helps us with iteration because we don’t have as many schema migrations to do.  In other words, if we add add a field to our collection and, we dont have to do a large DB migration.
Can handle polymorphic data










