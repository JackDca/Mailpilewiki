Commands are the basic building block of the Mailpile back-end application.
Most commands are both exposed to the user on the CLI, and to the web
interface as HTML pages or as REST API endpoints.

Virtually all of the Mailpile commands are written using the internal plugin
API and can be found in `mailpile/commands.py` and `mailpile/plugins/`.
Each command is derived from the base `Command` class found in
`mailpile/commands.py`.

A new command is registered with the plugin interface using
`mailpile.plugins.register_commands(CommandPluginClassName)`.

Coding examples may be found in `mailpile/plugins/demos.py`.

## A basic Command:

FIXME: insert md5sumCommand from demos.py


## Command attributes

All command classes have a few standard attributes which describe their
function to the rest of the app (and the user), including preferences for how
they are to be invoked, security characteristics and what details should by
default go into the event log.

The base `Command` class defines reasonable defaults for most attributes.

### Required attributes:

* `SYNOPSIS = ('opt-char', 'cli-name', 'api-name', 'parameters')`
* `ORDER = ('category' string, order int)`

### Optional:

* `HTTP_CALLABLE = ('GET', 'POST', 'PUT', 'DELETE', ...)`
...




