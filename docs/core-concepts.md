# Core Concepts

This page explains the application features that make up the supported request and response flow in PyFlexo.

## Application

The central object is `PyFlexoApp`.

It stores routes, loads Jinja2 templates from the configured template directory, serves static files under `/static`, and exposes the app as a WSGI callable.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()
```

> **Note**
> `PyFlexoApp` is a WSGI application object. You do not need a separate wrapper class.

## Routing

Routes are registered with the `route()` decorator.

A route can be a function-based view or a class-based view.

### Function-based views

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/")
def index(request, response):
    response.text = "Hello, world!"
```

### Dynamic route parameters

PyFlexo uses the `parse` library to match route parameters.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/hello/{name}")
def hello(request, response, name):
    response.text = f"Hello {name}"
```

### Class-based views

If the route target is a class, PyFlexo instantiates the class and dispatches to a method that matches the HTTP method name in lowercase.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/books")
class Books:
    def get(self, request, response):
        response.text = "Book list"

    def post(self, request, response):
        response.text = "Create book"
```

> **Warning**
> The current default `allow_methods` list for function routes includes `path` instead of `patch`. If you want to support `PATCH`, pass it explicitly in `allow_methods`.

## Requests

Handlers receive a `webob.Request` object as the first argument.

The examples in this repository use `request.method` and `request.path`.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/inspect")
def inspect_request(request, response):
    response.text = f"{request.method} {request.path}"
```

## Responses

Handlers receive a `Response` instance as the second argument.

Set one of the payload helpers on the response object:

- `response.text` for plain text
- `response.html` for HTML
- `response.json` for JSON

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/api")
def api(request, response):
    response.json = {"framework": "PyFlexo"}
```

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/page")
def page(request, response):
    response.html = "<h1>Hello</h1>"
```

> **Tip**
> Set only one response payload helper per handler. The framework resolves the body when the response is rendered.

## Templates

PyFlexo renders templates with Jinja2.

`PyFlexoApp.template()` loads a template from the configured template directory and returns the rendered string.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/home")
def home(request, response):
    response.html = app.template(
        "home.html",
        context={"title": "PyFlexo", "header": "Welcome"},
    )
```

## Static Files

PyFlexo serves files from the configured static directory under `/static`.

For example, `static/home.css` is available at `/static/home.css`.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp(static_dir="static")
```

## Middleware

The `Middleware` base class lets you wrap an application object and define request and response hooks.

```python
from py_flexo.app import PyFlexoApp
from py_flexo.middleware import Middleware


class LoggingMiddleware(Middleware):
    def process_request(self, req):
        print(req.method, req.path)

    def process_response(self, resp):
        print(resp.status_code)


app = PyFlexoApp()
app.add_middleware(LoggingMiddleware)
```

> **Warning**
> The current request path does not call `process_request()` and `process_response()` automatically. The hooks exist, but they are not wired into the default middleware flow in this release.

## Exception Handling

You can register a global exception handler for route handlers.

The handler receives the request, the response object, and the exception instance.

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


def exception_handler(request, response, exception):
    response.status_code = 500
    response.text = "Internal Server Error"


app.add_exception_handler(exception_handler)
```

> **Note**
> The exception handler is invoked for exceptions raised inside route handlers when the app has already matched a route.

## Best Practices

- Keep route handlers small and explicit.
- Save related ORM objects before inserting a record that references them.
- Use `app.test_session()` for in-process tests instead of starting a server.
- Prefer `response.text`, `response.html`, or `response.json` over assigning to `response.body` directly.

## Related Documentation

- [Getting Started](getting-started.md)
- [ORM](orm/README.md)
- [API Reference](api-reference.md)
- [Deployment](deployment.md)
