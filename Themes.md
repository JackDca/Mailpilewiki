![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

Mailpile supports themes. They live in `mailpile/www/`, so the directory `mailpile/www/default` contains the theme named `default`. Note that any files living in a theme folder will be accessible via the `/static/*` URL in the HTTP interface. It is not currently possible to access files via the HTTP interface from themes other than the current theme.

Mailpile's `default` theme contains:

* `css/` all the CSS stylesheets get compiled to
* `html/` all Jinja2 HTML templates
* `img/` all images
* `js/` all the JavaScript code
* `less/` all of the [LESS](http://lesscss.org)
* `webfonts/` all of custom fonts & icon fonts
* `theme.json` a manifest file specifies strings, icons, and colors for JS to access


That is the structure that Mailpile's theme ships with, but when making your own theme, just copy this directory and organize your assets however you like.

### Front-end Dependencies & Bower

The decision was made to migrate front-end dependencies (JS, CSS, and LESS) to use the [Bower](http://bower.io) package manager. It is a tool that is quite popular with front-end developers. My reasoning is:

* Makes updating of front-end deps' much easier
* Offers semantic versioning (semver) of front-end deps
* Will ultimately keep the Mailpile git repo smaller in size

Currently the plan is to keep comiting the ```default.css``` and various JS files so the experience for an alpha tester or backend python dev should remain identical to current flow. However, front-end developers will need to install Bower and the Mailpile deps if they want to work on existing front-end (mostly LESS / CSS) code.

#### Using Bower with Mailpile

To start doing front-end development on Mailpile you need to first install the existing dependencies, once you have Bower itself installed, do the following to:

* Navigate to theme folder ```$ cd Mailpile/static/default/```
* Install Bower dependencies ```$ bower install```

This will have created the following folder ```Mailpile/static/default/bower_components``` which contains all the source front-end dependencies.
 
* To add a new dependency type ```$ bower install new-package-name```
* To search packages type ```$ bower search name-of-package```