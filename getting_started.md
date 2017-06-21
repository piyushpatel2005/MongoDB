## Useful Terms:
**Document:** - Basic unit of data - similar to row in relational DB

**Collection:** - It is a group of documents. Similar to table in relational world.

Documents are key-value pairs like this:

```javascript
{"greeting": "Hello, World"}
```

Key must not be null character '\0'.
It cannot be . or $ as they have special meaning in Mongo.
Field order does not matter.

## Collections:

- Collections have dynamic schemas. They can be of any shape. This means they can have any number of keys and any key can have another document.
- Collection names may not contain '\0' or '$'.
- You should not create any collections that start with system as it is reserved for internal collections.

- Subcollections can be grouped by separating them with '.'. For example, application containing blog might have 'blog.posts' and 'blog.authors' collection.

## Starting MongoDB

MongoDB is almost always run as a network server. To start the server, run

    ```
    mongod
    ```
It will use default data directory '/data/db'. MongoDB listens on port 27017.  If port is not available, the server will fail.

To start the mongo shell, run the ```mongo``` command. It will connect to default db 'test'.

**The shell is a full-featured JavaScript interpreter**

Type command ```db``` to see the current db.
To change the database, use ```use foobar```. This will change the database to `foobar`


## Basic Operations

### Create

```javascript
  db.blog.insert({"title": "My blog", "content": "Here's blog content", "date": new Date()});
```

Alternatively, you could define a variable like this and then assign it to the blog collection.

```javascript
  post = {
    "title": "My blog",
    "content": "Here's my blog content",
    "date": new Date()
};

db.blog.insert(post);
```

To see the inserted collection, we can call find() method.

```javascript
db.blog.find()
```

It returns the inserted collection with a new key `_id`.

### Read

find() and findOne() can be used to query a database.

```javascript
  db.blog.findOne()
  ```
`find` and `findOne` can be passed criteria in the form of a query document to refine search. It is similar to WHERE statement in SQL. The shell will display up to 20 documents matching a find.

### Update

- Update takes two parameters. First, to find which document to update, second is the new document.

```javascript
post.comments = [];
db.blog.update({"title": "My blog"}, post);
```

### Delete

`remove` permanently removes documents from database. If called without any parameters, it would delete all documents from a collection.

```javascript
  db.blog.remove({"title": "My blog"});
```
