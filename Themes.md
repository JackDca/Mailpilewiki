![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

### Getting Started

* Make sure you have a instance of Mailpile installed and working properly
* Make sure you have completed steps in our [[Front End Development Guide]]
* Copy the `default-theme` located in `shared-data/` to `shared-data/your-theme/`

### Architecture

Mailpile supports themes. They live in `shared-data/`, so the directory `shared-data/default-theme` contains the theme named `default-theme`. Note that any files living in a theme folder will be accessible via the `/static/*` URL in the HTTP interface. It is not currently possible to access files via the HTTP interface from themes other than the current theme.

Mailpile's `default-theme` theme contains:

* `css/` all the CSS stylesheets get compiled to
* `html/` all Jinja2 HTML templates
* `img/` all images
* `js/` all the JavaScript code
* `less/` all of the [LESS](http://lesscss.org)
* `webfonts/` all of custom fonts & icon fonts
* `theme.json` a manifest file specifies strings, icons, and colors for JS to access

## Changing Themes

Currently, you can only change the them from the Mailpile CLI. To do so type 

```
mailpile> set sys.path.html_theme=/full/path/to/your-theme
```

That is the structure that Mailpile's theme ships with, but when making your own theme, just copy this directory and organize your assets however you like.

## Additional Themes

- [ArchivePile](https://github.com/TransparencyToolkit/ArchivePile/) - a read-only theme for browsing archives