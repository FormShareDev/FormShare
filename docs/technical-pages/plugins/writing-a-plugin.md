---
description: The structure of a FormShare plugin — directory layout, plugin class, interfaces, view classes, templates, static resources, htmx, async streaming, and schema extension.
---

# Writing a plugin

This page is the reference companion to "[Plugin development](README.md)". It assumes you have already scaffolded a plugin with the [cookiecutter template](README.md#scaffolding-a-new-plugin), installed it in development mode, and added it to FormShare's `.ini` file.

Throughout this page, examples assume the plugin is called `my_plugin` and the main class is `MyPlugin`. References to `u` mean `import formshare.plugins.utilities as u`.

## Directory structure

A typical plugin looks like this:

```
my_plugin/
  setup.py                    Package definition + entry points
  README.md
  alembic.ini                 (optional) DB migration config
  my_plugin/
    __init__.py
    plugin.py                 Main plugin class (declares interfaces)
    views.py                  View classes (endpoints)
    celery_plugin.py          (optional) Celery task hooks
    templates/                Jinja2 templates
      dashboard/              Override or extend core templates
        my_page.jinja2
    static/                   Static files served at /my_plugin/
      js/
      css/
    resources/                FanStatic library (JS/CSS resources)
      js/
      css/
    locale/                   i18n translations
      en/LC_MESSAGES/my_plugin.po
```

## Plugin registration

In `setup.py`, expose the plugin class through the `formshare.plugins` entry point group:

```python
from setuptools import setup, find_packages

setup(
    name="my_plugin",
    version="1.0",
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
    install_requires=["formshare >= 2.18.0"],
    entry_points={
        "formshare.plugins": [
            "my_plugin = my_plugin.plugin:MyPlugin",
        ],
    },
)
```

The left-hand side of the entry point (`my_plugin`) is what you list in `formshare.plugins` in the FormShare `.ini`. The right-hand side is the dotted path to the plugin class.

## The plugin class

The plugin class is a `SingletonPlugin` that declares which interfaces it implements:

```python
import formshare.plugins as plugins
import formshare.plugins.utilities as u
from .views import MyView, MyStreamView


class MyPlugin(plugins.SingletonPlugin):
    plugins.implements(plugins.IRoutes)
    plugins.implements(plugins.IConfig)
    plugins.implements(plugins.IResource)
    plugins.implements(plugins.ITranslation)

    # --- IRoutes ---

    def before_mapping(self, config):
        return []

    def after_mapping(self, config):
        return [
            u.add_route("my_page",
                        "/user/{userid}/my_feature",
                        MyView,
                        "dashboard/my_template.jinja2"),
            u.add_route("my_api",
                        "/user/{userid}/my_api",
                        MyAPIView,
                        "json"),
            u.add_route("my_sse",
                        "/user/{userid}/stream/{task_id}",
                        MyStreamView,
                        None),
        ]

    # --- IConfig ---

    def update_config(self, config):
        u.add_templates_directory(config, "templates")
        u.add_static_view(config, "myplugin", "static")

    # --- IResource ---

    def add_libraries(self, config):
        return [u.add_library("myplugin", "resources")]

    def add_js_resources(self, config):
        return [
            u.add_js_resource("myplugin", "main_js", "js/main.js"),
        ]

    def add_css_resources(self, config):
        return [
            u.add_css_resource("myplugin", "main_css", "css/main.css"),
        ]

    # --- ITranslation ---

    def get_translation_directory(self):
        import os, sys
        return os.path.join(
            os.path.dirname(sys.modules["my_plugin"].__file__),
            "locale",
        )

    def get_translation_domain(self):
        return "my_plugin"
```

## Interface catalog

FormShare exposes more than 40 interfaces. The authoritative definitions live in `formshare/plugins/interfaces.py`. Here is the summary you need to pick the right ones:

### Routes, config, resources

| Interface | Hooks |
| --- | --- |
| `IRoutes` | `before_mapping`, `after_mapping` — register web routes around the core ones. |
| `IAPIRoutes` | Register API routes. |
| `IConfig` | `update_config` — register template directories, static views. |
| `IResource` | `add_libraries`, `add_js_resources`, `add_css_resources` — register FanStatic libraries and resources. |
| `IEnvironment` | `after_environment_load` — final initialization hook after bootstrap completes. |

### Views

| Interface | Hooks |
| --- | --- |
| `IPublicView` | Hook into public page processing. |
| `IPrivateView` | Hook into authenticated page processing. |
| `IODKView` | Hook into ODK API endpoints. |
| `IAssistantView` | Hook into assistant views. |
| `IPartnerView` | Hook into partner views. |

### Data and processing

| Interface | Hooks |
| --- | --- |
| `ISchema` | `update_schema` — add custom fields to existing tables (stored as JSON extras). |
| `IDatabase` | `update_orm` — add new ORM tables to the FormShare database. |
| `IProject` | Lifecycle hooks: `before_creating_project`, `after_creating_project`, edit, delete. |
| `IForm` | Lifecycle hooks: `before_creating_form`, `after_creating_form`, edit, delete. |
| `IFormDataColumns` | Filter or modify form data columns. |
| `IXMLSubmission` | Hook into XML submission processing. |
| `IJSONSubmission` | Hook into JSON submission processing. |
| `IMediaSubmission` | Hook into media file processing. |
| `IDeleteSubmission` | Hook into submission deletion. |

### Authentication and authorization

| Interface | Hooks |
| --- | --- |
| `IUserAuthentication` | Hook into user login. |
| `IAssistantAuthentication` | Hook into assistant login. |
| `IPartnerAuthentication` | Hook into partner login. |
| `IUserAuthorization` | Custom authorization logic. |
| `IAuthenticationPolicy` | Custom authentication mechanisms. |
| `IUserPassword` | Password validation hooks. |

### Business logic

| Interface | Hooks |
| --- | --- |
| `IRegistration` | Hook into user registration (`before_register`, `after_register`). |
| `ITemplateHelpers` | Provide Jinja2 helper functions, available in templates as `request.h`. |
| `IProduct` | Define custom products for task management. |
| `IExport` | Custom export formats. |
| `IExportGenerator` | Generate exports (used by built-in CSV, Excel, etc.). |
| `IRepository` | Repository creation hooks. |
| `IRepositoryProcess` | Celery-based repository operations. |
| `IProjectEncryption` | Submission encryption hooks. |
| `IDatabaseEncryption` | Column-level encryption hooks. |
| `ITranslation` | Provide i18n translations. |
| `IPluralize` | Custom pluralization rules. |
| `IAPISecurity` | Filter sensitive data from API responses. |
| `IFormAccess` | Form access control hooks. |
| `IFormGroupAccess` | Form group access hooks. |
| `IUser` | User object hooks. |
| `ICollaborator` | Collaborator hooks. |
| `ILogOut` | Logout hooks. |
| `IPartner` | Partner lifecycle hooks. |
| `IImportExternalData` | Custom data importers (extends the [import external data](../../fundamentals/submissions/import-external-data.md) feature). |

For the exact method signatures, default arguments, and call sites of any of these interfaces, read `formshare/plugins/interfaces.py` directly.

## Helper utilities

Import the helpers as:

```python
import formshare.plugins.utilities as u
```

### Routes

```python
u.add_route(name, path, view, renderer)
```

`renderer` can be a Jinja2 template path (`"dashboard/my_template.jinja2"`), `"json"`, or `None` (for async views that return their own Response).

### Templates and static files

```python
u.add_templates_directory(config, "templates")
u.add_static_view(config, "myplugin", "static")
```

`add_templates_directory` registers a Jinja2 template search path. Templates in the plugin can override any core template by living at the same relative path and using `{% extend_me %}`.

`add_static_view` mounts the plugin's `static/` directory at `/myplugin/`. Reference files in templates with `request.url_for_static('js/script.js', 'myplugin')`.

### FanStatic resources

```python
u.add_library(name, path)
u.add_js_resource(library, resource_id, file, depends="CHAIN")
u.add_css_resource(library, resource_id, file, depends="CHAIN")
```

`depends="CHAIN"` makes the resource depend on the previously-defined resource (the typical pattern when you want a strict load order). Use `depends=None` for resources with no dependencies.

In templates, inject resources with the custom tags:

```jinja2
{% jsresource request, 'myplugin', 'main_js' %}
{% cssresource request, 'myplugin', 'main_css' %}
```

### Schema extension

Add a field to an existing FormShare table without changing the core model:

```python
u.add_field_to_user_schema(field_name, description)
u.add_field_to_project_schema(field_name, description)
u.add_field_to_assistant_schema(field_name, description)
u.add_field_to_assistant_group_schema(field_name, description)
u.add_field_to_form_schema(field_name, description)
u.add_field_to_form_access_schema(field_name, description)
u.add_field_to_form_group_access_schema(field_name, description)
```

Fields are stored as JSON extras on the corresponding row. Use with the `ISchema` interface.

## View classes

Base view classes live in `formshare/views/classes.py`. Plugin-facing wrappers in `formshare/plugins/utilities.py` expose them under stable names:

* **`FormSharePublicView`** — no login required. Override `process_view()` to return a dict (becomes the template variables).
* **`FormSharePrivateView`** — login required. Adds `self.user`, `self.activeProject`, CSRF checking, htmx event dispatch (`self.trigger_client_event`).
* **`FormShareProjectsView`** — requires a project context (`{userid}/project/{projcode}` route). Sets `self.user_id`, `self.project_code`, `self.project_id` automatically.
* **`FormShareFormEditorView`** — requires editor-or-above access on a form. Adds `self.form_id`.
* **`FormShareFormAdminView`** — requires admin access on a form. Adds `self.project_details`, `self.form_details`.
* **`FormShareAssistantView`** — assistant-facing endpoints.
* **`FormSharePartnerView`** — partner-facing endpoints.
* **`FormShareAsyncView`** — native async FastAPI endpoint. No thread pool, no SQLAlchemy session. Override `async def process_view(self, request)` and return a Starlette `Response` (typically `StreamingResponse` for SSE). Class attributes `methods = ["GET"]` and `requireAuth = True` control behavior.

Sync views' return values are processed by the dispatcher (`formshare/config/dispatcher.py`):

* `dict` + `.jinja2` renderer → rendered HTML.
* `dict` + `"json"` renderer → JSON response.
* `Response` / `FileResponse` → returned as-is.
* `HTTPException` raised → mapped to an error response.

The dispatcher **rolls back** the SQLAlchemy session at the end of every request. If your view modifies data, call `self.request.dbsession.commit()` explicitly before returning.

## Template inheritance with extend_me

Plugins can override any core FormShare template by placing a file at the same relative path inside their `templates/` directory. Use the `{% extend_me %}` Jinja2 tag to extend the core template:

```jinja2
{% extend_me %}

{% block css %}
    {{ super() }}
    <style>.my-custom { color: red; }</style>
{% endblock css %}

{% block some_section %}
    {# completely replace this block #}
    <div>My custom content</div>
{% endblock some_section %}
```

`{% extend_me %}` walks the template-path chain to find the next template at the same relative path and inherits from it. This is implemented in `formshare/config/jinja_extensions.py`.

## Partial rendering for htmx

FormShare uses htmx 2.x for partial page updates. Wrap sections of your templates with `partial_start` / `partial_end`:

```jinja2
{% partial_start 'my_section' %}
  <div class="my-content">
    ...
  </div>
{% partial_end 'my_section' %}
```

When the browser requests the same URL with `?partial=my_section`, FormShare renders the full page (so all view logic runs) but returns only the HTML between the markers. This is how panels refresh without full reloads.

To trigger htmx events from a view:

```python
self.trigger_client_event("formshare:files-updated")
# or with detail:
self.trigger_client_event(
    "formshare:notify",
    {"type": "error", "message": msg},
)
```

These set the `HX-Trigger` response header, which htmx fires as a custom DOM event in the browser. Multiple calls accumulate into a single header.

## Server-sent events (SSE) with AsyncView

For server-pushed streams (background task progress, log tailing, real-time notifications), use `FormShareAsyncView`:

```python
from formshare.plugins.utilities import FormShareAsyncView
from starlette.responses import StreamingResponse


class MyStreamView(FormShareAsyncView):
    methods = ["GET"]
    requireAuth = True

    async def process_view(self, request):
        task_id = request.path_params["task_id"]

        async def event_generator():
            yield ": connected\n\n"
            while True:
                if await request.is_disconnected():
                    break
                data = await get_next_event(task_id)
                yield f"data: {data}\n\n"

        return StreamingResponse(
            event_generator(),
            media_type="text/event-stream",
            headers={
                "Cache-Control": "no-cache",
                "X-Accel-Buffering": "no",
            },
        )
```

Register the route with `renderer=None` because the view returns its own Response:

```python
u.add_route("my_stream",
            "/user/{userid}/stream/{task_id}",
            MyStreamView,
            None)
```

`AsyncView` subclasses are auto-detected at startup and routed through the async dispatcher instead of the sync one. There is no thread pool, no SQLAlchemy session — the view is responsible for everything, including its own database access (typically through an async client).

The reference implementation in core is `formshare/views/task_stream.py`, which streams Celery task progress over Redis pub/sub.

## Persisting plugin settings

For key/value storage in the FormShare database, use `FormShareSettings`:

```python
from formshare.plugins.utilities import FormShareSettings

settings = FormShareSettings(self.request)
settings.store("my_plugin.api_key", "abc123")
value = settings.get("my_plugin.api_key")
settings.update("my_plugin.api_key", "xyz789")
settings.delete("my_plugin.api_key")
```

This is the right place for plugin-wide settings that you don't want hard-coded in the `.ini` file (because they need to be editable at runtime, or because they are secrets that belong in the database).

## Boot sequence

When FormShare starts up, plugins are loaded and their interface methods are called in this order:

1. Plugin classes loaded from entry points.
2. `IConfig.update_config(config)` — templates and static directories registered.
3. `IResource.add_libraries(config)` — FanStatic libraries registered.
4. `IResource.add_js_resources(config)` and `add_css_resources(config)`.
5. `IDatabase.update_orm(config)` — plugin ORM models registered.
6. `ISchema.update_schema(config)` — schema extensions applied.
7. `IRoutes.before_mapping(config)` — plugin routes prepended.
8. Core FormShare routes registered.
9. `IRoutes.after_mapping(config)` — plugin routes appended.
10. `IEnvironment.after_environment_load(config)` — final hooks.
11. Routes registered with FastAPI; async views routed through `make_async_endpoint`, sync views through `make_endpoint`.

If a plugin needs to react to startup completion (open a long-lived connection, register a background task), do it in `IEnvironment.after_environment_load`.

## Key source files

These files in the FormShare source are the definitive reference. When you need exact signatures, read them first:

| Path | What it contains |
| --- | --- |
| `formshare/plugins/interfaces.py` | All 40+ interface definitions with method signatures and docstrings. |
| `formshare/plugins/utilities.py` | Helper functions (`add_route`, `add_library`, …) and plugin-facing view wrappers. |
| `formshare/plugins/helpers.py` | Template helper functions exposed as `request.h.*` in Jinja2 templates. |
| `formshare/plugins/core.py` | Plugin loading mechanics (entry points, activation). |
| `formshare/views/classes.py` | Every base view class — read for the exact set of attributes available in your view. |
| `formshare/config/dispatcher.py` | Sync and async request dispatch logic. Read when you need to understand exactly what happens between an HTTP request and your `process_view()`. |
| `formshare/config/jinja_extensions.py` | `extend_me`, `partial_start`/`partial_end`, `jsresource`, `cssresource`. |
| `formshare/config/mainresources.py` | Every FanStatic resource FormShare already ships — grep here before adding a duplicate. |
| `formshare/app.py` | The FastAPI app factory and `_register_routes`. Read to understand the async-vs-sync routing decision. |
| `formshare/views/task_stream.py` | Production AsyncView SSE implementation. The canonical example for streaming endpoints. |

## Worked examples

The best way to learn the plugin system after reading the reference is to read a real plugin end-to-end. Examples that ship with FormShare:

* `tryouts/plugin_examples/` — simple plugins illustrating individual interfaces. Start here.
* The ElasticSearch map plugin (private), the OData plugin (private), and the Enketo plugin all exercise the full interface set: routes, templates with `extend_me`, static resources, schema extension, and Celery hooks.

When something does not work, the first place to look is the source file for the interface you are implementing — the docstring on the interface method usually documents the exact contract and the error mode.
