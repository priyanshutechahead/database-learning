# `$sort` Stage Examples

## Collection: `users`

```json id="6zmk3t"
[
  {
    "_id": 1,
    "name": "Alice",
    "age": 25,
    "salary": 50000
  },
  {
    "_id": 2,
    "name": "Bob",
    "age": 30,
    "salary": 70000
  },
  {
    "_id": 3,
    "name": "Charlie",
    "age": 22,
    "salary": 45000
  },
  {
    "_id": 4,
    "name": "David",
    "age": 35,
    "salary": 80000
  },
  {
    "_id": 5,
    "name": "Emma",
    "age": 28,
    "salary": 60000
  }
]
```

---

# Example 1: Sort by Age (Ascending)

`1` means **smallest → largest**.

## Aggregation

```javascript id="91knjv"
db.users.aggregate([
  {
    $sort: {
      age: 1
    }
  }
])
```

## Before Sorting

| Name    | Age |
| ------- | --: |
| Alice   |  25 |
| Bob     |  30 |
| Charlie |  22 |
| David   |  35 |
| Emma    |  28 |

## After Sorting

| Name    | Age |
| ------- | --: |
| Charlie |  22 |
| Alice   |  25 |
| Emma    |  28 |
| Bob     |  30 |
| David   |  35 |

## Output

```json id="x7tp5r"
[
  { "_id": 3, "name": "Charlie", "age": 22 },
  { "_id": 1, "name": "Alice", "age": 25 },
  { "_id": 5, "name": "Emma", "age": 28 },
  { "_id": 2, "name": "Bob", "age": 30 },
  { "_id": 4, "name": "David", "age": 35 }
]
```

---

# Example 2: Sort by Age (Descending)

`-1` means **largest → smallest**.

## Aggregation

```javascript id="rv9n6k"
db.users.aggregate([
  {
    $sort: {
      age: -1
    }
  }
])
```

## Output

| Name    | Age |
| ------- | --: |
| David   |  35 |
| Bob     |  30 |
| Emma    |  28 |
| Alice   |  25 |
| Charlie |  22 |

---

# Example 3: Sort by Salary (Highest First)

## Aggregation

```javascript id="q3eh7a"
db.users.aggregate([
  {
    $sort: {
      salary: -1
    }
  }
])
```

## Output

| Name    | Salary |
| ------- | -----: |
| David   |  80000 |
| Bob     |  70000 |
| Emma    |  60000 |
| Alice   |  50000 |
| Charlie |  45000 |

---

# Example 4: Sort by Multiple Fields

Suppose we have this data:

```json id="a4lu0b"
[
  { "name": "Alice", "country": "India", "age": 25 },
  { "name": "Bob", "country": "USA", "age": 30 },
  { "name": "Charlie", "country": "India", "age": 22 },
  { "name": "David", "country": "USA", "age": 35 },
  { "name": "Emma", "country": "India", "age": 28 }
]
```

Sort by:

* `country` (A-Z)
* `age` (youngest first)

## Aggregation

```javascript id="x2mbvq"
db.users.aggregate([
  {
    $sort: {
      country: 1,
      age: 1
    }
  }
])
```

## Output

| Country | Name    | Age |
| ------- | ------- | --: |
| India   | Charlie |  22 |
| India   | Alice   |  25 |
| India   | Emma    |  28 |
| USA     | Bob     |  30 |
| USA     | David   |  35 |

---

# Using `$sort` After `$group`

Suppose `$group` produced this result:

```json id="e8fqj2"
[
  { "_id": "India", "totalUsers": 15 },
  { "_id": "USA", "totalUsers": 8 },
  { "_id": "Canada", "totalUsers": 12 }
]
```

Now sort by `totalUsers` in descending order.

## Aggregation

```javascript id="2fgr7y"
db.users.aggregate([
  {
    $group: {
      _id: "$country",
      totalUsers: { $sum: 1 }
    }
  },
  {
    $sort: {
      totalUsers: -1
    }
  }
])
```

## Output

```json id="l0u9pw"
[
  { "_id": "India", "totalUsers": 15 },
  { "_id": "Canada", "totalUsers": 12 },
  { "_id": "USA", "totalUsers": 8 }
]
```

---

# Real-time Analogy 📚

Imagine a teacher has students' exam scores.

```text id="9x2rlv"
Alice    85
Bob      92
Charlie  78
David    95
Emma     88
```

The teacher says:

### Sort Highest to Lowest (`-1`)

```text id="cm8jtn"
David    95
Bob      92
Emma     88
Alice    85
Charlie  78
```

### Sort Lowest to Highest (`1`)

```text id="j5nkgw"
Charlie  78
Alice    85
Emma     88
Bob      92
David    95
```

Think of **`$sort`** as arranging books on a shelf. The books (documents) don't change—their order changes based on the field you choose (`age`, `salary`, `totalUsers`, etc.).
