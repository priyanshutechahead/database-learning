# `$group` Stage Examples

## Collection: `users`

```json
[
  {
    "_id": 1,
    "name": "Alice",
    "country": "India",
    "age": 25
  },
  {
    "_id": 2,
    "name": "Bob",
    "country": "USA",
    "age": 30
  },
  {
    "_id": 3,
    "name": "Charlie",
    "country": "India",
    "age": 28
  },
  {
    "_id": 4,
    "name": "David",
    "country": "USA",
    "age": 35
  },
  {
    "_id": 5,
    "name": "Emma",
    "country": "India",
    "age": 22
  }
]
```

---

# Example 1: Count Users in Each Country

## Aggregation

```javascript
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      totalUsers: { $sum: 1 }
    }
  }
])
```

## Step-by-Step

MongoDB first groups documents by the value of `country`.

```text
India
------
Alice
Charlie
Emma

USA
----
Bob
David
```

Then `$sum: 1` adds `1` for every document.

### For India

```text
0
+1 (Alice)
+1 (Charlie)
+1 (Emma)
= 3
```

### For USA

```text
0
+1 (Bob)
+1 (David)
= 2
```

## Output

```json
[
  {
    "_id": "India",
    "totalUsers": 3
  },
  {
    "_id": "USA",
    "totalUsers": 2
  }
]
```

---

# Example 2: Average Age Per Country

## Aggregation

```javascript
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      averageAge: { $avg: "$age" }
    }
  }
])
```

## Calculation

### India

```text
25
28
22

Average = (25 + 28 + 22) / 3
        = 75 / 3
        = 25
```

### USA

```text
30
35

Average = (30 + 35) / 2
        = 32.5
```

## Output

```json
[
  {
    "_id": "India",
    "averageAge": 25
  },
  {
    "_id": "USA",
    "averageAge": 32.5
  }
]
```

---

# Example 3: Maximum Age in Each Country

## Aggregation

```javascript
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      oldestAge: { $max: "$age" }
    }
  }
])
```

## Output

```json
[
  {
    "_id": "India",
    "oldestAge": 28
  },
  {
    "_id": "USA",
    "oldestAge": 35
  }
]
```

---

# Example 4: Total Age of Users in Each Country

## Aggregation

```javascript
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      totalAge: { $sum: "$age" }
    }
  }
])
```

## Calculation

### India

```text
25 + 28 + 22 = 75
```

### USA

```text
30 + 35 = 65
```

## Output

```json
[
  {
    "_id": "India",
    "totalAge": 75
  },
  {
    "_id": "USA",
    "totalAge": 65
  }
]
```

---

# Real-time Analogy 📦

Imagine a teacher has students from different countries.

```text
Alice   → India
Bob     → USA
Charlie → India
David   → USA
Emma    → India
```

The teacher says:

> "Stand with people from your country."

Students form groups:

```text
India Group
------------
Alice
Charlie
Emma

USA Group
---------
Bob
David
```

Now the teacher can ask questions for each group:

* Count students → `$sum: 1`
* Average age → `$avg`
* Oldest student → `$max`
* Youngest student → `$min`
* Total marks → `$sum: "$marks"`

Think of **`$group`** as creating **buckets** based on a common field (like `country`), and **accumulators** (`$sum`, `$avg`, `$max`, etc.) as calculating values inside each bucket.
