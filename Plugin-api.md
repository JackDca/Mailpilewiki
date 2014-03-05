There are demos of the plugin API in `mailpile/plugins/demos.py`

If you want to know which plugins are currently registered, do `print sys.plugins` from the textmode UI. To add a plugin, do `append sys.plugins <name>`.

## Command plugin hooks
You can add arbitrary commands to Mailpile. See [[Command plugins]]

* `mailpile.plugins.register_commands(*plugins)` - `plugins` is a list of one or more subclass of `Command`.

An example command plugin:
```python
class MyCommand(Command):
    """Do some very fun action"""
    ORDER = ('', 0)  # The command's order in the "help" output
    # The first value in SYNOPSIS would be a shortform version, and 
    # the last contains a description of the paramters the function takes.
    SYNOPSIS = (None, 'textui_commandname', 'webui_commandname', '')
    HTTP_CALLABLE = ('GET', )  # Can be "GET", "POST", "PUT",... or empty for no HTTP interaction
    HTTP_QUERY_VARS = {'q': 'search terms'}  # A documented list of query variables taken

    class CommandResult(Command.CommandResult):
        def as_text(self):
            # Overload the text printout. Also: as_json, as_html, ...
            return "...".join(self.result)

    def command(self):
        args = self.args[:] # The arguments of the function
        for q in self.data.get('q', []):  # self.data contains HTTP query args
            args.extend(q.split())

        # ... do something ...
        # Return some kind of JSON-serializable data structure...
        return {}
```

## Configuration plugin hooks
See [[Extending Maipile's Configuration]] and [[Configuring Mailpile]].

* `mailpile.plugins.register_config_variables(*args)`
* `mailpile.plugins.register_config_section(*args)`

## Search engine plugins hooks
See [[Search engine plugins]]

* `mailpile.plugins.register_search_term(term, handler)` - `handler` is a function that manages the search term.
* `mailpile.plugins.register_data_kw_extractor(term, function)`
* `mailpile.plugins.register_text_kw_extractor(term, function)`
* `mailpile.plugins.register_meta_kw_extractor(term, function)`

## Contact plugin hooks
The internal contacts manager stores everything internally as VCards. It supports the addition of importers and exporters to populate the VCard store, and context providers which provide the contact manager with ephemeral data relating to the contact that is expected to change over time. See [[Contact plugins]]

* `mailpile.plugins.register_contact_importer(importer)` - `importer` is subclass of `ContactImporter`.
* `mailpile.plugins.register_contact_exporter(exporter)` - `exporter` is subclass of `ContactExporter`.
* `mailpile.plugins.register_contact_context_provider(provider)` - `provider` is subclass of `ContactContextProvider`.

## Job plugin hooks

Needs documenting.