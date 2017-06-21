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
