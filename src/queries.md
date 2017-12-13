# Querying

find() method is used to perform queries in MongoDB.
First argument to find specifies query criteria.
If {} is passed, entire collection is returned.

```
db.users.find({"username": "joe"})
```
Two conditions can be specified as below. Condition1 and condition2
`
db.users.find({"username": "joe", "age": 27})
`

---
## Getting specific results from Query

This is passed as second argument to find().
```
db.users.find({}, {"username": 1, "email": 1})
```
returns only username and email. `_id` is always returned. To get results without `_id`, you need to explicitly filter results.

```
db.users.find({}, {"username": 1, "_id": 0})
```
This would result only username without any id field.

## Conditional queries

```
db.users.find({"age": {"$gte": 18, "$lte": 30}})
```
This would return all users with age greater than or equal to 18 and less than or equal to 30.

```
start = new Date("01/01/2017")
db.users.find({"registeredOn": {"$lt": start}})
```

To find users from a list of users, you can use `$in`. It is similar to VALUES...IN in relational database systems.
```
db.users.find({"user_id": {"$in": [12345, "joe"]}})
```

The opposite of `$in` is `$nin`.

**$or** to find documents where one or both conditions are true.
```
db.raffle.find({"$or": [{"ticket_no": 725}, {"winner": true}]})
```
This will find documents where "ticket_no" is 725 or "winner" is set to true.

**Note:** `$in` is more efficient than `$or`.

```
db.users.find({"id_num": {"$mod": [5, 1]}})
```
would return all users with id_num of 1, 6, 11, 16, etc. All id_num whose value when divided by first value(5) returns modulus of second value(1)
The opposite of that would return id_num of 2, 3, 4, 5, 7, 8, etc.

`
db.users.find({"id_num": {"$not": {"$mod": [5,1]}}})
`

**$and** to find documents that match both condition.

Multiple conditions can be put on a single key.
`
db.users.find({"age": {"$lt": 30, "$gt": 20}})
`

**Querying for null is bit different** as null not only matches itself but also matches "does not exist." In this case, we also need to make sure that the key exists.

`
db.c.find({"x": {"$in": [null], "$exists": true}})
`

- Regular Expressions can also be used.

`
db.users.find({"name": /joey>?/i})
`

### Querying Arrays

```javascript
db.food.insert({"fruit": ["apple", "banana", "peach"]});
db.food.find({"fruit": "banana"})

```

will successfully match the document.

To find any document with two elements, we can use `$all`.

```javascript
db.food.find({fruit: {$all: ["apple", "banana"]}});
```

This would return all documents that have both apple and banana, not those with only one of them.

```javascript
db.food.find({"fruit": ["apple", "banana", "peach"]});
```

will not find ["apple", "banana", "grapes"].

**$size** allows for querying for array of different size.

```
db.food.find({"fruit": {"$size": 3}})
```

will find "fruit" key with size 3.

```
db.food.find({"size": {"$gt": 3}})
```


**$slice** can be used to return a subset of elements for an array key.

```
db.blog.posts.findOne(CRITERIA, {"comments": {"$slice": 10}})
```

would return first 10 comments.

If specified as -10, would return last 10 comments.

- $slice can also be used to return pages like pagination.

```
db.blog.posts.findOne(CRITERIA, {"comments": {"$slice": [23, 10]}})
```

would return comments from 24th through 33th. It is similar to slice in JavaScript.

## Querying Embedded Documents.

two methods:

```
db.people.find({"name": {"first": "Piyu", "last": "Patel"}})
```

This means the "name" query must exactly match the subdocument.

---
It's usually better to query for just a specific key using dot-notation. Even if more keys are added to the name key, this query will still work as expected.

```javascript
db.people.find({"name.first": "Piyu", "name.last": "Patel"})
```

To find element from arrays, we can use "$elemMatch" which allows multiple condiitions.

```
db.blog.find({"comments": {"$elemMatch": {"author": "joe", "score": {"$gte": 5}}}})
```

This would find all comments with of Joe with score greater than or equal to 5.

**$where** allows to execute JavaScript in the query. This is discouraged as it can cause security issue.

### Cursors:
Database uses cursor to return results from database.

```
var cursor = db.collection.find()
```

To iterate through the results, you can use the next method and hasNext().

```javascript
while(cursor.hasNext()) {
  obj = cursor.next();
  // other stuff.
}

```

- We can also use forEach function with cursor.

### Limit, Skip and sort

To set a limit, chain the limit function onto call to find.

```
db.search.find().limit(3)
```
will return only three documents. `limit` sets an upper limit, not a lower limit.

```
db.search.find().skip(3)
```
will skip first three documents and return the rest of the matches.

```
db.search.find().sort({"username": 1, "age": -1})
```

This will find the results and then search using username and age. Sort direction will be ascending for username and descending for age.

- To create pagination for small skips.

```
db.stock.find({"desc": "mp3"}).limit(50).sort({"price": -1});

db.stock.find({"desc": "mp3"}).limit(50).skip(50).sort({"price": -1})
```

- Pagination without skip

```javascript
var page1 = db.foo.find().sort({"date": -1}).limit(100);

var latest = null;

// display first page
while(page1.hasNext()) {
  latest = page1.next();
  display(latest);
}

//get next page
var page2 = db.foo.find({"date": {"$gt": latest.date}});
page2.sort({"date": -1}).limit(100);
```
