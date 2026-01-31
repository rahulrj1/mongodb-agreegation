# Aggregation Pipeline Examples

## 1. Count of users that are active

```javascript
[
  {
    $match: {
      isActive: true
    }
  }, {
    $count: 'activeUsers'
  }
]
```

## 2. Find the average age of all the users

```javascript
[
  {
    $group: {
      _id: null, 
      averageAge: {
        $avg: "$age"
      }
    }
  }
]
```

## 3. Find the average age of males and females

```javascript
[
  {
    $group: {
      _id: "$gender", 
      averageAge: {
        $avg: "$age"
      }
    }
  }
]
```

## 4. Find the 2 most common favoriteFruit among users

```javascript
[
  {
    $group: {
      _id: "$favoriteFruit",
      _count: {
        $sum: 1
      }
    }
  }, 
  {
    $sort: {
      _count: -1
    }
  }, 
  {
    $limit: 2
  }
]
```

## 5. Which two countries has highest number of registered users?

```javascript
[
  {
    $group: {
      _id: "$company.location.country",
      userCount: {
        $sum: 1
      }
    }
  }, 
  {
    $sort: {
      userCount: -1
    }
  },
  {
    $limit: 2
  }
]
```

## 6. Find the average number of tags per user

**1st way**

```javascript
[
  {
    $unwind: "$tags"
  },
  {
    $group: {
      _id: "$_id",
      numberOfTags: {
        $sum: 1
      }
    }
  },
  {
    $group: {
      _id: null,
      averageNumberOfTags: {
        $avg: "$numberOfTags"
      }
    }
  }
]
```

**2nd way**

```javascript
[
  {
    $addFields: {
      numberOfTags: {
        $size: {$ifNull: ["$tags", []]}
      }
    }
  },
  {
    $group: {
      _id: null,
      averageNumberOfTags: {
        $avg: "$numberOfTags"
      }
    }
  }
]
```

## 7. How many users have enim as one of their tags

```javascript
[
  {
    $match: {
      tags: "enim"
    }
  }, 
  {
    $count: 'UsersWithEnimTag'
  }
]
```

## 8. What are the names and ages of all the users that are inactive and has "velit" as one of their tags?

```javascript
[
  {
    $match: {
      isActive: false,
      tags: "velit",
    },
  },
  {
    $project: {
      name: 1,
      age: 1,
    },
  },
]
```

## 9. Categorize users by their favoriteFruit

```javascript
[
  {
    $group: {
      _id: "$favoriteFruit",
      users: { $push: "$name"}
    }
  }
]
```

## 10. How many users have their second tag as addFields

```javascript
[
  {
    $match: {
      "tags.1": "ad"
    }
  },
  {
    $count: 'secondTagAd'
  }
]
```

## 11. Find users who have both "enim" and "id" as their tags

```javascript
[
  {
    $match: {
      tags: {$all: ["enim", "id"]}
    }
  }
]
```

## 12. List all the companies located in USA and their corresponding user count

```javascript
[
  {
    $match: {
      "company.location.country": "USA"
    }
  }, 
  {
    $group: {
      _id: "$company.title",
      userCount: {$sum: 1}
    }
  }
]
```

## 13. List all the books along with their author details

```javascript
[
  {
    $lookup: {
      from: "authors",
      localField: "author_id",
      foreignField: "_id",
      as: "authorDetails"
    }
  }, 
  {
    $addFields: {
      authorDetails: {
        $first: "$authorDetails"
      }
    }
  }
]
```

**Alternative way**

```javascript
[
  {
    $lookup: {
      from: "authors",
      localField: "author_id",
      foreignField: "_id",
      as: "authorDetails"
    }
  }, 
  {
    $addFields: {
      authorDetails: {
        $arrayElemAt: ["$authorDetails", 0]
      }
    }
  }
]
```
