Mailpile supports themes. They live in `static/`, so the directory `static/default` contains the theme named `default`. Note that any files living in a theme folder will be accessible via the `/static/*` URL in the HTTP interface. It is not currently possible to access files via the HTTP interface from themes other than the current theme.

Themes contain:
* `js/` -- all JavaScript code
* `css/` -- all CSS stylesheets
* `html/` -- all Jinja2 HTML templates
* `img/` -- all images

Anything beyond that is optional and specific to the theme. The default theme also contains `webfonts/` and `less/`.

### Front-end Dependencies & Bower

The decision was made to migrate front-end dependencies (JS, CSS, and LESS) to use the [Bower](http://bower.io) package manager. It is a tool that is quite popular with front-end developers. My reasoning is:

* Makes updating of front-end deps' much easier
* Offers semantic versioning (semver) of front-end deps
* Will ultimately keep the Mailpile git repo smaller in size

My plan is to keep comiting the ```default.css``` and various JS files so the experience for an alpha tester or backend python dev should remain identical to current flow. However, front-end developers will need to install Bower and the Mailpile deps if they want to work on existing front-end (mostly LESS / CSS) code.