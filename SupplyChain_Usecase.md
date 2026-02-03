# Use Case: Supply Chain Risk Resilience via Semantic RAG

## 1. Overview
In global logistics, a "local" event (like a factory fire or earthquake) creates a "global" impact. Standard Vector RAG can find news about the event, but it cannot navigate the complex web of dependencies required to tell you **which of your products** will be delayed. This Semantic RAG approach bridges that gap.

---

## 2. The Knowledge Schema (Cypher)
To ensure the LLM extracts data consistently, we define a strict schema. This acts as a "map" for the Graph Database.

### Nodes and Relationships
* **Company**: Manufacturers and suppliers.
* **Product**: Your end-user offerings.
* **Component**: The parts required to build products.
* **Location**: Geographic regions (Cities/Countries).
* **RiskEvent**: Disruptions (Natural disasters, strikes, etc.).

### Schema Template
```cypher
// 1. Constraints for Data Integrity
CREATE CONSTRAINT FOR (c:Company) REQUIRE c.id IS UNIQUE;
CREATE CONSTRAINT FOR (p:Product) REQUIRE p.id IS UNIQUE;

// 2. The Graph Architecture
// (:Company)-[:MANUFACTURES]->(:Product)
// (:Company)-[:SUPPLIES]->(:Component)
// (:Component)-[:PART_OF]->(:Product)
// (:RiskEvent)-[:AFFECTS]->(:Location)
// (:Company)-[:LOCATED_IN]->(:Location)
