# neo4j-graph-algo
<p align="left">
LOAD CSV WITH HEADERS FROM "file:///transport-nodes.csv" AS row<br>
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
<img src="/img/g1.png" width="350" title="hover text">
</p>
