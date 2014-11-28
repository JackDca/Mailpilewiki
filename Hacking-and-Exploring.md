![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

### Code structure ###

Mailpile's python code lives in `mailpile/`.

Mailpile's default HTML templates and Javascript lives in `static/default/`.

Miscellaneous documentation is in `doc/`.

Test data lives in `testing/`.


### Internal variables ###

There are a bunch of variables that can be tweaked. For a complete list:

    mailpile> help/variables
    ...

To set a variable to some value either run Mailpile with:

    $ ./mp --set section.variable=value

Or alternatively run `./mp` and issue:

    mailpile> set section.variable=value

after which you need to restart the program for it to take effect
(Ctrl+D and `./mp`). You can print the value of a variable using:

    mailpile> print variable


### Testing ###

We are slowly migrating the code to use the `doctest` module for
internal unit tests.

Black-box regression tests can be invoked by running
`scripts/mailpile-test.py`.  For experimenting and testing, the blackbox
test script can be run in an interactive mode:

    $ ./scripts/mailpile-test.py -i


### JSON, XML, RSS, ... ###

JSON and XML versions exist for most web-based commands and requests
and most Mailpile functionality is (or will be) accessible over an
HTTP REST-style API.

Please see `doc/URLS.md` for details.
