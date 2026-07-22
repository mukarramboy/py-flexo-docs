# Deployment

PyFlexo is a WSGI application. Deploy it behind a WSGI server.

## Example with Gunicorn

If your application object is named `app` in `main.py`, run:

```bash
gunicorn main:app
```

You can bind to a public interface and port as needed:

```bash
gunicorn --bind 0.0.0.0:8000 main:app
```

## Static Files in Deployment

PyFlexo serves static files through WhiteNoise from the configured `static_dir` under `/static`.

That means a small deployment can serve both the application and its static assets from the same WSGI process.

> **Tip**
> If you later place the app behind a reverse proxy, keep the `/static` path available to the application or let the proxy serve the same files directly.

## Related Documentation

- [Getting Started](getting-started.md)
- [Core Concepts](core-concepts.md)
- [API Reference](api-reference.md)
