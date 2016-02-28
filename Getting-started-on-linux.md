![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Mailpile is developed and tested most heavily on Debian and Debian-based distributions, but should work fine on any modern distribution (Fedora, Mandriva, even Arch if you are so inclined). There may be [packages available for your system](https://www.mailpile.is/download/). If not, consider [[submitting packages]].

## 1. Install your system dependencies

Mailpile requires a number of different packages in order to run. Specifically:

 * GnuPG _(preferably on the 1.x branch for now, as Mailpile doesn't currently act as a GPG Agent)_
 * OpenSSL
 * Python 2.7+
 * Lxml

Python 2.7 or newer is standard on virtually all Linux systems now. On Debian, you could install the other packages by running

    sudo apt-get install gnupg openssl python-virtualenv python-pip python-lxml libjpeg-dev

This will also install the Python virtual environment and package installer tools, which are optional but recommended.

To clone the source repo make sure to have `git` installed too
    
    sudo apt-get install git

The following steps assume that you already have [virtualenv](http://virtualenv.readthedocs.org) installed. If not please follow the [installation instructions](http://virtualenv.readthedocs.org/en/latest.installation.html) for that first. If you would prefer not to use virtualenv at all and install Mailpile globally (either with or without [pip](http://pip.readthedocs.org)), please see the instructions at the end of the page.

## 2. Clone the source repository

    # clone Mailpile, docs and plugins (submodules) to your machine
    git clone --recursive https://github.com/mailpile/Mailpile.git

This will clone the main development branch of Mailpile. If you want to clone a specific branch, specify it like so:

    git clone --recursive -b branchname https://github.com/mailpile/Mailpile.git

A [full list of existing branches is available on Github](https://github.com/mailpile/Mailpile/branches). Generally you'll want the highest version number available.

## 3. Setup your virtual environment

    # move into the newly created source repo
    cd Mailpile

    # create a virtual environment directory
    virtualenv -p /usr/bin/python2.7 --system-site-packages mp-virtualenv

    # activate the virtual Python environment
    source mp-virtualenv/bin/activate

`source` is a built into bash, so you have to use either bash or a bash compatible shell.

#### What is virtualenv?

Virtualenv is a tool which allows you to install the Mailpile's Python dependencies locally, without having to modify your operating system's global Python. This keeps things contained and makes it easier to un-install everything all at once. The downside, is you need to *activate* the virtual environment before you continue setting up Mailpile and always before you run Mailpile. That is what the `source` command does.

## 4. Install the dependencies

**Important:** You must have activated the virtual Python environment in your current shell, as described in step 1 above. If you opened a new shell or a new terminal window, see section 5.1.

If you want to develop Mailpile:

    pip install -r requirements-dev.txt

For production or end-users, install everything that's listed in [requirements.txt](https://github.com/mailpile/Mailpile/blob/master/requirements.txt) instead.

    pip install -r requirements.txt

If all is well, you should now be able to run Mailpile.

**Note:** If you have difficulty installing the LXML package, you can try installing a vendor supplied package instead, e.g. by running `sudo apt-get install python-lxml` or equivalent.

## 5. Start Running Mailpile

You can now run Mailpile directly using the command:

    ./mp

This should drop you into the Mailpile shell in the terminal and open up a new tab or window on your default browser that you can use to configure Mailpile and start using it.

 * [[Configuring Mailpile]]
 * [[Using Mailpile's command line]]
 * [[Troubleshooting]]

If you want to configure Mailpile to launch automatically on startup, consider adding it to either your window system config (if you're running it on a personal computer for instance), or to your init scripts (if you're using Mailpile on a server).

*If you want to configure Mailpile to accessed over the internet [follow these instructions]
(https://github.com/mailpile/Mailpile/wiki/Accessing-The-GUI-Over-Internet)*
### 5.1. Run Mailpile again!

Steps 1-5 should get you up and running. However, you don't need to go through the entire process every time you want to run Mailpile. The steps for doing that are as follows:

    # enter the Mailpile source directory
    cd Mailpile

    # activate the Mailpile virtual Python environment
    source mp-virtualenv/bin/activate

    # run Mailpile
    ./mp


Other useful commands (from within the Mailpile directory):

    # update your Mailpile
    git pull

    # update any submodules (documentation, plug-ins)
    git submodule update

    # if you are developing and want a throw-away, blank Mailpile
    ./scripts/setup-test.sh

    # running all the tests
    make alltests

    # leave the virtual Python environment
    deactivate


## 6. Installing Mailpile globally

### 6.1. With pip

If you want to use PIP (Python's package manager), you need to install these dependencies first, to enable PIP to run. For Debian...

    sudo apt-get install python-dev libxml2-dev libxslt-dev libz-dev python-pip

Then follow the details in [step 3](#3-clone-the-source-repository) & [step 4](#4-install-the-dependencies)

### 6.2. Without pip

If you don't want to use PIP, you need to install these dependencies:

 * python-dev
 * libxml2-dev
 * libxslt-dev
 * libz-dev
 * python-jinja2
 * python-lxml
 * python-pgpdump
 * spambayes

**Debian**

You could install these packages like this:

```
sudo apt-get install python-dev libxml2-dev libxslt1-dev zlib1g-dev python-jinja2 python-lxml python-pgpdump spambayes
```

**Fedora**

Using this dnf command might work for installing the dependencies:

```
sudo dnf install gnupg openssl python-devel libxml2-devel libxslt-devel zlib-devel python-jinja2 python-lxml python-pgpdump
```

Then follow the details in [step 3](#3-clone-the-source-repository)