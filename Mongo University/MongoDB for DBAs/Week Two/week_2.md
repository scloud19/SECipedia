* = Optional 


Helpful hints:
    As the output from the mongo shell is JSON, we can pipe that into a file and then utilize mongoimport to directly import it into the database
        As you can easily import JSON data via mongoimport.  Think about the implications of having a server hit an API and easily inserting into the DB through this vector
    Easily cut/paste json from shell or other sources and assign it to a variable in the shell

    If you don't invoke the function, the source will be displayed in the shell.
        Ex: db.isMaster
            Returns: function () { return this.runRommand('isMaster'); }
                Determines if this member of the replica set is the Primary
            These types of situations are good for seeing the real commands behind helpers.  Not all drivers in the software will respect the helpers, so you sometimes have to use the true commands behind them.
    To save yourself typing, when working with collections, assign the collection to a variable.
        Ex: t = db.pageviews
    When designing your interactions with the DB, it is very important to consider raceconditions.

    You can always use regular expressions in the shell
        I.e. db.test2.remove( { x: /ello/ } )

MongoDB indexes: Are Btrees

Mongo Document Limit: 16 MB
    This is more of a sanity check, but would you actually want to go larger than this? MongoDB can physically handle any document as long as there's enough RAM

Database and Collection init
    Remember that collections don't guarantee ordering
        Except: Capped Collections
    Create DB:
        use zach_man
        After creating the DB, there isn't any data files for it in /data
            This will be created when we create the first collection
            ex: db.collection_here.insert({ a: 1 })
                _id will automatically be inserted if you don't set it explicitly.
                    The _id field
                        Automatically indexed
                        Doesn't have to be of type ObjectId()
                        Must exist in every document
                        Immutable
                        Must be unique inside the collection
db.sample.find().pretty() <-- pretty print
    Similar idea: db.sample.find().toArray()

Updates
    2 ways:
        Full document update/replacement
        Partial Update
            Ex in SQL: update orders set avail = 0 where id=123
            Operators covered to do partial modifications:
                $set
                $push - Add element to array even if there's an identicial value
                $addtoSet - Inserts into array if value not present
                $pop
                $unset
                $inc
                In general in Mongo, if a field isn't there and we try to do an additive operation (i.e. $push, $inc, etc.) the field will be added if it isn't already there.
                    Ex: t.update({ _id: 101}, {$push: {arr: "hi"} } )

    ex: db.collection.update(<where clause>, <doc_or_partial_update_expression, [ options_document ])
        The keys in the options_document are:
            upsert (BOOL): Update, or insert if not present. Defaults to false
                This is very good when dealing with realtime dashboards and mitigating potential raceconditions
            multi: (BOOL) Update many documents, not just one.  Defaults to false
            writeConcern: document



        ex: t.update ({ _id : 100 }, { "_id" : 100, x : "hello world" })
            Adding "hello world" to all documents with an _id of 100.
                We couldn't change the _id field because it's immutable

    Another way to update a document (in this ex, t is pointing to a collection):
        myObj = t.findOne()
        myObj.y = 123;

        At this point it isn't persisted, so we need to:

        t.update ({ _id: myobj._id }, myobj  )

        Or we could simply write
            t.save(myobj) <-- syntactic sugar for the update command
                If you try to save a document with no ID, it will assign the document an objectID for its _id field, and then insert it.

Remove/Delete
    Before running these types of transformations, it would be good to run it as a find to see what we're deleting
        You can also run a .find().count() to get a rough idea of what's happening before executing the remove() function
    db.<collection_name>.remove(<matching expression>)
        This executes for all documents in the collection
    db.test.remove({})
        Removes all documents in the colleciton

Mongo wire protocol aka BSON wire protocol
    This is very low level, if you were writting a driver, you would want to know all about this
    Commands are the administrative commands that you'll use or more non-admin commands that aren't simple CRUD operations
    db.runCommand({ <commandname> : <val> }) <--- Command invokation
        To run these commands we need to switch to the admin db
            Ex: use admin
        Note that not every single DBA command is run with the runcommand syntax.  An example is killOp
        For many of these commands, there are helper methods
        Ex: 
            db.runCommand({ getLastError: 1, w: 2, wtimeout: 5000})
            Ensures the preceding operation has replicated to two members (the primary and one other member).  The command also specifies a timeout of 5000 ms to ensure that the command doesn't not forever block the db if it can't satisfy the requested write concern.
        Ex: 
            DBA style commands
                Are types of commands that run against the admin db
                admin db is a reserved database name
                Server-wide removal or other types of commands
            Ex:
                drop - Drop a collection
                repairDatabase
                clone <-- copy in data from external source
                copydb - from local source
                dbStats 
                create - create a collection
                    Ex: Used primarily for capped collections
                compact - compact a collection
                serverStatus
                    ex: db.serverStatus()
                replSetGetStatus
                addShard
                getLastError
                ensureIndex - Create an index
                dropIndex
                currentOp - Current operation in progress
                killOp - Utilized with an operation ID
                serverStatus
                logout - When using security with the servers
                rename - Rename a collection
            user commands:
                isMaster - Is master in cluster
                aggregate - running the aggregation framework (which can include CRUD operations)
                mapReduce
                count
                findAndModify
            
        Simple basic CRUD operations are not technically considered commands
        Ex:
            Query
            GetMore - Get more on existing cursor
            Insert - Can think of as the "Create" in CRUD
            Update
            Remove
            
    Commands are sent to the server via query format
        Server responds with a document

Bulk write operations
    Ex:
        var bulk = db.<collection>.initializeUnorderedBulkOp();
        var bulk = db.<collection>.initializeOrderedBulkOp();
            (If ordered)

        Then you can start adding items to the batch, etc.
            bulk.insert({ item: "abc123", status: "A" })
            bulk.insert({ item: "37sdj", status: "B" })

            OR
            bulk.find({ item: "abc"}).remove()
            bulk.find({ item: "abc"}).update({ $inc: {points: 1} } )

        bulk.execute()

    Good when performance is key, this will fire a single request to a server/cluster

    2 types
    Ordered
    Unordered: More efficient
        If we're in a sharded cluster, if unordered, the documents can be inserted in parallel as applicable

Collection operations
    Ex:
        db.<collection>.stats() <-- Collection stats
        db.<collection>.drop() <-- Delete collection







