# neo4j-graph-algo
<p align="left">
$\color[RGB]{0, 170, 228}{LOAD\ CSV\ WITH\ HEADERS\ FROM\ "file:///transport-nodes.csv"\ AS\ row}$<br>
MERGE (place:Place {id:row.id})<br>
SET place.latitude = toFloat(row.latitude),<br>
place.longitude = toFloat(row.latitude),<br>
place.population = toInteger(row.population)<br>
<br>
LOAD CSV WITH HEADERS FROM "file:///transport-relationships.csv" AS row<br>
MATCH (origin:Place {id: row.src})<br>
MATCH (destination:Place {id: row.dst})<br>
MERGE (origin)-[:EROAD {distance: toInteger(row.cost)}]->(destination)<br>
</p>
<p>MATCH (p:Place) RETURN p<br></p>
<p align="left">
<img src="/img/g1.jpg"  title="full city graph.">
</p>
<p align="left">Shortest Path</p>
<p align="left">
CALL gds.graph.project(<br>
    'myGraph',<br>
    'Place',<br>
    'EROAD',<br>
    {<br>
        nodeProperties: ['latitude', 'longitude'],<br>
        relationshipProperties: 'distance'<br>
    }<br>
)<br>
</p>
<p align="left">
MATCH (source:Place{id: "Amsterdam"}), (target:Place{id: "London"})<br>
CALL gds.shortestPath.astar.stream('myGraph', {<br>
    sourceNode: source,<br>
    targetNode: target,<br>
    latitudeProperty: 'latitude',<br>
    longitudeProperty: 'longitude',<br>
    relationshipWeightProperty: 'distance'<br>
})<br>
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path<br>
RETURN<br>
    index,<br>
    gds.util.asNode(sourceNode).id AS sourceNodeName,<br>
    gds.util.asNode(targetNode).id AS targetNodeName,<br>
    totalCost,<br>
    [nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,<br>
    costs,<br>
    nodes(path) as path<br>
ORDER BY index<br>
<p>
<p align="left">
<img src="/img/g2.jpg"  title="shortest path road graph.">
</p>
<br>
<p align="left">Breadth First Search<br></p>
<p>CALL gds.graph.project('myGraph', 'Place', 'EROAD')<br></p>
<p>MATCH (source:Place{id: "London"})<br>
CALL gds.bfs.stream('myGraph', {<br>
  sourceNode: source<br>
})<br>
YIELD path<br>
RETURN path<br></p>
<p align="left">
<img src="/img/g4.png"  title="Breadth First Search result on transport grapth.">
</p>
<p align="left">Breadth First Search algorithm with target nodes:<br></p>
<p align="left">
MATCH (a:Place{id:'London'}), (d:Place{id:'Amsterdam'}), (e:Place{id:'Rotterdam'})<br>
WITH id(a) AS source, [id(d), id(e)] AS targetNodes<br>
CALL gds.bfs.stream('myGraph', {<br>
  sourceNode: source,<br>
  targetNodes: targetNodes<br>
})<br>
YIELD path<br>
RETURN path<br></p>

<p align="left">
<img src="/img/g5.png"  title="Breadth First Search result on transport grapth with target nodes.">
</p>
<p align="left">Breadth First Search algorithm with maxDepth:<br></p>
<p align="left">MATCH (source:Place{id: "London"})<br>
CALL gds.bfs.stream('myGraph', {<br>
  sourceNode: source,<br>
  maxDepth: 1<br>
})<br>
YIELD path<br>
RETURN path<br></p>
<p align="left">
<img src="/img/g6.png"  title="Breadth First Search algorithm with maxDepth.">
</p>
<p align="left">
MATCH (source:Place{id: "Hoek van Holland"})<br>
CALL gds.bfs.stream('myGraph', {<br>
  sourceNode: source,<br>
  maxDepth: 1<br>
})<br>
YIELD path<br>
RETURN path<br>
</p>
<p align="left">
<img src="/img/g7.png"  title="Breadth First Search algorithm with maxDepth.">
</p>
<p align="left">A* Shortest Path algorithm improves on Dijkstra’s by finding shortest paths more quickly.<br></p>
<p align="left">
CALL gds.graph.project(<br>
    'myGraphAShotestPath',<br>
    'Place',<br>
    'EROAD',<br>
    {<br>
        nodeProperties: ['latitude', 'longitude'],<br>
        relationshipProperties: 'distance'<br>
    }<br>
)<br>
</p>
<p align="left">
MATCH (source:Place {id: "Immingham"}), (destination:Place {id: "London"})<br>
CALL gds.shortestPath.astar.stream('myGraphAShotestPath', {<br>
    sourceNode: source,<br>
    targetNode: destination,<br>
    latitudeProperty: 'latitude',<br>
    longitudeProperty: 'longitude',<br>
    relationshipWeightProperty: 'distance'<br>
})<br>
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path<br>
RETURN<br>
    index,<br>
    gds.util.asNode(sourceNode).id AS sourceNodeName,<br>
    gds.util.asNode(targetNode).id AS targetNodeName,<br>
    totalCost,<br>
    [nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,<br>
    costs,<br>
    nodes(path) as path<br>
ORDER BY index<br>
</p>
<img src="/img/g8.png"  title="Breadth First Search algorithm with maxDepth.">
</p>
<p align="left">
index	sourceNodeName	targetNodeName	totalCost	nodeNames	                            costs<br>
0	    "Immingham"	    "London"	    351.0	    ["Immingham", "Doncaster", "London"]	[0.0, 74.0, 351.0]<br>
</p>
<p align="left">
MATCH (source:Place {id: "London"}), (target:Place {id: "Gouda"})<br>
CALL gds.shortestPath.astar.stream('myGraphAShotestPath', {<br>
    sourceNode: source,<br>
    targetNode: target,<br>
    latitudeProperty: 'latitude',<br>
    longitudeProperty: 'longitude',<br>
    relationshipWeightProperty: 'distance'<br>
})<br>
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path<br>
RETURN<br>
    index,<br>
    gds.util.asNode(sourceNode).id AS sourceNodeName,<br>
    gds.util.asNode(targetNode).id AS targetNodeName,<br>
    totalCost,<br>
    [nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,<br>
    costs,<br>
    nodes(path) as path<br>
ORDER BY index<br>
</p>
<p align="left">
index	sourceNodeName	targetNodeName	totalCost	nodeNames	                                                                                costs<br>
0	    "London"	    "Gouda"	        426.0	    ["London", "Colchester", "Ipswich", "Felixstowe", "Hoek van Holland", "Den Haag", "Gouda"]	[0.0, 106.0, 138.0, 160.0, 367.0, 394.0, 426.0]<br>
</p>





