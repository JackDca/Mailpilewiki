![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

**Please refer to [[Configuring mailpile]] for an easy guide to Mailpile configuration.**

Once you've got Mailpile running on your machine, from Mailpile's command line interface, do the following:

1. `setup`  -- run the setup command to instantiate the absolute basics
2. Point your web browser at [http://localhost:33411](http://localhost:33411)
3. Use the GUI installer that you see from accessing the above URL

## Running from an alternative config directory
By default, Mailpile reads its config from `.local/share/Mailpile/` (Or `~/.mailpile` in previous versions, which presence will still be tested for backward compatibility)

You can override this by setting the `$MAILPILE_HOME` environment variable, for example:

    $ export MAILPILE_HOME=/home/user/.config/mailpile

or for a single run:

    $ MAILPILE_HOME=/home/user/.mailpile-alternative ./mp

