Mailpile doesn't yet know how to read and index encrypted e-mail, but it
will in the future.  In the future Mailpile may also know how to log on to
your remote IMAP and POP3 accounts and download or index remote mail.
This means for sensitive messages, the search index becomes a potential
security risk, as does the configuration file.  More broadly, easy access
to all your communications can be a privacy risk in and of itself:
consider the search `naked att:jpg` as an example.  It is almost certainly
worth taking steps to protect your Mailpile.

The simplest and most effective strategy, is to store your `.mailpile`
folder on an encrypted volume.

Alternately, if you have a GPG key and run Mailpile in an environment
where gpg-agent is available for key management, you can tell Mailpile
to encrypt its config and data using your key, like so:

    $ ./mp --set "prefs.gpg_recipient = youremail@yourdomain.com"

Note that this only encrypts the main index and config file, and only
works if `gpg` is in your path. The search terms themselves are not
encrypted, which means the contents of individual messages could at
least in part be derived from the index.  This problem can be mitigated,
at the cost of some performance, by telling Mailpile to use a one-way
hash to obfuscate the search terms:

    $ ./mp --set "prefs.obfuscate_index = Some RaNdoM LongISH SECRET"

Note that if you change this setting, whatever has already been indexed
will "disappear" and become unfindable.  So do this first if you do it
at all!
