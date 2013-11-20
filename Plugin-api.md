There are demos of the plugin API in `mailpile/plugins/demos.py`

### Command plugin hooks
See [[Command plugins]]

* `mailpile.plugins.register_commands(*plugins)` - `plugins` is a list of one or more subclass of `Command`.

### Configuration plugin hooks
See [[Extending Maipile's Configuration]] and [[Configuring Mailpile]].

* `mailpile.plugins.register_config_variables(*args)`
* `mailpile.plugins.register_config_section(*args)`

### Search engine plugins hooks
See [[Search engine plugins]]

* `mailpile.plugins.register_search_term(term, handler)` - `handler` is a function that manages the search term.
* `mailpile.plugins.register_data_kw_extractor(term, function)`
* `mailpile.plugins.register_text_kw_extractor(term, function)`
* `mailpile.plugins.register_meta_kw_extractor(term, function)`

### Contact plugin hooks
See [[Contact plugins]]

* `mailpile.plugins.register_contact_importer(importer)` - `importer` is subclass of `ContactImporter`.
* `mailpile.plugins.register_contact_exporter(exporter)` - `exporter` is subclass of `ContactExporter`.
* `mailpile.plugins.register_contact_context_provider(provider)` - `provider` is subclass of `ContactContextProvider`.