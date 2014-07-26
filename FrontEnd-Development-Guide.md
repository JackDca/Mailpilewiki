**Backbone Dependencies**

```
underscore.js
backbone.js
backbone-validation.js
```

**Bootstrap**

```
dropdown.js
modal.js
```

**jQuery UI**

```
jquery.ui.core.js
jquery.ui.widget.js
jquery.ui.mouse.js
jquery.ui.draggable.js
jquery.ui.droppable.js
jquery.ui.sortable.js
```

**Misc JS Dependencies**

```
underscore.js
backbone.js
backbone-validation.js
jquery.autosize.js
slugify.js
jquery.timer.js
mousetrap.js
mousetrap.global.bind.js
purl.js
jquery.qtip.js
select2.js
favico.js
helpers.js
```

All of these JS files are compiled into `libraries-min.js` manually at the moment. Some of them are downloaded via Bower, others exist in the Mailpile repo in the `static/default/js/libraries` folder.

We'd like to make this process more elegant and easy for a front-end dev to jump and work with, but will probably not do so post 1.0, hopefully post Beta.