Currently the way front-end plugin code will interact / be stored with backend python plugins is highly undetermined and subject to much change.

However, we have created a repo with various skeletons of plugins that allows for some plugin

### 1. Clone the Mailpile Plugins repo

`$ git clone https://github.com/mailpile/plugins`

### 2. Into the falling path in your instance of Mailpile

`/static/default/plugins`

3. Create config.json for your plugin

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

