# ORM

PyFlexo ORM is a small SQLite-backed object mapper built into PyFlexo.

It lets you define tables with Python classes, create tables from those classes, and work with rows as Python objects.

> **Note**
> ORM support is available in PyFlexo 0.2.0 and later.

## Overview

The ORM is intentionally narrow in scope.

It supports:

- Class-based table definitions
- Automatic primary key generation
- Basic column type mapping
- Insert, select, update, and delete operations
- Simple foreign key relationships

It currently supports SQLite only.

```python
from py_flexo.orm import Database, Table, Column, ForeignKey
```

## Models

Define a table by subclassing `Table`.

The class name becomes the table name in lowercase.

```python
from py_flexo.orm import Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)
```

Each instance has an `id` field that is assigned when the object is saved.

```python
author = Author(name="John", age=30)
print(author.id)
```

## Database

Create a database connection with `Database(path)`.

```python
from py_flexo.orm import Database

db = Database("database.db")
```

The `tables` property returns the table names that currently exist in the SQLite database.

```python
print(db.tables)
```

## Fields

### `Column`

Use `Column` for scalar values.

Supported Python types in the current release are:

- `str` -> `TEXT`
- `int` -> `INTEGER`
- `float` -> `REAL`
- `bool` -> `INTEGER`

```python
from py_flexo.orm import Table, Column


class Book(Table):
    title = Column(str)
    published = Column(bool)
```

### `ForeignKey`

Use `ForeignKey` to point at another table class.

```python
from py_flexo.orm import Table, Column, ForeignKey


class Author(Table):
    name = Column(str)


class Book(Table):
    title = Column(str)
    author = ForeignKey(Author)
```

## CRUD

### Create tables

Create tables from model classes with `Database.create()`.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)
```

### Save objects

Save an instance with `Database.save()`.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)

author = Author(name="John", age=30)
db.save(author)
print(author.id)
```

### Load one object

Use `Database.get()` to load one row by primary key.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)

author = Author(name="John", age=30)
db.save(author)

loaded = db.get(Author, author.id)
print(loaded.name)
```

### Load all objects

Use `Database.all()` to load all rows for a model.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)
db.save(Author(name="John", age=30))
db.save(Author(name="Jane", age=25))

authors = db.all(Author)
print(len(authors))
```

### Update objects

Modify the object and call `Database.update()`.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)

author = Author(name="John", age=30)
db.save(author)

author.name = "Jane"
author.age = 31
db.update(author)
```

### Delete objects

Use `Database.delete()` to remove a row.

```python
from py_flexo.orm import Database, Table, Column


class Author(Table):
    name = Column(str)
    age = Column(int)


db = Database(":memory:")
db.create(Author)

author = Author(name="John", age=30)
db.save(author)
db.delete(author)
```

## Relationships

Foreign keys are stored as references to other `Table` instances.

Save the related object first, then save the object that points to it.

```python
from py_flexo.orm import Database, Table, Column, ForeignKey


class Author(Table):
    name = Column(str)


class Book(Table):
    title = Column(str)
    author = ForeignKey(Author)


db = Database(":memory:")
db.create(Author)
db.create(Book)

author = Author(name="John")
db.save(author)

book = Book(title="Python", author=author)
db.save(book)

loaded = db.get(Book, book.id)
print(loaded.author.name)
```

When records are loaded with `get()` or `all()`, foreign keys are resolved back into model instances.

> **Warning**
> If you save a relationship before the related object has an `id`, the ORM will not have a valid foreign key value to store.

## API Reference

### `Database(path)`

Creates a SQLite connection.

### `Database.tables`

Returns the list of table names in the database.

### `Database.create(table)`

Creates the table described by a `Table` subclass.

### `Database.save(instance)`

Inserts a model instance and assigns its generated `id`.

### `Database.all(table)`

Returns all rows as model instances.

### `Database.get(table, id)`

Returns one model instance by primary key.

Raises a generic `Exception` if the row does not exist.

### `Database.update(instance)`

Updates an existing row from the current instance values.

### `Database.delete(instance)`

Deletes the row represented by the instance.

### `Table(**kwargs)`

Base class for ORM models.

Keyword arguments map directly to model fields.

### `Column(column_type)`

Declares a typed scalar field.

### `ForeignKey(table)`

Declares a relationship to another table class.

## Best Practices

- Create tables before saving any objects.
- Save related objects before saving rows that reference them.
- Keep model names singular and descriptive.
- Use `get()` when you expect exactly one object and `all()` when you need a collection.

## Related Documentation

- [Getting Started](../getting-started.md)
- [Core Concepts](../core-concepts.md)
- [API Reference](../api-reference.md)
- [Deployment](../deployment.md)
