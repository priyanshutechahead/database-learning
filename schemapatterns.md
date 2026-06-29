# Schema Patterns: Embedding vs. Referencing

In a document database (like MongoDB), data relationships are handled in one of two ways: **Embedding (denormalization)** or **Referencing (normalization)**.

---

# Embedding (Denormalization)

Embedding stores related data inside a single document as a sub-document or an array of documents.

```json
// User Document with Embedded Addresses
{
  "_id": "u101",
  "name": "Alice Smith",
  "addresses": [
    {
      "type": "shipping",
      "city": "Noida",
      "zip": "201301"
    },
    {
      "type": "billing",
      "city": "Delhi",
      "zip": "110001"
    }
  ]
}
```

## When to Use Embedding

### 1:1 Relationships

When one entity strictly belongs to another (e.g., a user and their account settings).

### 1:Few (Bounded 1:N)

When the "many" side will not grow infinitely. A user might have 3–5 addresses, but rarely 50,000.

### Data is Frequently Read Together

If you always need to display the address whenever you load the user profile, embedding eliminates the need for a second database query.

### Data Atomicity

Updates to a single document are atomic. If you update the user and their address together, it succeeds or fails as a single unit.

---

# Referencing (Normalization)

Referencing splits data into separate collections and links them using a unique identifier (like an `_id`), similar to a foreign key in SQL.

```json
// Authors Collection
{
  "_id": "auth77",
  "name": "George Orwell"
}

// Books Collection
{
  "_id": "book99",
  "title": "1984",
  "author_id": "auth77"
}
```

## When to Use Referencing

### 1:Many Unbounded (1:Infinite)

When the child array can grow indefinitely. For example, an unbounded array of log entries inside a user document will eventually hit the maximum document size limit (e.g., 16MB in MongoDB).

### Many-to-Many (N:M) Relationships

If multiple books can have multiple authors, and authors belong to multiple books, referencing prevents massive data duplication.

### Data Changes Frequently

If the related data is updated often and is shared across multiple documents, changing it in one single reference document is cleaner than hunting down and updating thousands of embedded instances.
