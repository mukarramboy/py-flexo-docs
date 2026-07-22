# API Reference

This reference lists the public classes and methods implemented by PyFlexo.

## Application

### `py_flexo.app.PyFlexoApp`

```python
PyFlexoApp(templates_dir="templates", static_dir="static")
```

Creates the main WSGI application object.

#### Methods

- `__call__(environ, start_response)`
- `add_route(path, handler, allow_methods=None)`
- `route(path, allow_methods=None)`
- `test_session()`
- `template(template_name, context=None)`
- `add_exception_handler(handler)`
- `add_middleware(middleware_cls)`
- `wsgi_app(environ, start_response)`
- `handle_request(request)`
- `find_handler(path)`
- `default_response(response)`
- `method_not_allowed_response(response)`

#### Notes

- Import it from `py_flexo.app`.
- Use `route()` as a decorator for function-based or class-based views.
- `test_session()` returns a `requests.Session` mounted to `http://testserver`.

## Response

### `py_flexo.response.Response`

```python
Response()
```

Represents the framework response object.

#### Attributes

- `json`
- `html`
- `text`
- `content_type`
- `body`
- `status_code`

#### Notes

- Set `json`, `html`, or `text` before returning the response.
- `status_code` defaults to `200`.

## Middleware

### `py_flexo.middleware.Middleware`

```python
Middleware(app)
```

Wraps an application instance.

#### Methods

- `add(middleware_cls)`
- `process_request(req)`
- `process_response(resp)`
- `handle_request(request)`
- `__call__(environ, start_response)`

#### Notes

- Subclass `Middleware` to define custom request and response hooks.
- In the current release, the default request path does not call the hook methods automatically.

## ORM

### `py_flexo.orm.Database`

```python
Database(path)
```

Opens a SQLite connection at `path`.

#### Properties

- `tables`

#### Methods

- `create(table)`
- `save(instance)`
- `all(table)`
- `get(table, id)`
- `update(instance)`
- `delete(instance)`

### `py_flexo.orm.Table`

```python
class Author(Table):
    name = Column(str)
    age = Column(int)
```

Base class for ORM models.

#### Notes

- Use the class name as the table name source.
- Pass field values through the constructor as keyword arguments.
- Each instance has an `id` attribute that starts as `None`.

### `py_flexo.orm.Column`

```python
Column(column_type)
```

Declares a database column.

Supported Python types in the current release:

- `str`
- `int`
- `float`
- `bool`

### `py_flexo.orm.ForeignKey`

```python
ForeignKey(table)
```

Declares a relationship to another `Table` subclass.

## Related Documentation

- [Getting Started](getting-started.md)
- [Core Concepts](core-concepts.md)
- [ORM](orm/README.md)
- [Deployment](deployment.md)
