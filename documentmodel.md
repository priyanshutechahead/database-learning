# Document Model vs Relational Model

## When choosing between a document model and a relational model, you are ultimately deciding how your data’s relationships, scalability, and structure should be handled.

---

# 1. Data Structure and Storage

## Relational Model (SQL)

The relational model organizes data into highly structured, tabular formats consisting of rows and columns (tables). Every row in a table has the exact same structure defined by a strict schema.

**Storage Philosophy:** Data is normalized (broken down into smaller, non-redundant tables) to eliminate duplication.

## Document Model (NoSQL)

The document model stores data as documents (typically JSON, BSON, or XML formats).

**Storage Philosophy:** Data is often denormalized. Related data is grouped together within a single document as nested structures or arrays, rather than being split across multiple tables.

---

# 2. Key Differences at a Glance

| Feature            | Relational Model                                             | Document Model                                                    |
| ------------------ | ------------------------------------------------------------ | ----------------------------------------------------------------- |
| Data Format        | Tables (Rows & Columns)                                      | Documents (JSON / BSON)                                           |
| Schema             | Strict/Static: Schema must be defined before inserting data. | Flexible/Dynamic: Documents can have different fields on the fly. |
| Data Relationships | Handled via Joins and Foreign Keys.                          | Handled via Embedding (nesting) or References.                    |
| Scaling            | Vertical: Making the server bigger (more CPU/RAM).           | Horizontal: Sharding data across multiple servers.                |
| Transactions       | Strict ACID compliance across the entire DB.                 | Typically ACID-compliant at the single-document level.            |

---

# 3. How Relationships are Handled

The way these two models handle connections between data points is fundamentally different.

Let's look at an example: an Order placed by a Customer containing multiple Products.

## Relational Model: References & Joins

To avoid duplicating data, a relational database splits this information into three distinct tables:

* Customers
* Orders
* Order_Items

To see the complete order, the database must perform a **JOIN** operation at runtime to stitch the pieces back together using foreign keys.

## Document Model: Embedding

A document database prefers to store the entire entity together. The customer data and the list of products are nested right inside the single Order document.

```json
{
  "order_id": "XYZ123",
  "date": "2026-06-29",
  "customer": {
    "name": "Alice Smith",
    "email": "alice@email.com"
  },
  "items": [
    {
      "product": "Laptop",
      "price": 1200
    },
    {
      "product": "Mouse",
      "price": 25
    }
  ]
}
```

No joins are required here; fetching the document retrieves everything at once.

---

# 4. Schema Flexibility

## Relational (Schema-on-Write)

You must define the tables, columns, and data types beforehand. Altering a schema on a massive production database can require complex, time-consuming migrations.

## Document (Schema-on-Read)

There is no universal structure enforced by the database itself. One document might have 5 fields, while the next document in the same collection has 10 fields.

This makes it ideal for rapidly evolving applications or polymorphic data.

---

# 5. Scalability: Vertical vs. Horizontal

## Relational Databases

Relational databases are generally designed to run on a single machine. Because data is heavily interconnected via relationships and joins, splitting tables across different servers (horizontal scaling) is complex and often hurts performance.

You scale by upgrading your server's hardware (**Vertical Scaling**).

## Document Databases

Document databases are built with distribution in mind. Because a single document contains all the relevant data for an item, documents can easily be split up and distributed across multiple server nodes (**Horizontal Scaling**, or **Sharding**) without worrying about cross-server joins.

---

# Summary: When to use which?

## Choose the Relational Model if:

* Your data structure is highly consistent, predictable, and clear.
* Your application relies heavily on complex data analysis and relationships across many different entities (e.g., banking apps, ERP systems).
* Strict data integrity and global ACID transactions are non-negotiable.

## Choose the Document Model if:

* Your data is unstructured, semi-structured, or rapidly evolving (e.g., content management systems, user profiles).
* You need to scale horizontally to handle massive amounts of read/write traffic.
* You want your application code (which often objects) to map naturally to your database format (JSON) without needing an intricate ORM layer.
