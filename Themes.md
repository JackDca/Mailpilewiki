Mailpile supports themes. They live in `static/`, so the directory `static/default` contains the theme named `default`. Note that any files living in a theme folder will be accessible via the `/static/*` URL in the HTTP interface. It is not currently possible to access files via the HTTP interface from themes other than the current theme.

Themes contain:
* `js/` -- all JavaScript code
* `css/` -- all CSS stylesheets
* `html/` -- all Jinja2 HTML templates
* `img/` -- all images

Anything beyond that is optional and specific to the theme. The default theme also contains `webfonts/` and `less/`.