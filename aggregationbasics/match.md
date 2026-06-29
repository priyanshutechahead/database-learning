# `$match` Stage Examples

## Collection: `users`

```json id="ex3c1m"
[
  {
    "_id": 1,
    "name": "Alice",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 2,
    "name": "Bob",
    "status": "inactive",
    "country": "USA"
  },
  {
    "_id": 3,
    "name": "Charlie",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 4,
    "name": "David",
    "status": "inactive",
    "country": "Canada"
  },
  {
    "_id": 5,
    "name": "Emma",
    "status": "active",
    "country": "USA"
  }
]
```

---

# Example 1: Find Only Active Users

## Aggregation

```javascript id="ag1m2x"
db.users.aggregate([
  {
    $match: {
      status: "active"
    }
  }
])
```

## What MongoDB Does

It checks every document.

| User    | Status   | Keep? |
| ------- | -------- | ----- |
| Alice   | active   | ✅ Yes |
| Bob     | inactive | ❌ No  |
| Charlie | active   | ✅ Yes |
| David   | inactive | ❌ No  |
| Emma    | active   | ✅ Yes |

## Output

```json id="out9k2"
[
  {
    "_id": 1,
    "name": "Alice",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 3,
    "name": "Charlie",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 5,
    "name": "Emma",
    "status": "active",
    "country": "USA"
  }
]
```

---

# Example 2: Filter Users from India

## Aggregation

```javascript id="ag4pd8"
db.users.aggregate([
  {
    $match: {
      country: "India"
    }
  }
])
```

## Output

```json id="out1zn"
[
  {
    "_id": 1,
    "name": "Alice",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 3,
    "name": "Charlie",
    "status": "active",
    "country": "India"
  }
]
```

---

# Example 3: Multiple Conditions

Find users who are **active AND from India**.

## Aggregation

```javascript id="ag7fh2"
db.users.aggregate([
  {
    $match: {
      status: "active",
      country: "India"
    }
  }
])
```

## Output

```json id="out6yl"
[
  {
    "_id": 1,
    "name": "Alice",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 3,
    "name": "Charlie",
    "status": "active",
    "country": "India"
  }
]
```

---

# Example 4: Filter Using Comparison Operators

Find users whose `_id` is greater than `2`.

## Aggregation

```javascript id="ag8tw4"
db.users.aggregate([
  {
    $match: {
      _id: { $gt: 2 }
    }
  }
])
```

## Output

```json id="out5vr"
[
  {
    "_id": 3,
    "name": "Charlie",
    "status": "active",
    "country": "India"
  },
  {
    "_id": 4,
    "name": "David",
    "status": "inactive",
    "country": "Canada"
  },
  {
    "_id": 5,
    "name": "Emma",
    "status": "active",
    "country": "USA"
  }
]
```

---

# Why `$match` is Placed First?

Suppose your collection has **1,000,000 users**, but only **20,000 are active**.

## ❌ Less Efficient

```javascript id="bad5mx"
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      totalUsers: {
        $sum: 1
      }
    }
  },
  {
    $match: {
      _id: "India"
    }
  }
])
```

Here, MongoDB groups all **1,000,000 users** before filtering.

---

## ✅ More Efficient

```javascript id="good7qa"
db.users.aggregate([
  {
    $match: {
      status: "active"
    }
  },
  {
    $group: {
      _id: "$country",
      totalUsers: {
        $sum: 1
      }
    }
  }
])
```

Now MongoDB first reduces the dataset to **20,000 active users**, then performs the grouping, making the aggregation much faster.

---

# Real-time Analogy 📦

Imagine there are **100 students** waiting outside a classroom.

The teacher says:

> "Only students wearing a blue uniform can enter."

```text
Outside
-------------------------
Alice (Blue)   ✅
Bob (Red)      ❌
Charlie (Blue) ✅
David (Green)  ❌
Emma (Blue)    ✅
```

After `$match`, only these students remain:

```text
Inside Classroom
----------------
Alice
Charlie
Emma
```

The teacher can now perform other tasks (like counting students, grouping them by class, calculating average marks) on just these students, instead of all 100.

Think of **`$match`** as a **security gate**: it lets only the documents that satisfy the condition pass to the next stage of the aggregation pipeline.
