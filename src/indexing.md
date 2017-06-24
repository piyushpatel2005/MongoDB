# Indexing

Indexing makes querying faster. To understand what MongoDB is doing, we can use explain() function.

```
db.users.find().explain()
```

Searching any field by querying without indexes and with indexes makes a significant performance improvement.

To create an index, we use ensureIndex() function.

```
db.users.ensureIndex({"username": 1})
```

**Decide to create index on those queries which are used frequently.**

Sometimes, you may need compound indexes when you are searching based on more than two keys.

```
db.users.ensureIndex({"age": 1, "username": 1})
```
