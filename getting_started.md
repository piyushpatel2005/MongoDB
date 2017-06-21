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

## Data Types

1. null
```javascript
  {"x": null}
```

2. Number: This defaults to 64-bit floating point number.
```javascript
  {"x": 3.14}

  {"x": NumberInt("3")}
  {"x": NumberLong("3")}
```

3. String:
```javascript
  {"x": "foobar"}
```

4. Date: stored as miliseconds since the epoch.
```javascript
  {"x": new Date()}
```

5. Regular expressions:
```javascript
  {"x": /foobar/i}
```

6. Array
```javascript
  {"x": ["a", "b", "c"]}
```

7. Embedded documents
  Documents can contain entire documents as values in parent document.
```javascript
  {"x": {"foo": "bar"}}
```

8. Object Id: 12-byte ID
Every document must have an `_id` key. It defaults to ObjectId. In a single collection, every document must have a unique value for id. MongoDB's distributed nature makes it difficult and time-consuming to synchronize autoincrementing primary keys across multiple servers if auto increment is used. Therefore, it uses ObjectId.

12-byte ObjectId is created as follows

First 4 bytes: Timestamp
3 bytes: Machine
2 bytes: PID
3 bytes: Increment.

As timestamp comes first, they can sort by insertion order.

```javascript
  {"x": ObjectId()}
```

9. Code

```javascript
  {"x": function () {/*...*/}}
```

## MongoDB shell

To connect to the remove shell you can use this command.
```javascript
  mongo some-host:30000/myDB
```

You can find help using ```help``` command.
Or for specific help ```db.foo.help()```.

### Running scripts with the shell

To run three scripts, you can run using.

```
mongo script1.js script2.js script3.js
```

To run on a non-default host/port mongod, use

```
mongo --quiet server-1:30000/foo script1.js script2.js script3.js
```

To run scripts from interactive shell, use load() function:
```
load("script1.js")
```
JavaScript equivalent for Shell helpers.
use foo           db.getSisterDB("foo")
show dbs          db.getMongo().getDBs()
show collections  db.getCollectionNames()


You can run shell commands using Mongo run() function.

For example to use "pwd" command. Use following in Mongo shell.

```javascript
  run("pwd")
```

With .mongorc.js file, we can run initial scripts when we launch the mongo. This file goes in the home directory.

You can also customize the prompt in .mongorc.js file.

```javascript
prompt = function () {
  return (new Date()) + "> ";
};
```
OR

```javascript

prompt = function () {
  if(typeof db == 'undefined') {
    return '(nodb)> ';
  }

  try {
    db.runCommand({getLastError: 1});
  }
  catch(e) {
    print(e);
  }
  return db+"> ";
};
```
