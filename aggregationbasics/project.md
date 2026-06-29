# `$project` Stage Examples

## Collection: `users`

```json
[
  {
    "_id": 1,
    "name": "Alice",
    "age": 25,
    "country": "India",
    "salary": 50000
  },
  {
    "_id": 2,
    "name": "Bob",
    "age": 30,
    "country": "USA",
    "salary": 70000
  },
  {
    "_id": 3,
    "name": "Charlie",
    "age": 22,
    "country": "India",
    "salary": 45000
  },
  {
    "_id": 4,
    "name": "David",
    "age": 35,
    "country": "Canada",
    "salary": 80000
  },
  {
    "_id": 5,
    "name": "Emma",
    "age": 28,
    "country": "USA",
    "salary": 60000
  }
]
```

---

# Example 1: Include Only Name and Country

## Aggregation

```javascript
db.users.aggregate([
  {
    $project: {
      name: 1,
      country: 1
    }
  }
])
```

## Output

```json
[
  {
    "_id": 1,
    "name": "Alice",
    "country": "India"
  },
  {
    "_id": 2,
    "name": "Bob",
    "country": "USA"
  },
  {
    "_id": 3,
    "name": "Charlie",
    "country": "India"
  },
  {
    "_id": 4,
    "name": "David",
    "country": "Canada"
  },
  {
    "_id": 5,
    "name": "Emma",
    "country": "USA"
  }
]
```

**Notice:** `_id` is still included because MongoDB includes it by default.

---

# Example 2: Hide `_id`

## Aggregation

```javascript
db.users.aggregate([
  {
    $project: {
      _id: 0,
      name: 1,
      country: 1
    }
  }
])
```

## Output

```json
[
  {
    "name": "Alice",
    "country": "India"
  },
  {
    "name": "Bob",
    "country": "USA"
  },
  {
    "name": "Charlie",
    "country": "India"
  },
  {
    "name": "David",
    "country": "Canada"
  },
  {
    "name": "Emma",
    "country": "USA"
  }
]
```

---

# Example 3: Rename a Field

Suppose you want `country` to appear as `countryName`.

## Aggregation

```javascript
db.users.aggregate([
  {
    $project: {
      _id: 0,
      name: 1,
      countryName: "$country"
    }
  }
])
```

## Output

```json
[
  {
    "name": "Alice",
    "countryName": "India"
  },
  {
    "name": "Bob",
    "countryName": "USA"
  },
  {
    "name": "Charlie",
    "countryName": "India"
  },
  {
    "name": "David",
    "countryName": "Canada"
  },
  {
    "name": "Emma",
    "countryName": "USA"
  }
]
```

---

# Example 4: Create a New Calculated Field

Suppose `salary` is the monthly salary.

Create a new field called `annualSalary`.

## Aggregation

```javascript
db.users.aggregate([
  {
    $project: {
      _id: 0,
      name: 1,
      salary: 1,
      annualSalary: {
        $multiply: ["$salary", 12]
      }
    }
  }
])
```

## Output

```json
[
  {
    "name": "Alice",
    "salary": 50000,
    "annualSalary": 600000
  },
  {
    "name": "Bob",
    "salary": 70000,
    "annualSalary": 840000
  },
  {
    "name": "Charlie",
    "salary": 45000,
    "annualSalary": 540000
  },
  {
    "name": "David",
    "salary": 80000,
    "annualSalary": 960000
  },
  {
    "name": "Emma",
    "salary": 60000,
    "annualSalary": 720000
  }
]
```

---

# Example 5: `$project` After `$group`

Suppose `$group` returns:

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

Now make the output more readable.

## Aggregation

```javascript
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
    $project: {
      _id: 0,
      countryName: "$_id",
      totalUsers: 1
    }
  }
])
```

## Output

```json
[
  {
    "countryName": "India",
    "totalUsers": 3
  },
  {
    "countryName": "USA",
    "totalUsers": 2
  }
]
```

Here:

* `_id: 0` → Remove `_id`
* `countryName: "$_id"` → Rename `_id` to `countryName`
* `totalUsers: 1` → Keep `totalUsers`

---

# Real-time Analogy 📄

Imagine a company has an employee record.

```text
Employee Record
---------------
ID: 101
Name: Alice
Age: 25
Country: India
Salary: 50000
Phone: 9876543210
```

Different departments need different information.

## HR Department

Needs only:

* Name
* Age
* Country

## Finance Department

Needs only:

* Name
* Salary
* Annual Salary

## Public Directory

Needs only:

* Name
* Country

The original employee record doesn't change. Each department simply receives a customized version of it.

Think of **`$project`** as creating a customized report: you choose which fields to show, which to hide, rename column names, or even add new calculated fields, while leaving the original document unchanged.
