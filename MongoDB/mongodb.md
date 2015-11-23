Inspiration by:
    https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf
    docs.mongodb.org

SQL to MongoDB commands
WHERE       $match
GROUP BY    $group 
HAVING      $match 
SELECT      $project
ORDER BY    $sort
LIMIT       $limit
SUM/COUNT   $sum
JOIN        $unwind

Mongo shell
    Mongo localhost/DBNAME


Inserting Documents:
db.ships.insert({name:'Narada',operator:'Romulan Star Empire',type:'Warbird',class:'Warbird',crew:65,codes:[251,251,220]})

Finding Documents:
db.zSheets.findOne()
    Finds one arbitrary document

db.zSheets.find().prettyPrint()
    Finds all documents and using nice formatting

db.zSheets.find({}, {name:true, _id:false})
    Shows only the names of the zSheets

db.zSheets.findOne({'name':'mongoCheatSheet'})
    Finds one document by attribute

Finding via operators:
$gt/ $gte
    greater than / greater than equals
    ex: db.ships.find({class:{$gt:’P'}) 

$lt/ $lte
    lesser than / lesser than equals
    ex: db.ships.find({class:{$lte:’P'}

$exists
    does an field exist?
    ex: db.ships.find({field:{$exists:true}}) 

$regex
    Pattern matching
    ex: db.ships.find({field:{$regex:’^USS\\sE’}}) 

$type:
    $type selects the documents where the value of the field is an instance of the specified numeric BSON type.
    ex: db.ships.find({field:{$type:<BSON INT Type>}}) 
    Warning: Try to make fields have a consistent datatype

    Noteable BSON Types:
        String: 2
        Array: 4
        Binary: 5
        Date: 9
    

Shell Commands:
db.ships.<command> 
    db – implicit handle to the used database ships – name of the used collection

use <database> 
    Switch to another database

help
    Prints available commands and help

Updating Documents:

.update
    db.ships.update({name : 'USS Prometheus'}, {name : 'USS Something'}) 
    Replaces the whole document

    db.ships.update({name : 'USS Something'}, {$set : {operator : 'Starfleet', class : 'Prometheus'}})
    sets / changes certain fields of a given document

    db.ships.update({name : 'USS Something'}, {$unset : {operator : 1}})
        removes an attribute from a given document

Removing documents
    db.ships.remove({name : 'USS Prometheus'})
    Removes the document

    db.ships.remove({name:{$regex:’^USS\\sE’}})
    Remove via operator

Indexes
    db.ships.find ({'name':'USS Defiant'}).explain()
    Explains index usage

    db.ships.stats()
    Index statistics

    db.ships.totalIndexSize()
    Index size

Overall view of System
    mongotop
    Shows time spent per operations per collection

    mongostat
    Shows snapshot on the MongoDB system

Aggregation Framework:

Pipeline Stages
    General Notes:
    Think of each stage as doing a minipulation on the data and then feeding it into the next stage.
    If you're going to filter down the number of documents at some point in the pipeline, for memory purposes it's advised to do this as soon as possible.

    $project
    Change the set of documents by modifying keys and values. This is a 1:1 mapping

    $match
    Filtering operation to narrow data.

    $group
    This does the actual aggregation and as we are grouping by one or more keys this can have a reducing effect on the amount of documents

    $sort
    Sorting (which can be memory intensive).

    $skip
    With this it is possible to skip forward in the list of documents for a given amount of documents. This allows for example starting only from the 10th document. Typically this will be used together with “$sort” and especially together with “$limit”.

    $limit
    This limits the amount of documents to look at by the given number starting from the current position. 

    $unwind
    This is used to "unwind" documents that are using arrays.  You can create a document for each item in an array.
    ex:
    { "_id" : 1, "item" : "ABC1", sizes: [ "S", "M", "L"] }
    db.inventory.aggregate( [ { $unwind : "$sizes" } ] )
    { "_id" : 1, "item" : "ABC1", "sizes" : "S" }
    { "_id" : 1, "item" : "ABC1", "sizes" : "M" }
    { "_id" : 1, "item" : "ABC1", "sizes" : "L" }


    Aggregation Examples
        db.ships.aggregate([{$group : {_id : "$operator", num_ships : {$sum : 1}}}])
        Counts the number of ships per operator
        SQL: SELECT operator, count(*) FROM ships GROUP BY operator;

        db.ships.aggregate([{$project : {_id : 0, operator : {$toLower: "$operator"}, crew : {"$multiply" : ["$crew",10]}}}])

    Aggregation Expressions
        

    When doing help, you can do that on many levels.
        Ex: db.help(), db.mycoll.help(), etc.  Do help() to find all of the options

TO SORT:
Once you get closer:  Look into basic mongo syntax

TODO:
Look at storage engines.  What are they, etc.  Look at wiredtiger, mmapv1 (default)
security considerations
look at enterprise version of mongo

Look at how to shard mongodb on AWS
what services have offical support for mongodb?
Make sure the file system is correct for quick IO i.e. ext4 is good
Turn off last access time in FS (File system)

BSON = Binary Serialization Object Notation <— How Mongo stores things
cursor = Practically all data read commands return a cursor.  If you fetch 10 documents with a certain query, you can then ask mongo to get you the next 10, etc.  It remembers where you are and this minimizes huge loads going over the network

oplog- If you are backing up, this keeps track of files that are changed throughout the backup process…  This makes the time snapshot more accurate.  Think of oplog as operations log.  This only works for replica sets (not single mongo DB instances).

mongodump for backing up

restore your backups as a test to make sure everything works correctly

Make sure to index fields that you’re going to be querying often.  To see how the indexs are used… to your query and add .explain()
    If you see stage: COLLSCAN, that means that mongo isn’t using an index and instead is going through every single item.. BAD
  Also utilize .explain(‘executionStats’)
 
You can use multi field indexs… however if you omit certain fields in your query, you have to make sure that the index is utilizing it efficiently.  It really depends on where that field that you’re omitting occurs in the overall query.

Covered queries… Are the best.   When the result that we need is actually in the index.. SUPER fast.

Sparse indexes only index when there’s a value.. i.e. not NULL.  this can be a huge performance increase.  However, if you’re utilizing sorting… you wouldn’t want to use a sparse index… not all values will be there.  In general, do these often… but not every situation.

Unique indexes can do 2 things.  Say the unique index is on username.. it will provide fast lookup and also validation.  Be careful, you can’t have 2 unique indexs with null values.  To mitigate this, do a unique index and make it sparse.  Unique indexes don’t work across shards

TTL indexes: Time to live.. You can set an expiration date on a document.  If there is a huge number of documents going away at once.. maybe it’s a better idea to do some sort of batch processing, etc. 
for indexes building, you can do foreground (blocking) or background

Replica set: primary and secondary.. If one goes down others can take place.  Also if one fails, others can rehydrate.  There’s one primary, and that one has read/write privs… no secondarys have writeaccess.  Want to put replica sets in different availability zones
    The oplog is a capped collection that looks at all of the writes going on in the primary… then, the replica’s watch this collection and mimic it for redundancy

An election is held for replica set memebers to figure out who is primary.  Has to get more than 50% of the votes from other replica set members to be primary.  If there are only 2 servers, and one goes down.. there’s obviously a problem with the over 50% rule.  So you can have an arbiter server that holds no data but only votes

Chaining:  There’s often data charges for moving data between replica sets… So if you have multiple replicasets between data centers, you can invoke chaining and only move data to one replica set (across data centers) and then spread to other replica sets from that hub.  THis is on by default (but look into this further) 

Sharding: Distribute the database over a bunch of servers.
    Horizontal splitting: A single document resides entirely on one of the sharded servers.
    Mongos is the shard server that is the “ shard router “

When to shard:
    Watch I/O utilization, RAM, and free disk space

db.setProfilingLevel <— You can use this to automatically capture slow queries.  Remember that you need to check for the freq of queries… not just how slow they are.

What to spend money on for Mongo hardware:
CPU- Limited effect on overall performance, larger effect when stoage engine provudes encryption, compression
Memory- Your working set should fit into RAM
Disk- IO is CRUCIAL for performance

Say you have a 500 mb file with mmv1 engine:
Wired tiger with full comp would make this same file 80 mb

To maximize IO: Have index’s on one volume and files on another.. mongo can than utilize these in parellel.. wiredtiger has a config for this


