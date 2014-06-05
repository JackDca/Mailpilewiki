Mailpile is best tested on recent Debian-derived distributions,
including Ubuntu.  It also works just fine on other modern distros (such
as Fedora), but you will need to use different commands to install and
configure the dependencies.

To install Mailpile on Debian or Ubuntu, please follow these steps (last
tested 27.01.2014 on Ubuntu 12.04).


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

Running the following commands in the CLI will configure Mailpile's
basic features:

    mailpile> setup
    ...
    mailpile> set profiles.0.email = yourmail@domain.com
    ...
    mailpile> set profiles.0.name = Your Real Name
    ...

You can set up alternate profiles by using sequentially higher numbers
instead of 0 - 1, 2, 3, etc.

By default Mailpile will try to send mail using the operating system's
local mailserver (`/usr/sbin/sendmail`), but you can also configure an
explicit route for outgoing mail:

    # Basic: Send mail in the clear over port 25
    mailpile> set profiles.0.route = smtp://user:password@smtphost.com:25
    ...

    # Better: Send mail using TLS over the submission port, 587
    mailpile> set profiles.0.route = smtptls://user:password@smtphost.com:587
    ...

When you are done configuring Mailpile, you can exit the CLI by pressing
CTRL+D - or you can check out the web interface on `http://localhost:33411`.


### 4. Import your e-mail

Mailpile does not yet (as of the January alpha release) know how to
download your e-mail for you.  Instead it behaves like a traditional
Unix mail client and relies on the operating system (or other tools) to
do so.

If you have a running mail server which delivers incoming mail to a
standard location like `/var/mail/USERNAME` or `/home/user/Maildir`,
then you are done - Mailpile can read the mail directly from there.

Alternately, if you have been using Thunderbird, then Mailpile can read
your mail directly from the Thunderbird folder in your home directory -
this is obviously a stop-gap measure, until Mailpile is stable enough to
replace Thunderbird.  We're not there yet, but we will be soon!

We have written instructions for a few common configurations:

* [[Configuring Linux to download your e-mail]]
* [[Synchronizing Mailpile with Thunderbird]]

