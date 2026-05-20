---
description: How FormShare's plugin system works, when you should write a plugin, and how to scaffold a new one with the cookiecutter template.
---

# Plugin development

FormShare ships with a plugin system that lets you extend or modify almost every aspect of the platform without touching the core code. Most of FormShare's optional features — including the Enketo bridge, the ElasticSearch map view, the OData export, and several import / export formats — are shipped as separate plugins on top of the same core, and your own additions live alongside them as installable Python packages.

This page introduces the plugin system, explains when you should write a plugin, and walks through scaffolding a new plugin from the official cookiecutter template. The companion page "[Writing a plugin](writing-a-plugin.md)" goes into the structure of `plugin.py`, the interface catalog, the view classes, templating, htmx integration, async streaming, and schema extension.

{% hint style="info" %}
**This section is for developers.** The rest of the FormShare documentation is for the people running data-collection projects. This section is for the people building features on top of FormShare. If you've never touched Python before, the rest of the documentation is more relevant to you.
{% endhint %}

## When to write a plugin

A plugin is the right answer when:

* You need to add a new page or API endpoint that doesn't exist in core.
* You need to react to a lifecycle event — a new project, a new form, a submission landing in the database, a user signing up — and run custom code at that moment.
* You need to import data from a source FormShare doesn't natively understand.
* You need to add a column to an existing table without modifying core models.
* You need to override an existing template (the form details page, the project list, etc.) to add a panel or change behavior.
* You need to expose a new JS / CSS asset alongside FormShare's built-in ones.
* You need a streaming endpoint (Server-Sent Events, long-polling) that pushes events to the browser.

A plugin is the wrong answer when:

* The change you want to make is genuinely part of core behavior. Open an issue on GitHub and discuss instead.
* You only need a one-off script — a Celery task triggered manually, or a `psql` query. Use FormShare's existing [API](../../data-management/cleaning/in-a-repository/api.md) or write a standalone script.
* You need to fork FormShare for a contractual reason (a private build for one customer). A fork is heavier than a plugin, but sometimes more honest.

## How the plugin system works

FormShare uses the [PyUtilib](https://pypi.org/project/PyUtilib/) component system (`pyutilib.component.core`) as its plugin engine. The mechanics are simple:

1. A plugin is a normal Python package, installable with `pip`. It declares itself via the `formshare.plugins` entry point in its `setup.py`.
2. At startup, FormShare reads the list of active plugins from its `.ini` file (`formshare.plugins = name1 name2 …`), looks them up in the entry points, and loads each plugin's main class.
3. Each plugin class declares which **interfaces** it implements (`IRoutes`, `IConfig`, `IResource`, `ISchema`, etc.) using `plugins.implements()`.
4. As FormShare boots, it calls the corresponding interface methods on every plugin that implements them. Each interface defines hooks at specific lifecycle points — route registration, template directory discovery, schema extension, submission processing, and so on.
5. Plugins can override or extend core templates by placing a file at the same relative path inside their own `templates/` directory and using the `{% extend_me %}` Jinja tag.

There are over 40 interfaces, covering every meaningful extension point. The complete list lives in `formshare/plugins/interfaces.py` in the FormShare source. The [Writing a plugin](writing-a-plugin.md) page summarises them with one-line descriptions and source pointers.

## Scaffolding a new plugin

The official cookiecutter template is the fastest way to start a new plugin. It generates the directory structure, the `setup.py` with entry points, a `plugin.py` skeleton, the Alembic configuration for database migrations, and the standard support files.

### Prerequisites

* Python 3 with `pip`
* [Cookiecutter](https://cookiecutter.readthedocs.io/) installed: `pip install cookiecutter`
* A working FormShare development environment (the plugin needs FormShare to be installed in the same virtual environment)

### Generate the project

Run cookiecutter against the template repository:

```bash
cookiecutter https://github.com/qlands/formshare-cookiecutter-plugin
```

Cookiecutter asks a few questions interactively (plugin name, author, version, etc.) and creates the project directory.

### Install the plugin in development mode

From inside the generated directory:

```bash
python setup.py develop
```

This registers the plugin with the active virtual environment without copying files — every change you make to the plugin's source code takes effect on the next FormShare restart.

### Configure database migrations

The scaffold ships with an example Alembic configuration for plugins that need to add tables of their own (using `IDatabase`):

```bash
mv alembic.example.ini alembic.ini
# Edit alembic.ini — set `sqlalchemy.url` to match your FormShare database
alembic revision --autogenerate -m "Initial version"
alembic upgrade head
```

If the plugin doesn't add ORM models, skip the Alembic steps.

### Enable the plugin in FormShare

Edit FormShare's `development.ini` (or `production.ini`) and add the plugin's entry-point name to the `formshare.plugins` list:

```ini
[app:formshare]
formshare.plugins = your_plugin_name
```

The name must match the left-hand side of the entry point you declared in the plugin's `setup.py` — for example `your_plugin_name = your_plugin.plugin:YourPlugin`.

### Restart FormShare

Restart the FormShare web app (and the Celery workers if your plugin defines Celery tasks). The plugin is now active.

## A minimal "hello FormShare" plugin

The smallest useful plugin registers a single new page and a static asset. Here's what `plugin.py` would look like:

```python
import formshare.plugins as plugins
import formshare.plugins.utilities as u
from .views import HelloView


class HelloPlugin(plugins.SingletonPlugin):
    plugins.implements(plugins.IRoutes)
    plugins.implements(plugins.IConfig)

    def after_mapping(self, config):
        return [
            u.add_route(
                "hello_formshare",
                "/user/{userid}/hello",
                HelloView,
                "dashboard/hello.jinja2",
            ),
        ]

    def update_config(self, config):
        u.add_templates_directory(config, "templates")
```

And the view in `views.py`:

```python
from formshare.plugins.utilities import FormSharePrivateView


class HelloView(FormSharePrivateView):
    def process_view(self):
        return {"message": f"Hello, {self.user.id}"}
```

After installing, enabling the plugin in the `.ini`, and restarting, the URL `/user/<your-userid>/hello` renders the template `dashboard/hello.jinja2` from the plugin with `message` in scope.

## Where to go next

* "[Writing a plugin](writing-a-plugin.md)" — full reference: directory structure, plugin class anatomy, every interface, view classes (sync and async), templates and the `extend_me` mechanism, static resources, htmx integration, SSE streaming, schema extension, and settings persistence.
* The FormShare source under `formshare/plugins/` contains the authoritative interface definitions (`interfaces.py`), helper utilities (`utilities.py`), and template helpers (`helpers.py`).
* `formshare/views/classes.py` holds every base view class — read it when you need the exact set of attributes available to your view.
* Plugins that ship with FormShare (in private plugin directories or under `tryouts/plugin_examples/`) are the best worked examples — they exercise every interface in real code.
