**THIS IS A WORK IN PROGRESS, PLEASE ADD COMMENTS IF ANYTHING IS UNCLEAR - ... or clarify, of course**

The new Mailpile plugin architecture allows you to create and install plugin packages that live outside the Mailpile source tree. This allows you to extend the functionality of Mailpile in a lot of ways. This documentation is intended to help you get started with the creation of Mailpile plugins, but we also suggest looking at the demo plugins shipped with Mailpile in `plugins/demos/`.

## Getting started

When you decide to make a plugin, you will normally have some functionality in mind. It could be expanding on any number of Mailpile's features or providing entirely new features. For instance, a plugin might add a new way to visualize your inbox, or support importing contacts from a new type of file format or online service. It could also add support for a calendar, instant messaging, or something else entirely new to Mailpile. The sky is not the limit!

The first thing to do is to make a directory for your plugin `plugins/your-awesome-thing` or copy our `demos` folder and rename it. Then start poking around. Most things should become clear, for everything else, read on...

## Plugin layout

A plugin lives in a folder and contains at minimum a `manifest.json` file containing a valid JSON manifest. If this file does not exist, the folder is not considered to be a plugin and Mailpile will ignore it.

Beyond that, you can have any collection of Python, Javascript, HTML, CSS, and other files that you want to use in the plugin within the plugin folder. Whether you organize it in any way and how you chose to do so is entirely up to you, but there are a few little tricks. The most important trick is that if you have Python or Javascript files which have the same name as the plugin, then these will be loaded as the root object rather than a sub-object. For instance, if your plugin is named `foo`, then `foo.py` will be available as `mailpile.plugins.foo` in the Python hierarchy, while `bar.py` will be available as `mailpile.plugins.foo.bar`. This is similar to how `__init__.py` would normally be expected to behave. The same applies for Javascript, where `foo.js` will be available as `mailpile.plugins.foo` while `bar.js` will be loaded as `mailpile.plugins.foo.bar`. (Yes, the same hierarchy is used on both Python and Javascript sides!)

## Manifest files

The manifest files describe what your plugin does, and how. Some of the contents of the manifest are meant for human consumption, but most of the information is telling Mailpile how your code provides added functionality and how to present that functionality to the user.

A manifest file is a valid strict JSON file, with one exception: a line where the first non-whitespace character is a hash symbol (#) is considered a comment. (Note: This means you can't put a comment after JSON content on a line!)

So, as a barebones example, your manifest file could be:
    
    {
        "name": "My plugin",
        "author": "J. Random Hacker <jrh@example.com>",
        "code": {
            "javascript": [],
            "python": [],
            "css": []
        }
    }

A fully documented manifest file is in `plugins/demos/manifest.json`. The following will only be directive descriptions.

### name

A string that contains the plugin name. This must match the name of the folder containing the plugin.

### author

A string that contains the plugin author, formatted like an e-mail `From:` header: `Your Name <email@domain.com>`

### code

A dictionary containing three lists, named `python`, `javascript` and `css` respectively. Contains a list of files that the plugin should load of each type. These resources are all managed by Mailpile directly and loaded in the right places to make them available to the application. As such, each must obey a few simple conventions.

Python files will be loaded with the plugin's name under the `mailpile.plugins` namespace. If a Python file has the same name as the plugin, e.g. `xyzzy.py` in the `xyzzy` plugin, it will be loaded as the root of the plugin. More details below.

Javascript files will be loaded into the HTML client as objects under the `mailpile.plugins` namespace, within a closure function. If a Javascript file has the same name as the plugin, e.g. `xyzzy.js` in the `xyzzy` plugin, it will be loaded as the root of the plugin. Any methods you want exposed to the rest of the app must be returned as an object. More details below.

CSS files will be loaded into the HTML client.

### routes

Routes bind URLs with files from your plugin. Generally this mechanism is used to assign view templates to API commands, with the exception of the `/static/` namespace which can be used to serve up raw data.

The routes section is a dictionary that maps URL keys to dictionaries. The dictionaries contain:
    
 * **name**: the filename in your plugin (required)
 * **api**: the API version this routing is considered to occur at (see API versions below) (recommended)
 * **mimetype**: the MIME type of the file (optional)

### config

The config section is used to declare configuration areas in the configuration file. These entries follow the same structure as is layed out in [[Config]].

For example, you can add a variable named `md5sum_blacklist` that takes a string to the `sys` section of the configuration structure and defaults to `gross` like so:

    config: {
        "sections": {},
        "variables": {
            "sys": {
                "md5sum_blacklist": ["Words hated by the md5sum command",
                                     "str", "gross"]
            }
        }
    }
    
The first parameter is documentation for that variable, for use when Mailpile auto-generates settings dialogs.

### commands

The commands section lists any [[Commands]] that the plugin provides. These then get registered into the command registry and are immediately available through all [[user interaction]] modes (HTML, JSON, CLI, etc).

A command entry must name its class, URL and name, e.g.:
    
    {
        "class": "demos.md5sumCommand",
        "url": "md5sum",
        "name": "md5sum"
    }

This will make the command available on the command line as `md5sum` (based on the `name`) and on the URL `/api/N/md5sum/` (based on the `url` field, where N is the API version). Both of these will execute the command defined by the `md5sumCommand` class (which is a subclass of `Command`). See below.

### contacts

The contacts structure defines three lists, each referring to Python classes:

 * **importers**: Contact importers, e.g. an importer that loads contacts from a Mork or WAB database.
 * **exporters**: Contact exporters, e.g. an exporter that saves contacts to a Mork database or a CardDAV database.
 * **context**: Contact context providers, e.g. one that shows the newest tweets from a particular contact.


### periodic_jobs

Allows you to register jobs that should happen at fixed intervals. There are two classes of jobs: fast jobs, which are expected to run and terminate quickly, and slow jobs, which may go on for some time before quitting. This distinction is made for optimization purposes.

Fast and slow jobs are provided as lists. Each item must declare an interval (in seconds) and a class to execute.

    "periodic_jobs": {
        "fast": [{"interval": 5, "class": "TickJob"}],
        "slow": [{"interval": 15, "class": "TickJob"}]
    },

### keyword_extractors

DETAILS MISSING.

### search_terms

DETAILS MISSING.

### filters

Allows you to register actions that are taken before and after [[deterministic filters]] and [[Bayesian filters]] are applied, in the `pre` and `post` lists.

    "filters": {
        "pre": [],
        "post": []
    },

DETAILS MISSING.

### threads

DETAILS MISSING.

### user_interface

Here we start defining how things will be presented in the user interface. There are currently four types of user interface elements:

 * **Display modes**: Different ways of rendering data. For instance, searching defaults to a list view, but you could create a display mode that displays a timeline or something.
 * **Display refiners**: Ways to refine the data being shown. This could for add, remove or alter terms for a query. For instance, a refiner that shows only unread messages.
 * **Selection actions**: Actions that can be taken on a set of items which have been selected. For example, delete selected contacts.
 * **Activities**: Stand-alone activities. This can be anything that should be represented as a button in a given context and doesn't fit into the above categories.

The user_interface structure is therefore at its most basic:
    
    "user_interface": {
        "activities": [],
        "display_modes": [],
        "display_refiners": [],
        "selection_actions": []
    }

Every item of every type here exists in a set of given contexts. These contexts determine when and how the UI elements are displayed. Without contexts, weird things would happen. It would be silly for a selection action which knows how to create a group from a set of contacts to show up when your selection is a set of messages.

Contexts are given in terms of the API endpoints. So, a user interface element that knows how to work with search results from `/api/0/search/` would list `/api/0/search/` as one of its contexts. They also have names, descriptions, and a number of other attributes.

| Attribute            | Activities | Display modes | Display refiners | Selection actions | Required? | Values          | Default value         | Description                                                               |
|----------------------|------------|---------------|------------------|-------------------|-----------|-----------------|-----------------------|---------------------------------------------------------------------------|
| context              | Yes        | Yes           | Yes              | Yes               | Yes       | List of strings | []                    | List of contexts that the element is shown in                             |
| name                 | Yes        | Yes           | Yes              | Yes               | Unique    | HTML ID string  | ""                    | Name of element                                                           |
| description          | Yes        | Yes           | Yes              | Yes               | No        | String          | ""                    | Descriptive text for this UI element                                      |
| text                 | Yes        | Yes           | Yes              | Yes               | No        | String          | ""                    | Button text                                                               |
| icon                 | Yes        | Yes           | Yes              | Yes               | No        | Filename or ID  | ""                    | Image path or ID for CSS class                                            |
| javascript_setup     | Yes        | Yes           | Yes              | Yes               | No        | Identifier      | ""                    | Javascript function to call on initialization                             |
| url                  | Yes        | Yes           | Yes              | Yes               | No        | URL             | ""                    | URL to go to on click                                                     |


## API versions

Each version of Mailpile where there are API changes will have its own API version number. When you develop plugins which reference the API based on a particular version, any subsequent changes in the API should be resolved automatically to the greatest degree possible. For instance, if we had an API call in API verion 7 called `bork`, it would be accessible under `/api/7/bork/`. If you refer to that in an action context or a route, and in version 8 it changes to `huzzah`, then in the version 8 API we will include a transation stating that `/api/7/bork/` is equivalent to `/api/8/huzzah/`. That way, hopefully, even if big things change, most of the time your plugins should continue to work.

It is possible but not certain that this will also provide backwards-compatibility for plugins, where a plugin developed with `/api/8/huzzah/` will be able to use the `/api/7/bork/` call transparently.

Further details on this are available in the [[API versioning]] documentation.

## Writing Python plugins

Most plugins will have some Python component that implements a commmand, contact importer or exporter, a periodic job, keyword extractor, some search terms, filters, or worker threads.

(Some of this can come from the [[Plugin API]] page)

### Commands

A command is a class that subclasses the `Command` class, or any subclass thereof.

It must implement a function called `command`. This function must return a Python data structure, such as a string, int, bool, list, or dict.

When you add a command, it will instantly become available as a command in the CLI, an API endpoint on the HTTP server, and if you supply templates, as HTML or anything else. Where it will become available is determined by the routing you did.

### ...


## Writing Javascript plugins

...

