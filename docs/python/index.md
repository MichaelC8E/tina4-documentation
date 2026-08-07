# Tina4 for Python Engineers

You already write Python. You have a Flask, FastAPI or Django habit, and it works. So this
page is not "why Python" — it is the narrower question: **what do you get by using Tina4
instead, and what does it cost you?**

Tina4 is a batteries-included web framework for Python 3.12+. Routing, ORM, migrations,
templates, authentication, sessions, queues, caching, WebSocket, SSE, GraphQL, SOAP and
generated OpenAPI docs are all in the box — as one package, with **zero third-party
dependencies**. You install `tina4-python` and nothing else.

## Why not just use Flask?

Flask is excellent at being small. The cost shows up later, when the app needs the things
Flask deliberately leaves out. A database layer, migrations, JWT auth, and browsable API
docs are four separate packages, four upgrade cycles, and four sets of conventions you
choose and then have to defend in code review.

Tina4 makes those choices for you and ships them together. That is the entire trade: less
freedom, fewer decisions, fewer moving parts.

::: info Measured comparison in progress
The honest version of this comparison builds the *same* app in Tina4, Flask, FastAPI and
Django — database-backed list, a JWT-protected write, and Swagger docs — then counts what
each one needs to get there. Feature-matched, or not published at all: a "hello world"
benchmark is the one case where Flask needs nothing extra, and it would flatter us for the
wrong reason.
:::

What is already measured, on `tina4-python` 3.13.95 with CPython 3.14.5 (2026-08-07):

| | Tina4 |
|---|---|
| Third-party packages installed | **0** — `site-packages` holds `tina4_python`, its dist-info, and the virtualenv shims. Nothing else |
| Install size | 3.9 MB, 105 `.py` files, fresh install |
| Built-in features | 97, each replacing a dependency you would otherwise add ([Chapter 38](38-feature-list.md)) |
| Python floor | 3.12 |

### What zero dependencies buys you

No resolver conflicts. One package to upgrade. A supply chain that is one name long.
A fresh project resolves like this, and this is the whole install:

```
Resolved 2 packages in 572ms
Installed 1 package in 9ms
 + tina4-python==3.13.95
```

The longer version of this argument: [What is Tina4](/general/01-what-is-tina4).

### What it costs you

Worth knowing before you commit:

- **Raw throughput is not the pitch.** A bare ASGI framework serving plain JSON will beat
  Tina4 on requests per second. If your bottleneck is a JSON echo, reach for one. Our
  published throughput figures are being re-measured, so this page quotes none.
- **The ecosystem is a fraction of Django's.** No third-party app directory, far fewer
  Stack Overflow answers, fewer people who have hit your bug before.
- **The conventions are fixed.** Routes live in `src/routes/`, templates in
  `src/templates/`, models in `src/orm/`. The framework will not be talked out of it.

## How it works

Three things to understand. Read these; do not run them yet —
[Chapter 1](01-getting-started.md) is where you type.

**Routes are files.** Drop a `.py` file into `src/routes/`, decorate a handler, and it is
live. Path parameters arrive as arguments:

```python
from tina4_python.core.router import get

@get("/api/bookmarks/{tag}")
async def bookmarks(tag, request, response):
    return response.json({"tag": tag, "items": []})
```

No registry, no blueprint, no `app.include_router`. The framework discovers every file
under `src/routes/` at boot, and the file watcher picks up your edits without a restart.

GET routes are public. `POST`, `PUT`, `PATCH` and `DELETE` require a token by default —
writes are closed until you open them with `@noauth()`. That is a feature, and it will be
the first thing that surprises you.

**Pages are templates.** `src/templates/` holds Frond templates, which are
Twig-compatible, with inheritance and blocks:

```twig
{% extends "base.html" %}
{% block content %}<h1>{{ title }}</h1>{% endblock %}
```

```python
return response.render("bookmarks.html", {"title": "Bookmarks"})
```

**Everything has a place.** The scaffold is the convention:

```
src/routes/      handlers, auto-discovered
src/orm/         models, auto-discovered
src/templates/   Frond templates
src/public/      static files, served at /
src/scss/        SCSS, compiled to src/public/css/
migrations/      SQL migrations
.env             configuration
```

And this is live before you write a line of code:

```
/            welcome page
/health      liveness probe
/swagger     API docs, generated from your routes
/__dev       dev dashboard: routes, requests, SQL runner, queues, mailbox
```

The OpenAPI spec behind `/swagger` is generated from the routes you declared — no
annotations, no schema file. It is served at `/swagger/openapi.json` as OpenAPI 3.0.3.

## What's built in

Orientation only. Each entry links the chapter that owns it; the
[Quick Reference](quick-reference.md) has the syntax.

| | |
|---|---|
| **Web** | [routing](02-routing.md) · [request & response](03-request-response.md) · [templates](04-templates.md) · [middleware & security](10-middleware-security.md) |
| **Data** | [database](05-database.md) · [ORM](06-orm.md) · [query builder](07-query-builder.md) · [migrations](05-database.md) |
| **Auth** | [JWT & tokens](08-authentication.md) · [sessions & cookies](09-sessions-cookies.md) |
| **APIs** | [Swagger/OpenAPI](20-swagger.md) · [API client](21-api-client.md) · [GraphQL](22-graphql.md) · [SOAP/WSDL](25-wsdl-soap.md) |
| **Realtime** | [WebSocket](23-websocket.md) · [SSE](24-sse.md) · [WebRTC](39-realtime-webrtc.md) |
| **Ops** | [queues](12-queues.md) · [caching](11-caching.md) · [logging](15-logging.md) · [deployment](34-deployment.md) |
| **Tooling** | [dev tools](30-dev-tools.md) · [CLI](31-cli.md) · [scaffolding](19-scaffolding.md) · [testing](18-testing.md) |

Checking whether something ships before you reach for a package?
[Chapter 38: Complete Feature List](38-feature-list.md) — 97 features, each naming the
dependency it replaces.

## Start here

- **[Chapter 1: Getting Started](01-getting-started.md)** — install from nothing, run it,
  and build a working endpoint and page.
- **[Quick Reference](quick-reference.md)** — you know what you want, you need the syntax.
- **[Chapter 38: Complete Feature List](38-feature-list.md)** — is it already in the box?

::: tip In a hurry, and the CLI is already installed?

```bash
tina4 init python my-app
```

[Chapter 1](01-getting-started.md) covers what that does, and what to do when it does not.
:::

***

## 📕 Download the book

[**Tina4 for Python Developers** (PDF)](/pdfs/Tina4-for-Python-Developers.pdf): full reference, printable, with clickable table of contents and PDF outline. Regenerated with every release.
