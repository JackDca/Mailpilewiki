![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Mailpile is developed and tested most heavily on Debian and Debian-based distributions, but should work fine on any modern distribution (Fedora, Mandriva, even Arch if you are so inclined). There may be [packages available for your system](https://www.mailpile.is/download/). If not, consider [[submitting packages]].

## 1. Clone the source repository

You will need `git` installed on your system. When it is up, clone the repository:

    git clone https://github.com/mailpile/Mailpile.git

This will clone the main development branch of Mailpile. If you want to clone a specific branch, specify it like so:

    git clone -b branchname https://github.com/mailpile/Mailpile.git

A [full list of existing branches is available on Github](https://github.com/mailpile/Mailpile/branches). Generally you'll want the highest version number available.

## 2. Install all the dependencies

Mailpile requires a number of different packages in order to run. Specifically:

 * GnuPG _(preferably on the 1.x branch for now, as Mailpile doesn't currently act as a GPG Agent)_
 * OpenSSL
 * Python 2.7+

And everything that's listed in `requirements.txt` (which can be auto-installed if you have PIP, by running `sudo pip install -r requirements.txt`). If you don't want to use PIP, you need to install these dependencies:

 * python-dev
 * libxml2-dev
 * libxslt-dev
 * libz-dev
 * python-jinja2
 * python-lxml
 * python-pgpdump

Under Fedora this yum command might work for installing the dependencies:
sudo yum install gnupg openssl python-devel libxml2-devel libxslt-devel zlibrary-devel python-jinja2 python3-jinja2 python-lxml python3-lxml python-pgpdump python3-pgpdump

If all is well, you should now be able to run Mailpile.

## 3. Start Running Mailpile

You can run Mailpile directly using the command:

    ./mp

This should drop you into the Mailpile shell in the terminal and open up a new tab or window on your default browser that you can use to configure Mailpile and start using it.

 * [[Configuring Mailpile]]
 * [[Using Mailpile's command line]]
 * [[Troubleshooting]]

If you want to configure Mailpile to launch automatically on startup, consider adding it to either your window system config (if you're running it on a personal computer for instance), or to your init scripts (if you're using Mailpile on a server).

