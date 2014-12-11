![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

This is currently a catch all page for people who run into issues with new or existing installs of Mailpile that get broken due to upgrading and such! Feel free to submit situations you encounter and how you manage to resolve the issue.

# Search index

## I broke my search index, how to fix it ?

If you use development version and follow updates, it's possible your search index get damaged at some point (e.g: format change), the symptom is only recent messages are searchable. To rebuild your whole search index:

1. remove `~/.mailpile/kw-journal.dat` file (if any)
2. remove `~/.mailpile/search/` directory
3. in mailpile shell, do an empty search: `search` 
4. in mailpile shell, do a `rescan all` and be patient :).

This operation is harmless (does not destroy tag information or anything else), but it may take a while!


# Mailboxes and Mail Sources

## How do I look at my mail source settings?

You can use the `print` command to print out the relevant section(s) of the Mailpile configuration. Since the output can be quite verbose, you may prefer to use the `-flat` argument to avoid looking at all the data at once.  If you need to double-check your passwords, you'll use the `-secrets` argument.

Some examples:

    # List which mail sources exist
    mailpile> print -flat sources
    {
        "sources": {
            "9e6bc04lzz8": "{ ... }", 
            "x7skvlsdk3f": "{ ... }"
        }
    }

    # Examine one of them in more detail
    mailpile> print -flat sources.9e6bc04lzz8
    {
        "sources.9e6bc04lzz8": {
            "discovery": "{ ... }", 
            "enabled": true, 
            "host": "imap.gmail.com", 
            "interval": 300, 
            "keepalive": false, 
            "mailbox": "{ ... }", 
            "name": "GMail", 
            "password": "(SUPPRESSED)", 
            "port": 993, 
            "post_command": "", 
            "pre_command": "", 
            "protocol": "imap_ssl", 
            "username": "somemailpileuserorother@gmail.com"
        }
    }

    # To look at the password, you could do this:
    mailpile> print -flat -secrets sources.9e6bc04lzz8
    
    # or this:
    mailpile> print sources.9e6bc04lzz8.password



## Checking & Fixing Mailboxes

There is a tool which exists that checks and attempts to fix issues with given mailboxes such as duplicates and such. To use this functionality run the following command whereby `001a` is the mailbox id:

`mailpile> hacks/chkmbx -clean 001a`



## Backing up (and restoring) downloaded mail

It is a good idea to make a backup of any mail you have downloaded, before performing manual surgery on your Mailpile's mailboxes and mail sources.  The steps are as follows:

1. Use `print` to figure out the mailbox ID or IDs you want to back up
2. Use `order` to disable threading in search results
3. Use `search` to find the mail in that mailbox
4. Use `export` to make a copy of the mail
5. Use `add` to re-add the exported mail to your Mailpile (optional)

Note: this procedure applies to any mail you can find with a search, not just mail source data - just skip step 1 and use what ever search describes the mail you want backed up / exported.

Examples:

    # Print a list of mailboxes associated with a particular mail source
    # The list may be long, in this example we just show the INBOX entry
    mailpile> print sources.9e6bc04lzz8.mailbox
    ...
        "0006": {
            "apply_tags": {
                "0000": "c"
            }, 
            "local": "/home/user/.local/share/Mailpile/default/mail/df30f", 
            "name": "INBOX", 
            "path": "@0006", 
            "policy": "read", 
            "primary_tag": "16", 
            "process_new": true
        }, 
    ...

    # Disable threading in this session
    mailpile> order rev-flat-date

    # Search for mail found in that mailbox:
    mailpile> search mailbox:0006

    # If you want to include messages that have been trashed or marked
    # as spam, you need to do this:
    mailpile> search all:mail +in:trash +in:spam mailbox:0006

    # Save the results of the previous search to a file
    mailpile> export -flat all mbox:/home/user/exported-0006.mbx

    # If you only want to save messages 5, 6 and 11:
    mailpile> export -flat 5 6 11 mbox:/home/user/exported-0006.mbx

    # If you want to back up entire conversations (potentially including
    # mail not matching the original search query), omit the -flat argument
    mailpile> export 5 6 11 mbox:/home/user/exported-0006.mbx

    # To re-import exported mail, put the mbox file somewhere nice and:
    mailpile> add /path/to/exported-0006.mbx


## My downloaded mail seems wonky

If you have the feeling that too much or too little mail is being downloaded, you can try to reset the local copy of a remote mail source and see if things behave better.  The procedure is as follows:

1. Find the mailbox ID of the copy you want reset
2. Make a backup of the mail (optional, see above)
3. Shut down Mailpile
4. Delete the pickled mailbox state file, `.../Mailpile/default/pickled-mailbox.XXID`
5. Delete the downloaded mail, `.../Mailpile/default/mail/XXXXX`
6. Restart Mailpile
7. Add the backups as a local mailbox (optional)
8. Rescan your mail

Note that this procedure inevitably will force Mailpile to download your mail all over again. If you have deleted things from the IMAP server, you will lose them permanently unless you make backups. This can be quite annoying, as metadata about those messages will remain in the search index, potentially leading to ghost results.

See above for hints on how to find your Mailpile folder and for the basics on finding your mail source settings.

Examples of relevant commands:

    # Finding mailbox details - here you can see the mailbox with the ID 0006
    # stores local mail here: /home/user/.local/share/Mailpile/default/mail/df30f 
    #
    mailpile> print sources.SOURCEID.mailbox
    ...
        "0006": {
            "apply_tags": {
                "0000": "c"
            }, 
            "local": "/home/user/.local/share/Mailpile/default/mail/df30f", 
            "name": "INBOX", 
            "path": "@0006", 
            "policy": "read", 
            "primary_tag": "16", 
            "process_new": true
        }, 
    ...

    # Rescanning your mail (press CTRL-C to abort at any time)
    mailpile> rescan both

## I get an ImportError when running ./mp inside a virtualenv 

    $ ./mp
    ImportError: No module named jinja2

Check that you're using a recent version of virtualenv (1.7 doesn't work, 1.11 does).

Virtualenv works by overriding the `python` command in your current shell by modifying `PATH`.

Older versions did not override `python2`. The `mp` script specifies `python2` as its interpreter. So with old versions of virtualenv, when your shell invokes `python2` it runs outside of the virtualenv and without your dependencies.

[#1106](https://github.com/mailpile/Mailpile/issues/1106) shows the fix on Ubuntu 12.04