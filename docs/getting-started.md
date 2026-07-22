# Getting Started

PyFlexo is a WSGI framework. You build an application by creating a `PyFlexoApp` instance, registering routes, and serving the app with a WSGI server.

## Installation

PyFlexo is published as `py-flexo`.

```bash
pip install py-flexo
```

If you use `uv`:

```bash
uv add py-flexo
```

> **Note**
> The source package is imported as `py_flexo`, even though the distribution name uses a hyphen.

## Quick Start

Create a file named `main.py`:

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/")
def home(request, response):
    response.text = "Hello, PyFlexo!"
```

Run the application with any WSGI server. For example, with Gunicorn:

```bash
gunicorn main:app
```

You can also exercise the app in-process during development:

```python
from py_flexo.app import PyFlexoApp

app = PyFlexoApp()


@app.route("/")
def home(request, response):
    response.text = "Hello, PyFlexo!"


client = app.test_session()
response = client.get("http://testserver/")
print(response.text)
```

> **Tip**
> Keep your template directory named `templates` and your static directory named `static` unless you need to customize them through `PyFlexoApp`.

## Related Documentation

- [Core Concepts](core-concepts.md)
- [API Reference](api-reference.md)
- [Deployment](deployment.md)
- [ORM](orm/README.md)
