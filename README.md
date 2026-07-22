# PyFlexo Documentation

PyFlexo is a small WSGI web framework with explicit routing, template rendering, static file serving, middleware hooks, exception handling, and a lightweight SQLite ORM.

This documentation covers the public API that is implemented in the repository. It does not describe planned features or framework conventions that are not yet present in the codebase.

> **Note**
> Package-level imports are not re-exported from `py_flexo.__init__`. Import objects from the module that defines them.

Example imports:

```python
from py_flexo.app import PyFlexoApp
from py_flexo.orm import Database, Table, Column, ForeignKey
from py_flexo.middleware import Middleware
```

## Documentation Map

- [Getting Started](docs/getting-started.md)
- [Core Concepts](docs/core-concepts.md)
- [ORM](docs/orm/README.md)
- [Deployment](docs/deployment.md)
- [API Reference](docs/api-reference.md)
- [Changelog](docs/changelog.md)

## What to read first

If you are new to the framework, start with [Getting Started](docs/getting-started.md). If you are already building an application, move to [Core Concepts](docs/core-concepts.md) and then the [API Reference](docs/api-reference.md).

## Related Documentation

- [ORM documentation](docs/orm/README.md)
