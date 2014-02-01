**Note:** Mailpile is currently only released in source form, from our Github repository.

## Quickstart

The quickest way to get up and running, is to follow one of the following guides:

* [[Getting started on Linux]]
* [[Getting started on MacOS]]
* [[Getting started on Windows]]

When that is done, the following guides will help you configure the software and get started using it:

* [[Configuring Mailpile]]
  * [[Setting up Basic Config]]
* [[Indexing Mail]]
  * [[Synchronizing Mailpile with Thunderbird]]
* [[Using Mailpile]]
  * [[Using the Web Interface]]
* [[Protecting Your Privacy]]
* [[Hacking and Exploring]]
* [[Mailpile's Performance]]

If you are using some other operating system or are a developer who prefers Docker, Virtualenv or PIP to the above recipes, please read on...

======================================================================================================

## Details for Developers

If you are using some other operating system you are on your own, but the following general guidelines may be of use.

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

    $ docker build -t mailpile scripts/docker/

and run it:

    $ docker run -i -t -p 33411:33411 mailpile

or enter the container's bash prompt directly:

    $ docker run -i -t mailpile bash
