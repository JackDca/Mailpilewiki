This page describes how to configure Ubuntu or Debian to download your
e-mail from a remote server. These instructions probably apply to other
operating systems as well, but the commands for installing software
(`apt-get install` etc.) may differ.

Two methods are described: **OfflineIMAP** and **postfix + fetchmail**.
They can be summarized roughly as follows:

* **OfflineIMAP** just downloads your mail to a local folder.
* The **postfix + fetchmail** solution configures a full local mail server.

The former is simpler, the latter is a more advanced - more flexible and
more complicated - configuration.

So, choose your poison!

--------------------------------------------------------------------

## OfflineIMAP

### Installing OfflineIMAP

That should be as simple as:

    sudo apt-get install offlineimap

### Configuring OfflineIMAP

OfflineIMAP reads its configuration from a file named `.offlineimaprc` in
your home directory. You can edit (or create) it using a command line
`nano $HOME/.offlineimaprc`.

For a simple case of one remote IMAP account, all mail downloaded to a
folder named `MyMail` in your home directory, the configuration file
should look something like this:

    [general]
    accounts = MyMail

    [Account MyMail]
    localrepository = Local
    remoterepository = Remote

    [Repository Local]
    type = Maildir
    localfolders = ~/MyMail

    [Repository Remote]
    type = IMAP
    remotehost = imap.yourprovider.com
    remoteuser = yourname
    remotepass = yourpassword

If downloading from GMail, the last section might be better written like so:

    [Repository Remote]
    type = Gmail
    cert_fingerprint = 89091347184d41768bfc0da9fad94bfe882dd358
    folderfilter = lambda folder: folder.startswith('[Gmail]/All Mail')
    remoteuser = yourname@gmail.com
    remotepass = yourpassword

This will only download the "All Mail" section.

You can test your OfflineIMAP configuration by running `offlineimap`. If it
looks like it is about to take forever, you can abort it by pressing CTRL+C.

### Configuring Mailpile

You can configure Mailpile to periodically invoke OfflineIMAP for
you. In the Mailpile CLI, type:

    # Run offlineimap before looking for new mail
    mailpile> set prefs.rescan_command = offlineimap || true

    # Configure Mailpile to look for new mail every 300 seconds
    mailpile> set prefs.rescan_interval = 300

Finally, you need to tell Mailpile where to find your mail:

    # Add your mailbox to the list of locations to scan
    mailpile> add ~/MyMail

    # Read it now! Invokes offlineimap and then scans for new mail.
    mailpile> rescan

That's all folks!


--------------------------------------------------------------------

## postfix + fetchmail

### Installing postfix

We like postfix, but hese instructions probably also work almost
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
    poll pop.gmail.com with proto POP3
      user 'joe@gmail.com' there with password 'SEKRIT'
      is unixjoe here
      nofetchall keep options ssl

To create this file,use `nano $HOME/.fetchmailrc` in the shell (or your
editor of choice), and afterwards run `chmod 0700 $HOME/.fetchmailrc` to
set the correct access controls.

(*Note for GMail users:* you cannot use your normal password with
fetchmail if you are using two-factor authentication. Instead you
will need to create a custom password using GMail's security tools.)

This setup can then be tested by running the following command:

    # Fetch some mail!  (hit CTRL+C to abort)
    fetchmail

    # Or if you really need verbose output for debugging:
    fetchmail -V -v --nodetach --nosyslog`


### Configuring Mailpile

Once local mail delivery and fetchmail are both working, you can either use
a cron job to periodically download your e-mail, or configure Mailpile to
periodically invoke fetchmail for you. In the Mailpile CLI, type:

    # Run fetchmail before looking for new mail
    mailpile> set prefs.rescan_command = fetchmail || true

    # Configure Mailpile to look for new mail every 300 seconds
    mailpile> set prefs.rescan_interval = 300

Finally, you need to tell Mailpile where to find your mail:

    # Add your mailbox to the list of locations to scan
    mailpile> add /var/mail/unixjoe

    # Read it now! Invokes fetchmail and then scans for new mail.
    mailpile> rescan

That wasn't too hard, was it?