# neo4j-graph-algo
<p align="left">
$\color{LightSkyBlue}{LOAD\ CSV\ WITH\ HEADERS\ FROM\ "file:///transport-nodes.csv"\ AS\ row}$<br>
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
