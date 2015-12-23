Mailpile provides a simple script for integrating with the Apache
web-server.

## What it does

Creates a /mailpile/ namespace in your Apache configuration, which is
used to launch and access Mailpile instances.

To launch your Mailpile, simply visit <http://example.com/mailpile/>
(replace the domain with your actual domain, or `localhost`) and enter
your Unix username and password. This will start Mailpile in the
background and let you start using it.


## Installation

The easy way to install, is to use the automated installation script:

    sudo ./scripts/mailpile-apache.py --install-apache

If you would rather install by hand, the installer performs the following
steps (read the source for full details):

1. Installs `screen`
2. Run `a2enmod headers rewrite proxy proxy_http`
3. Create `/etc/apache2/conf-enabled/mailpile.conf`
4. Create `/var/lib/mailpile/apache/`
5. Run `apache2ctl restart`

(Note: this list is almost certainly incomplete)


## Security

Please use <www.letsencrypt.org> to enable SSL for your site. Without it
your e-mail (and potentially your PGP keys) may be at risk.

Note that the running Mailpile instances will have e-mail account
credentials (usernames and passwords), as well as PGP key passphrase
material in RAM. A hostile server administrator could modify the running
Mailpile to steal both, read and modify your e-mail.

For this reason we recommend always running Mailpile on hardware you
control.


## Performance

Performance should be fine, Apache's proxy is relatively efficent.

However, note that the more Mailpile instances you have running, the
more RAM will be consumed on your machine. Users that have a lot of
e-mail will consume a lot of RAM, so be sure your server is sized
accordingly.


## Enabling remote access

Check out <https://pagekite.net/>! It should work with Let's Encrypt.


## How it works

Mailpile's Apache integration makes use of Apache's built in proxy to
forward traffic to running Mailpile instances, and keeps track of which
local ports each user's Mailpile runs.

If Mailpile is not running for a given user, the user is directed to a
log-in page where they can enter their username and password to start
Mailpile in the background.

Internally, `su` is used to launch Mailpile in a `screen` session. Next
the launcher script updates an Apache `.htaccess` file to make the new
instance reachable by adding proxy rules.
