![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Mailpile is developed and tested most heavily on Debian and Debian-based distributions, but should work fine on any modern distribution (Fedora, Mandriva, even Arch if you are so inclined). There may be [packages available for your system](https://www.mailpile.is/download/). If not, consider [[submitting packages]].

## Installing from source

### 1. Clone the source repository

You will need `git` installed on your system. When it is up, clone the repository:

    git clone https://github.com/pagekite/Mailpile.git

This will clone the main development branch of Mailpile. If you want to clone a specific branch, specify it like so:

    git clone -b branchname https://github.com/pagekite/Mailpile.git

A [full list of existing branches is available on Github](https://github.com/pagekite/Mailpile/branches). Generally you'll want the highest version number available.

### 2. Install the requirements

Mailpile requires a number of different packages in order to run. Specifically:

 * GnuPG _(preferably on the 1.x branch for now, as Mailpile doesn't currently act as a GPG Agent)_
 * OpenSSL
 * Python 2.7+

And everything that's listed in `requirements.txt` (which can be auto-installed if you have PIP, by running `pip install -r requirements.txt`.

If all is well, you should now be able to run Mailpile.

### 3. Running Mailpile

You can run Mailpile directly using the command:

    ./mp

This should drop you into the Mailpile shell in the terminal and open up a new tab or window on your default browser that you can use to configure Mailpile and start using it.

 * [[Configuring Mailpile]]
 * [[Using Mailpile's command line]]
 * [[Troubleshooting]]

If you want to configure Mailpile to launch automatically on startup, consider adding it to either your window system config (if you're running it on a personal computer for instance), or to your init scripts (if you're using Mailpile on a server).

## Configuring Mailpile

