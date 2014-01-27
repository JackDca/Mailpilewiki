This page describes how to configure the Ubuntu or Debian operating system
for local mail delivery.  We recommend `postfix` and `fetchmail`, but other
software can certainly be used to acheive the same effect.


### Installing postfix

We like postfix, but thhese instructions probably also work almost
verbatim for other common mail servers (exim for example).

    sudo apt-get install postfix fetchmail

The package installer will ask what kind of mail configuration you
prefer. Usually "Local only" is fine - you can update the settings at
any time with `sudo dpkg-reconfigure postfix`.


### Installing fetchmail

To configure fetchmail, create a file named `.fetchmailrc` in your
home directory. If your Unix account is named `unixjoe`, and you have
a GMail account, contents like the following might work:

    set postmaster unixjoe
    poll pop.gmail with proto POP3
      user 'joe@gmail.com' there with password 'SEKRIT'
      is unixjoe here nofetchall keep options ssl

This can then be tested by running the following command:

    fetchmail

    # Or if you really need verbose output for debugging:
    fetchmail -V -v --nodetach --nosyslog`


### Configuring Mailpile

Once local mail delivery and fetchmail are both working, you can either use
a cron job to periodically download your e-mail, or configure Mailpile to
periodically invoke fetchmail for you:

    # Run fetchmail before looking for new mail
    ./mp --set 'prefs.rescan_command = fetchmail || true'

    # Configure Mailpile to look for new mail every 300 seconds
    ./mp --set 'prefs.rescan_interval = 300'

Finally, you need to tell Mailpile where to find your mail:

    ./mp --add /var/mail/unixjoe
