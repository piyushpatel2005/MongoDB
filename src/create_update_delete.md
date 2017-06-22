# Creating, Updating and Deleting Documents

This part explains three of the CRUD operations for MongoDB.

## Inserting

```javascript
db.foo.insert({"bar": "baz"});
```

To insert multiple documents in single command, use batchInsert(). It takes an array of documents.
```javascript
db.foo.batchInsert([{"_id": 0}, {"_id": 1}, {"_id": 2}]);
```

## Removing

```
db.foo.remove()
```
removes all documents from foo collection.

```
db.blog.list.remove({"name": "hi"})
```
removes only those that have "name" equal to "hi".

To remove entire collection, use drop() method. Whole collection along with metadata is removed.
```
db.blog.drop()
```

## Updating

```
db.blog.update({"title": "My blog"}, {"title": "My title", "content": "New content"});
```

Using `_id` to update a collection is faster and safer as it ensures only one document will get updated and it is indexed.

### Atomic updates
Atomic update modifiers can be used to specify complex update operations.

**$inc**
This will add 1 to the pageviews for a given url.
```
db.analytics.update({"url": "www.piyushpatel2005.com"}, {"$inc": {"pageviews": 1}});
```
**$set** sets the value of a field. It can be used when we want to add only one field keeping all other fields intact.
```
db.users.update({"name": "joe"}, {"$set": {"favorite book": "Green Lantern"}});
```
**$unset** to remove the key altogether.
```
db.users.update({"name": "joe"}, {"$unset": {"favorite book": 1}});
```
This removes `favorite book` key altogether.

**$push** to add element to the end of an array.
```javascript
db.blog.posts.update(
  {"title": "A blog post"},
  {"$push": {
    "comments": {
      "name": "joe",
      "email": "xyz@joe.com",
      "content": "nice post."
    }
  }
}
);
```
**$each** can be used to push multiple values in one operation.
```
db.stock.ticker.update({"_id": "GOOG"},
{"$push": {"hourly": {"$each": [562.4, 564,7, 559,123]}}});
```
**$slice** can be used to create a queue in the document.
```
db.movies.find({"genre": "Horror"},
{"$push": {"top10": {
  "$each": [
    {"name": "Nightmare on Elm Street", "rating": 6.6},
    {"name": "Saw", "rating": 4.3}
  ],
  "$slice": -10,
  "$sort": {"rating" -1}
  }}});
```
sorts the top10 movies based on rating
and limits the result to top 10 only using slice.
**$ne** used for meaning not equal to
```
db.papers.update({
  "authors cited": {
    "$ne": "Richie"
    }
  },
  {"$push": {
    "authors cited": "Richie"
    }})
```

**$eq** equal to
```
db.inventory.find({qty: {$eq: 20}});
```
**$gt** greater than
**$gte** greater than or equal to
**$lt** less than
**$lte** less than or equal to
**$in** selects documents where the value of a field equals any value in the specified array.
```
db.inventory.find({qty: {$in: [5, 15]}})
```
**$nin** not in
**$addToSet** to avoid repetition of the same value.

**$pop** can remove elements from arrays from either end.

```
db.lists.insert({"todo": ["dishes", "laundry", "dry cleaning"]});
db.lists.update({}, {"$pop": {"todo": -1}})
```
removes from the beginning. If value 1 is supplied, it removes from the end.

**$pull** to remove elements of an array that match the given criteria.
```
db.lists.update({}, {"$pull": {"todo": "laundry"}});
```
It removes all the elements with matching criteria.

**Positional Array modification**
```
db.blog.update({"post": <post_id>}, {"$inc": {"comments.0.votes": 1}})
```
We cannot keep track of the index. Hence, there is yet another operator for positional modification.
```
db.blog.update({"comments.author": "John"},
{"$set": {"comments.$.author": "Jim"}})
```
This updates only the first match.

### Upserts
- special type of update.
- If no document is found that matches update criteria, a new document will be crated by combining criteria and updated documents. If document is found, it will be updated normally.

This is set using the third argument to update.
```
db.analytics.update({"url": "/blog"}, {"$inc": {"pageviews": 1}}, true)
```
If no document found for this. It will create one like this:
```
db.analytics.insert({"url": "/blog", "pageviews": 1})
```

Updates only update the first document found that matches the criteria. To update all documents, you can add fourth argument to update to true.
