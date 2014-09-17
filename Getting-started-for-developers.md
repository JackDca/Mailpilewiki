![Technical Documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

This is a rough guide for getting Mailpile running on odd operating systems or system configurations, intended for a technical audience. Generally speaking, if you are using some other operating system than those explicitly supported, you are on your own, but the following general guidelines may be of use.

Mailpile doesn't employ any magic. Everything it does is mostly compliant with the laws of physics and the relevant RFCs. In particular, no violations of the second law of thermodynamics have been detected during Mailpile's operation. Developers are nonetheless urged to exercise caution.

### Requirements

- [Python](http://python.org) 2.7
- [python-imaging](http://www.pythonware.com/products/pil/) 1.1.7
- [python-lxml](http://lxml.de/) 2.3.2
- [python-jinja2](http://jinja.pocoo.org/) 2.6
- [spambayes](http://www.spambayes.org/) 1.1a6

You will also need a tool which can download your e-mail and store in one of the following formats:

- Unix mbox
- Unix Maildir
- Windows Maildir
- Mac Mail.app format
- GMVault

### Using PIP Install

If you're the kind of Python dev who prefers to stay in the Python universe, the PIP tool can be used to install the required packages:

    $ pip install -r requirements.txt

Note that this requires having a functioning C development environment and sometimes `lxml` can be a bit tricky to install. For Debian-based distributions, the required development libraries can be installed like so:

    $ sudo apt-get install libxml2-dev libxslt1-dev

as per [this Stack Overflow
answer](http://stackoverflow.com/questions/15759150/src-lxml-etree-defs-h931-fatal-error-libxml-xmlversion-h-no-such-file-or-di).

### Developing using virtualenv ###

The `Makefile` includes a recipe for setting up a virtualenv for use with Mailpile:

    $ make virtualenv
    $ source mp-virtualenv/bin/activate
    $ mailpile

This allows easy, sandboxed usage.

### Developing using docker ###

You can build a docker image:

    $ docker build -t mailpile .

and run it:

    $ docker run -i -t -p 33411:33411 mailpile

or enter the container's bash prompt directly:

    $ docker run -i -t mailpile bash

### Installing on Raspberry Pi ###

Users have reported running into a problem when running **make** when building the Alpha on Raspberry Pi. Once you get to installing therubyracer it might fail due to a problem with libv8. Run the following:

```
$ gem install libv8 -- --with-system-v8
``` 