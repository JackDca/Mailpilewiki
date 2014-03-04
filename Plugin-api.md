There are demos of the plugin API in `mailpile/plugins/demos.py`

## Command plugin hooks
You can add arbitrary commands to Mailpile. See [[Command plugins]]

* `mailpile.plugins.register_commands(*plugins)` - `plugins` is a list of one or more subclass of `Command`.

An example command plugin:
```python
class GPGKeySearch(Command):
    """Search for a GPG Key."""
    ORDER = ('', 0)
    SYNOPSIS = (None, 'crypto/gpg/searchkey', 'crypto/gpg/searchkey', '<terms>')
    HTTP_CALLABLE = ('GET', )
    HTTP_QUERY_VARS = {'q': 'search terms'}

    class CommandResult(Command.CommandResult):
        def as_text(self):
            if self.result:
                return '\n'.join(["%s: %s <%s>" % (keyid, x["name"], x["email"]) for keyid, det in self.result.iteritems() for x in det["uids"]])
            else:
                return _("No results")

    def command(self):
        args = self.args[:]
        for q in self.data.get('q', []):
            args.extend(q.split())

        g = GnuPG()
        return g.search_key(" ".join(args))
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
