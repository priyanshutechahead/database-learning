# Aggregation Basics

The aggregation framework is a powerful pipeline tool used to process, transform, and analyze documents. Think of it as an assembly line where data enters, passes through distinct stages, and comes out transformed.

Here are the four foundational stages:

---

# `$match` (The Filter)

Filters the documents to pass only those that match the specified conditions to the next stage.

Always place this as early as possible in the pipeline to reduce the dataset size.

```javascript id="j4p9md"
// Filter for only active users
{
  $match: {
    status: "active"
  }
}
```

---

# `$group` (The Aggregator)

Groups input documents by a specified identifier expression (`_id`) and applies accumulator functions (like `$sum`, `$avg`, `$max`) to the grouped data.

```javascript id="yv7k2s"
// Group by country and calculate total users per country
{
  $group: {
    _id: "$country",
    totalUsers: {
      $sum: 1
    }
  }
}
```

---

# `$sort` (The Organizer)

Orders the incoming documents by a specified field.

Use `1` for ascending order and `-1` for descending order.

```javascript id="pq5wte"
// Sort by totalUsers in descending order
{
  $sort: {
    totalUsers: -1
  }
}
```

---

# `$project` (The Reshaper)

Reshapes each document in the stream by including, excluding, or renaming fields.

It can also compute new calculated fields.

```javascript id="mz8qxa"
// Pass only the country name (renamed) and total users, hide the default _id
{
  $project: {
    _id: 0,
    countryName: "$_id",
    totalUsers: 1
  }
}
```

---

# Putting it Together: The Pipeline

```javascript id="x3rnvu"
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
  },
  {
    $sort: {
      totalUsers: -1
    }
  },
  {
    $project: {
      _id: 0,
      countryName: "$_id",
      totalUsers: 1
    }
  }
]);
```
