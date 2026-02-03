Use Case: Supply Chain Risk Resilience via Semantic RAG
Overview In global logistics, a "local" event (like a factory fire or earthquake) creates a "global" impact. Standard Vector RAG can find news about the event, but it cannot navigate the complex web of dependencies required to tell you which of your products will be delayed. This Semantic RAG approach bridges that gap.
The Knowledge Schema (Cypher) To ensure the LLM extracts data consistently, we define a strict schema. This acts as a "map" for the Graph Database.
Nodes and Relationships Company: Manufacturers and suppliers.
Product: Your end-user offerings.
Component: The parts required to build products.
Location: Geographic regions (Cities/Countries).
RiskEvent: Disruptions (Natural disasters, strikes, etc.).
Schema Template Cypher // 1. Constraints for Data Integrity CREATE CONSTRAINT FOR (c:Company) REQUIRE c.id IS UNIQUE; CREATE CONSTRAINT FOR (p:Product) REQUIRE p.id IS UNIQUE;
// 2. The Graph Architecture // (:Company)-[:MANUFACTURES]->(:Product) // (:Company)-[:SUPPLIES]->(:Component) // (:Component)-[:PART_OF]->(:Product) // (:RiskEvent)-[:AFFECTS]->(:Location) // (:Company)-[:LOCATED_IN]->(:Location) 3. The Hybrid Retrieval Logic Step A: Vector Search (The "What") The user asks: "How will the earthquake in Hsinchu affect our Q4 laptop production?" The Vector DB retrieves recent incident reports and news snippets containing:
“Magnitude 6.1 earthquake strikes Hsinchu Industrial Park...”
“Power outages reported at major semiconductor facilities...”
Step B: Graph Traversal (The "Who" and "Where") Using the entities found (Hsinchu, Earthquake), the system executes a multi-hop traversal:
Cypher MATCH (re:RiskEvent {type: "Earthquake"})-[:AFFECTS]->(l:Location {name: "Hsinchu"}) MATCH (c:Company)-[:LOCATED_IN]->(l) MATCH (c)-[:SUPPLIES]->(comp:Component)-[:PART_OF]->(p:Product {category: "Laptop"}) RETURN p.name AS ImpactedProduct, c.name AS Supplier, comp.name AS CriticalPart 4. Sample "Golden Dataset" Copy and paste this into a Neo4j sandbox to visualize the connections:
Cypher // Create Locations CREATE (l1:Location {name: "Hsinchu", region: "Taiwan"})
// Create Companies CREATE (c1:Company {name: "TSMC", id: "CO_001"}) CREATE (c2:Company {name: "GlobalFoundries", id: "CO_002"})
// Create Products & Components CREATE (p1:Product {name: "Zenith Laptop", id: "PROD_99"}) CREATE (comp1:Component {name: "M3 Processor", id: "COMP_10"})
// Establish Relationships CREATE (c1)-[:LOCATED_IN]->(l1) CREATE (c1)-[:SUPPLIES]->(comp1) CREATE (comp1)-[:PART_OF]->(p1)
// Add a Risk Event CREATE (re:RiskEvent {type: "Earthquake", severity: "High"}) CREATE (re)-[:AFFECTS]->(l1) 5. Summary of Benefits Logical Inference: The system "knows" that even if your laptop isn't mentioned in the news, it is impacted because it uses a component made in the affected region.
Precision: Eliminates hallucinations where the LLM might guess which companies are in Hsinchu.
Speed: By targeting specific locations in the graph, we avoid scanning millions of unrelated document vectors.
