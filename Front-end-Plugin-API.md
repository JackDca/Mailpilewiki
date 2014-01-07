Currently the way front-end plugin code will interact / be stored with backend python plugins is highly undetermined and subject to much change. The following represents the current state of things, but please be aware that the footing might be pulled from under you at any moment.

## UI hooks
These hooks are used in Python modules to register various interactions. They are then filled out in detail in the HTML and Javascript sides for the plugins.

* `register_uiclass(uiclass)`
* `register_display_mode(uiclass, name, jsaction, url="#")`
* `register_display_action(uiclass, name, jsaction, url="#")`
* `register_selection_action(uiclass, name, jsaction, url="#")`
* `register_activity(name, jsaction, url="#")`
* `register_asset(assetclass, path)`
* `register_body_block(uiclass, path)`

## Jinja2 functions:
* `get_display_modes(uiclass)` - returns `[{"name": "", "url": "#", "jsaction": "", "text": None, "icon": None},...]`
* `get_display_actions(uiclass)` - returns `[{"name": "", "url": "#", "jsaction": "", "text": None, "icon": None},...]`
* `get_selection_actions(uiclass)` - returns `[{"name": "", "url": "#", "jsaction": "", "text": None, "icon": None},...]`
* `get_activities()` - returns `[{"name": "", "url": "#", "text": "", "icon": None},...]`
* `get_assets(assetclass)` - returns `["filename", ...]`
* `get_body_blocks(uiclass)` - returns `["filename", ...]`

## Modes, actions, etc:

* [uiclass:search]
* * display_modes		->	list, graph
* * display_actions		->	all, new, select[all,none]
* * selection_actions	->	later, group, tag, delete, read, archive, spam, advanced[export]

* [uiclass:thread]
* * display_modes		->	
* * display_actions		->	archive, delete, show all
* * selection_actions	->

* [uiclass:contact]
* * display_modes		->	cards, list
* * display_actions		->	view, add, import
* * selection_actions	->	group, mailto

* [uiclass:tags]
* * display_modes		->	cards, list
* * display_actions		->	view, add
* * selection_actions	->	

* [uiclass:main]
* * activities			->	compose, contacts, tags, donate, settings


## Plugins repository
We have created a repo with various skeletons of plugins.

1. Clone the Mailpile Plugins repo

`$ git clone https://github.com/mailpile/plugins`

2. Into the falling path in your instance of Mailpile

`/static/default/plugins`

3. Create config.json manifest file for your plugin

This is not currently manditory, but in due time will replace the manual use of registration hooks documented above to a substantial degree. Please generate the manifest file even though it isn't currently used, as it will make everything else a lot easier in the future.

```
{
  "plugin": "force-grapher",
  "name": "Use The Force, Grapher",
  "created": "2013-10-31",
  "updated": "2013-10-31",
  "creators": ["Smári McCarthy <smari@mailpile.is>", "Brennan Novak <hi@bnvk.me>"],
  "categories": [
    "interfaces",
    "data visualization",
    "graphs",
    "force directed",
    "inbox"
  ],
  "navigation": ["sub-navigation"],
  "views": ["search"],
  "urls":{
    "default": "/force-grapher"
  },
  "components": {
    "python": "false",
    "css": "true",
    "javscript": "true",
    "html": "true"
  }
}
```
