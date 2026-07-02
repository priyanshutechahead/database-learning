# Database Design Patterns: Embedding vs. Referencing

This document provides a quick-reference guide for deciding between **Embedding (Denormalization)** and **Referencing (Normalization)** in document-oriented databases (e.g., MongoDB).

---

## Quick Decision Matrix

| Factor | Go with **Embedding** | Go with **Referencing** |
| :--- | :--- | :--- |
| **Relationship Type** | 1:1 or 1:Few (Bounded) | 1:Many (Large/Unbounded) or Many:Many |
| **Data Growth** | Bounded / Small (< 16MB) | Unbounded / Large |
| **Query Pattern** | Fetched together in one shot | Fetched independently or paginated |
| **Data Duplication** | Acceptable for read speed | Unacceptable / Avoid data anomalies |
| **Write Pattern** | Read-heavy, rare updates | Write-heavy, frequent updates |

---

## 1. Embedding (Denormalization)

Embedding stores related data inside a single document as an un-indexed sub-document or array.

### When to Use
* **1:1 Relationships:** The data belongs exclusively to the parent (e.g., a user and their settings).
* **1:Few Relationships:** The nested data has a natural limit and won't grow infinitely (e.g., a product and its 3–4 color variants).
* **Read-Heavy Workloads:** You need to fetch the parent and child data together in a single query for maximum performance.
* **Data is Static:** The nested data rarely changes, or represents a historical snapshot (e.g., an order receipt capturing product prices at the time of purchase).

### Example (JSON)
```json
// Users Collection
{
  "_id": "60c72b2f9b1d8b2bad9a1111",
  "username": "johndoe",
  "email": "john@example.com",
  "preferences": {
    "theme": "dark",
    "notifications": true
  }
}
