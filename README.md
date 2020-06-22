# janus


Links :
	https://developer.ibm.com/patterns/develop-graph-database-app-using-janusgraph/
	https://medium.com/@chris.hupman/getting-started-with-janusgraph-552f43beb9c0
	
	https://medium.com/finc-engineering/graph-db-diving-into-janusgraph-part-1f-199b807697d2
	https://medium.com/finc-engineering/graph-db-diving-into-janusgraph-part-2-f4b9cbd967ac
	https://medium.com/enharmonic/evolving-data-models-with-janusgraph-d0ecf6d3fda3
	https://blog.levilentz.com/dockerized-configuredgraphfactory-with-janusgraph-cassandra-and-elasticsearch/
	
	https://kelvinlawrence.net/book/Gremlin-Graph-Guide.html#tpintro      =>book
	https://github.com/krlawrence/graph
	https://github.com/krlawrence/graph/blob/master/sample-code/CreateGraph.java
	https://github.com/krlawrence/graph/blob/master/sample-code/GraphFromCSV.java
	https://github.com/krlawrence/graph/blob/master/sample-code/GraphSearch.java
	
	
	https://www.ibm.com/cloud/blog/database-deep-dives-janusgraph
	https://github.com/IBM/janusgraph-utils
	JanusGraph Journey https://www.youtube.com/watch?v=1TQcPWgPvF8
	
	
	
	From <https://github.com/IBM/janusgraph-utils/blob/master/doc/source/images/architecture.png> 
	
	
	
	https://developer.ibm.com/dwblog/2018/whats-janus-graph-learning-deployment/
	https://developer.ibm.com/dwblog/2018/janusgraph-composite-mixed-indexes-traversals/
	https://developer.ibm.com/dwblog/2018/janusgraph-administrative-operations-backups/
	
	
	https://www.experoinc.com/post/developing-a-janusgraph-backed-service-on-gcp


start gremlin.bat
start gremlin-server.bat D:\soft\elk\janusgraph-full-0.5.2\conf\gremlin-server\gremlin-server.yaml




------------------------------

// Step 1 - Initial Schema Example - Setup

//

// Run with a Gremlin Console from the command line:

// $ bin/gremlin -i InitialSetup.groovy

//

// We use an in-memory graph for all testing

// This supports our data modelling graph operations and

// simplifies our setup without requiring an external storage backend.

graph = JanusGraphFactory.build().

  set('storage.backend', 'inmemory').open()





//-----------------------

// Load the Initial Schema

//-----------------------

mgmt = graph.openManagement()



// Define Vertex labels

Orchestra = mgmt.makeVertexLabel('Orchestra').make()

Artist = mgmt.makeVertexLabel('Artist').make()

Work = mgmt.makeVertexLabel('Work').make()

Concert = mgmt.makeVertexLabel('Concert').make()



// Define Edge labels - the relationships between Vertices

COMPOSER = mgmt.makeEdgeLabel('COMPOSER').multiplicity(MANY2ONE).make()

SOLOIST = mgmt.makeEdgeLabel('SOLOIST').multiplicity(SIMPLE).make()

CONDUCTOR = mgmt.makeEdgeLabel('CONDUCTOR').multiplicity(SIMPLE).make()

ORCHESTRA = mgmt.makeEdgeLabel('ORCHESTRA').multiplicity(SIMPLE).make()

INCLUDES = mgmt.makeEdgeLabel('INCLUDES').multiplicity(SIMPLE).make()



// Define Vertex Property Keys

// Orchestra

name = mgmt.makePropertyKey('name').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()



mgmt.addProperties(Orchestra, name)





// Artist

lastName = mgmt.makePropertyKey('lastName').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

firstName = mgmt.makePropertyKey('firstName').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

gender = mgmt.makePropertyKey('gender').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

nationality = mgmt.makePropertyKey('nationality').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

deceased = mgmt.makePropertyKey('deceased').

  dataType(Boolean.class).cardinality(Cardinality.SINGLE).make()



mgmt.addProperties(Artist, lastName, firstName, gender, nationality, deceased)





// Work

title = mgmt.makePropertyKey('title').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

compositionDate = mgmt.makePropertyKey('compositionYear').

  dataType(Integer.class).cardinality(Cardinality.SINGLE).make()

soloInstrument = mgmt.makePropertyKey('soloInstrument').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()



mgmt.addProperties(Work, title, compositionDate, soloInstrument)





// Concert

firstDate = mgmt.makePropertyKey('firstDate').

  dataType(String.class).cardinality(Cardinality.SINGLE).make()

numShows = mgmt.makePropertyKey('numShows').

  dataType(Integer.class).cardinality(Cardinality.SINGLE).make()



mgmt.addProperties(Concert, name, firstDate, numShows)



// Define connections as (EdgeLabel, VertexLabel out, VertexLabel in)

mgmt.addConnection(COMPOSER, Work, Artist)

mgmt.addConnection(SOLOIST, Work, Artist)

mgmt.addConnection(CONDUCTOR, Work, Artist)

mgmt.addConnection(ORCHESTRA, Concert, Orchestra)

mgmt.addConnection(INCLUDES, Concert, Work)



mgmt.commit()





//-----------------------

// Add Sample Data

//-----------------------

g = graph.traversal()

// Make an Orchestra

nyPhil = g.addV('Orchestra').property('name', 'New York Philharmonic').next()

cso = g.addV('Orchestra').property('name', 'Chicago Symphony Orchestra').next()



// Make 4 Artists

salonen = g.addV('Artist').

  property('lastName', 'Salonen').

  property('firstName', 'Esa-Pekka').

  property('gender', 'Male').

  property('nationality', 'Finnish').next()



strauss = g.addV('Artist').

  property('lastName', 'Strauss').

  property('firstName', 'Richard').

  property('gender', 'Male').

  property('nationality', 'German').

  property('deceased', true).next()



gilbert = g.addV('Artist').

  property('lastName', 'Gilbert').

  property('firstName', 'Alan').next()



ma = g.addV('Artist').

  property('lastName', 'Ma').

  property('firstName', 'Yo-Yo').next()



// Make 3 Works

alsoSprach = g.addV('Work').

  property('title',"Also sprach Zarathustra").

  property('compositionYear', 1896).next()



wing = g.addV('Work').

  property('title',"Wing on Wing").

  property('compositionYear', 2004).next()



celloConcerto = g.addV('Work').

  property('title', 'Cello Concerto').

  property('compositionYear', 2017).

  property('soloInstrument', 'cello').next()



// Make 3 concerts

concert1 = g.addV('Concert').

  property('name', 'Esa-Pekka Salonen Conducts US Premiere by Tansy Davies').

  property('firstDate', '4/27/2017').

  property('numShows', 3).next()



concert2 = g.addV('Concert').

  property('name', 'Premieres by Esa-Pekka Salonen and Anna Thorvaldsdottir').

  property('firstDate', '5/19/2017').

  property('numShows', 3).next()



concert3 = g.addV('Concert').

  property('name', 'Salonen & Yo-Yo Ma').

  property('firstDate', '3/9/2017').

  property('numShows', 3).next()



// Add relationships

// INCLUDES

g.addE('INCLUDES').from(concert1).to(alsoSprach).iterate()

g.addE('INCLUDES').from(concert2).to(wing).iterate()

g.addE('INCLUDES').from(concert3).to(celloConcerto).iterate()



// ORCHESTRA

g.addE('ORCHESTRA').from(concert1).to(nyPhil).next()

g.addE('ORCHESTRA').from(concert2).to(nyPhil).next()

g.addE('ORCHESTRA').from(concert3).to(cso).next()



// COMPOSER

g.addE('COMPOSER').from(celloConcerto).to(salonen).next()

g.addE('COMPOSER').from(alsoSprach).to(strauss).next()

g.addE('COMPOSER').from(wing).to(salonen).next()



// CONDUCTOR

g.addE('CONDUCTOR').from(celloConcerto).to(salonen).next()

g.addE('CONDUCTOR').from(alsoSprach).to(salonen).next()

g.addE('CONDUCTOR').from(wing).to(gilbert).next()



// SOLOIST

g.addE('SOLOIST').from(celloConcerto).to(ma).next()



g.tx().commit()



// We can use some simple asserts to check our data model

assert 4 == g.V().hasLabel('Artist').count().next()

assert 3 == g.V().hasLabel('Concert').count().next()



assert 'Ma' == g.V().has('Orchestra', 'name', 'Chicago Symphony Orchestra').

  in('ORCHESTRA').has('Concert', 'name', 'Salonen & Yo-Yo Ma').

  out('INCLUDES').has('Work', 'title', 'Cello Concerto').

  out('SOLOIST').hasLabel('Artist').values('lastName').next()



-------------------------------------------
https://stackoverflow.com/questions/59906848/how-to-get-the-combined-vertex-and-edge-properties-with-gremlin-and-janusgraph

List<Map<String, Object>> propertyList = g.V("V_ID")   // Get the vertex
.outE().hasLabel("OUT_EDGE").as("E")                   // Get the outgoing edge as E
.inV().as("V")                                         // Get the vertex(pointed by E) as V
.select("E", "V")                                      // Select Edge E and Vertex V
.by(__.valueMap().with(WithOptions.tokens).unfold()    // Get value map including tokens
.group().by(Column.keys).by(__.select(Column.values).unfold()))  // Form key value pairs
.toList();                                             // Return the list of properties
NOTE: Replace the sample strings ("V_ID", "OUT_EDGE") tokens as per your implementation
The above query will return all the properties of edges and its associated vertex in java map. The property map will also contain the tokens (i.e. id, label).
Here I had to group and unfold the valueMap() since the valueMap() by default wraps the Value field inside an array and I do not want this behavior since I have all the properties with single value so there is no point in getting a list that contains a single value.
You now have all the edge and associated vertex properties merged and available with the propertyList.
