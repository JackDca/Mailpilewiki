![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Mailpile is best tested on recent Debian-derived distributions,
including Ubuntu.  It also works just fine on other modern distros (such
as Fedora), but you will need to use different commands to install and
configure the dependencies.

To install Mailpile on Debian or Ubuntu, please follow these steps (last
tested 07.06.2014 on Ubuntu 14.04).


### 1. Clone the source repository:

Use one of the following two commands:

    # The alpha release branch (technical preview)
    git clone -b release/alpha https://github.com/pagekite/Mailpile.git

    # The main development branch
    git clone https://github.com/pagekite/Mailpile.git

(If those fail, try `sudo apt-get install git` first.)

### 2. Install the requirements:

    sudo apt-get install make
    cd Mailpile
    sudo make debian-dev  # Does an apt-get install for requirements

If you get the following error you might want to install _build-essential_ package (`sudo apt-get install build-essential`) and then _Ruby_ (`sudo apt-get install ruby`).
```
ERROR:  Error installing therubyracer:
	ERROR: Failed to build gem native extension.

        /usr/bin/ruby1.8 extconf.rb
extconf.rb:1:in `require': no such file to load -- mkmf (LoadError)
	from extconf.rb:1

```



### 3. Configure Mailpile!

You should now be able to run Mailpile on and access the built-in CLI:

    ./mp

### 4. Installing on a Personal Computer

Access the GUI interface in your web browser at `http://localhost:33411` you should be presented with an intuitive setup flow that guides you through configuring your Mailpile. The first step you should see when loading the above URL asks you configure your language.

### 5. Installing on a Web Server (network access)

You will need to configure your server's network settings to allow HTTP access to port `33411` which is where Mailpile's HTTP server is serving the application. There are a few ways to do this and we will be providing better documentation about this soon!
