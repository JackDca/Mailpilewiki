## Clean install
Once you've got Mailpile running on your machine, from Mailpile's command line interface, do the following:

1. `setup`  -- run the setup command to instantiate the absolute basics.
2. Point your web browser at http://localhost:33411

## Setting up Basic Config
For best results, the first step is to tell the program your e-mail
address and set up basic tags (`New`, `Inbox`, etc.) and filters so
Mailpile will behave like a normal mail client.  Mailpile can do this
for you, but if you are importing lots of old mail, you may want to
postpone the filter definition until after the import (see below), to
start with a clean slate:

    $ ./mp --setup
    $ ./mp --set "profiles.0.email = yourmail@domain.com"
    $ ./mp --set "profiles.0.name = Your Name"
    ...

If you do not have a local working mail server in `/usr/sbin/sendmail`,
you may also want to configure a default outgoing SMTP server:

    $ ./mp --set "profiles.0.route = smtp://postmaster@mailserver.org:password@smtp.mailserver.org:25"
    ..

Mailpile does not by default access IMAP or POP3 servers directly, it
relies on other tools (such as `fetchmail`) to take care of downloading
new mail.

**Note:** You can add multiple accounts by replacing the `0` in the profile
variable name with higher numbers.

## Running from an alternative config directory
By default, Mailpile reads its config from `~/.mailpile`

You can override this by setting the `$MAILPILE_HOME` environment variable, for example:

`$ export MAILPILE_HOME=/home/user/.config/mailpile`

or for a single run:

`$ MAILPILE_HOME=/home/user/.mailpile-alternative ./mp`

## Other configuration

* See [[Config]]