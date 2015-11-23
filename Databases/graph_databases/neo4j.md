When to use a relational database vs graph vs noSQL
    Use graph in datasets with highly connected data that is unstructured.
        Ex: A social network where you're trying to query a property of friends, of friends, of friends
    Use relational databases for highly structured data.
        Good for reporting applications and calculations
    NoSQL
        When all of the data needs to be present on every object
        Not good when querying relationships between documents
What is a graph?
    Consists of nodes (entities)
    Relation that connects the entities

Why Graphs?
    Easily extendable and expandable
    Friendly for humans to conceptualize
    Both the nodes and the relationships can contain data (properties )

Property Graph Model
    Contains nodes and relationships
    Nodes and relationships contain properties
    Relationships are named and directed with a start and end node

Neo4j
    ACID
        If the full query doesn't work, everything is rolled back to its previous state
    Implemented in Java 
    Can scale up (add more entities if additional RAM is added) and out (clusters of servers)
    REST API
    Cypher
        Query language made for Neo4j

The rest of the document will focus on Neo4j

Node
    Contain properties
        Data types are set implicitly    

        If you don't want a property, just omit it completely, no need to set null value.

        A property Can be set to be unique.
            If it doesn't exist, the constraint doesn't apply 
        Key = string
        Value = prim data type
        Indexing (each node has an inplicit auto incrementing integer ID)
            Although you can change this

    
    Can have labels (tags) which can tell you something about the node
        labels can be used so nodes are queried in groups
        can be added and removed dynamically

Relationships
    Connect nodes
    Directed (arrows between nodes)
    Named
    Can contain properties

Database Configs:
    neostore.*
        Up this if your production server has more memory

        neostore.propertystore.*
            keys and values in nodes and relationships
        neostore.relationshipstore.*
            relationship data

        if the traversal speed is more important than the properties,
        applocate more memory to the relationship store, etc. 

        keep_logical_logs
            Will log all transactions to data.  So if DB crashes, the DB will look for unfinished transactions and automatically roll them back

Cypher is the most powerful querying language for neo4j
     To make a query:
        Think of a whiteboard friendly structure that you would like to retrieve
        Translate into ASCII art
            (:DAUGHTER{name:'Jenna'})-[:LIVES_WITH]->(p:Parent)
            Node and property | Relationship and arrow | p is a variable to utilize later that's assigned to parent
                These can keep continuing
        Surround by clauses
            MATCH
            (:DAUGHTER{name:'Jenna'})-[:LIVES_WITH]->(p:Parent)
            RETURN p.name
                This would return that parent's name.  This can be executed in the web interface as an example
                The last line of the query can also be
                    RETURN c.name AS name (this renames the result)



Database Design
    To model a sequence
        Have Previous, Next relationships between nodes
    Lets say that we have Nodes that are episodes of a TV show.  We could have a property that includes the airing date.  This is great for most cases,But what if we wanted to specifically construct a query for finding all epidsodes with a certain airing month.
         For more efficiency: We could make a node with both month and year properties and add an "aired in" relationship between the episode and the node.


