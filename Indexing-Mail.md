![Guide](https://github.com/mailpile/Mailpile/wiki/images/page-guide.png)

Mailpile is built around a search engine and before you can read or sort your e-mail, Mailpile has to add it to the local search index. Usually this will happen automatically once you have configured Mailpile with the details of one or more accounts.

However, many power users already have existing e-mail on their hard drives or have specific opinions about how they would like their mail to be processed. If you are one of those users, read on to learn a bit more about how Mailpile processes mail and how you can customize and control the experience.

In this guide:

   * [What Happens During Indexing?](#what-happens-during-indexing)
   * [When Does Indexing Happen?](#when-does-indexing-happen)
   * [Manually Configuring Mail Sources](#manually-configuring-mail-sources)
      * [Examining Your Settings](#examining-your-settings)
      * [Creating a New Mail Source](#creating-a-new-mail-source)
      * [Changing Settings](#changing-settings)
   * [Examples](#examples)
      * [Auto-discover New Local Mailboxes](#auto-discover-new-local-mailboxes)

**Important:** Low-level configuration tools do not yet exist for the web interface of Mailpile; in order to benefit from this guide you will need to make use of the Mailpile command-line interface. Changing these settings is NOT without risk - you can break your Mailpile if you get things wrong here!


## What Happens During Indexing?

Discovering and indexing new mail happens in a few stages.

1. Configuration: *create mail sources*
2. Mailbox discovery: *apply discovery policies*
3. Message copying: *update local mailboxes*
4. Message indexing: *record metadata and search terms, apply tags*

Configuration is accomplished either by using the Add Account wizard, or manually as described in this document. This tells Mailpile to look for mail and what to do with the mail it finds. You will generally create one or more [[mail sources]], each of which describes a remote IMAP or POP3 account or a set of mailboxes already on your local computer.

Mailbox discovery is a process whereby Mailpile will automatically discover which folders exist on an IMAP server or which mailboxes exist in a local directory. This happens periodically, so if a new folder or mailbox is created, Mailpile can automatically react and make that mail available for use. How exactly this happens is decided by the *discovery policy* of the relevant *mail source*.

Message copying is the act of downloading mail from a remote server to Mailpile's (usually encrypted) local mail store. This step may be skipped, but is generally recommended.

Indexing is the final stage, when each e-mail is individually read and added to Mailpile's index. The metadata index will store things like the date, subject and sender. [[Tags]] will be applied based on global filter rules, auto-tagging, as well as any configuration of the mail source itself. The search index will be updated using keywords extracted from the message header, body and attachments. This stage may involve decrypting the mail or processing large attachments, and may be somewhat resource intensive as a result.


## When Does Indexing Happen?

Indexing happens on a per-mail-source basis and each mail source keeps its own schedule depending on the type of mailbox, network connectivity and other factors.

The status of each mail source is recorded in the [[Event Log]]; eventually there should be nice interfaces to browse this data, but for now you can take a look either by visiting `/api/0/eventlog/incomplete/` in your browser or by running the `eventlog incomplete` command at the *mailpile>* prompt. In the latter case, piping through `less` is advised:

    mailpile> pipe less eventlog incomplete
    ...

If you are impatient and want a round of indexing to happen *right now*, you can trigger it using the `rescan` command:

    # Rescan everything, not just mail
    mailpile> rescan full
    ...

    # Rescan only mail sources
    mailpile> rescan sources
    ...

    # Trigger a rescan of mailbox 000b only
    mailpile> rescan mailbox:000b
    ...


## Manually Configuring Mail Sources

Manually configuring mail sources is a large topic, arguably one of the most complex parts of Mailpile.


### Examining your Settings

Mail source settings live in two sections of the Mailpile configuration tree; `sys.mailbox` and `sources`. You will need to examine both of these structures to get a full understanding of how your Mailpile is configured. An example is presented and discussed below.

First, it's useful to get a short list of what sources exist:

    mailpile> print -flat sources
    {
       "sources": {
            "abcdefg": "{ ... }", 
            "1234xyz": "{ ..(Local mailboxes).. }", 
            "999000x": "{ ..(Personal GMail).. }"
        }
    }

Next, let's examine one in more detail, using less:

    mailpile> pipe less print sources.abcdefg
    {
        "sources.abcdefg": {
            "name": "Personal GMail", 
            "profile": "random-vcard-id-string", 
            "enabled": true, 
            "host": "imap.gmail.com", 
            "interval": 300, 
            "keepalive": false, 
            "auth_type": "password", 
            "username": "user.name@gmail.com"
            "password": "(SUPPRESSED)", 
            "port": 993, 
            "post_command": "", 
            "pre_command": "", 
            "protocol": "imap_ssl", 

Above we have settings pertaining to the source of mail; this is an IMAP source (GMail) which is linked to a profile (an account). The source checks for new mail every 300 seconds (interval: 300) and disconnects after each batch (keepalive: false).

Next we see the discovery policy. Whenever a new folder is discovered on the IMAP server, an entry is added to the `mailboxes` section below and it is configured in accordance with this policy. In this case, the default is to apply the policy "unknown" which effectively ignores the new folder:

            "discovery": {
                "apply_tags": {}, 
                "create_tag": true, 
                "guess_tags": true,
                "local_copy": true, 
                "parent_tag": "27", 
                "paths": {}, 
                "policy": "unknown", 
                "process_new": true, 
                "visible_tags": false
            }, 
            "mailbox": {
                "000w": {
                    "apply_tags": {}, 
                    "local": "/home/USER/.local/share/Mailpile/default/mail/12345", 
                    "name": "MyGmailTag", 
                    "path": "@000w", 
                    "policy": "unknown",
                    "primary_tag": "Personal GMail/Baronsstigur39", 
                    "process_new": true
                }, 

Above we can see an IMAP folder with the id `000w`, below is another with the ID `0011`. The first is unconfigured (policy: unknown), in accordance with the discovery policy.

The second (the INBOX) has however been manually configured for *reading mail*, keeping local copies at a specific path, applying both tags `28` and `a` to all mail, and processing mail as "new", which means the unread flag will be set if necessary.

                "0011": {
                    "apply_tags": {
                        "0000": "a"
                    }, 
                    "local": "/home/USER/.local/share/Mailpile/default/mail/67890", 
                    "name": "INBOX",
                    "path": "@0011",
                    "policy": "read",
                    "primary_tag": "28",
                    "process_new": true
                }
            }
        }
    }

Finally, we can examine some of the resources mentioned in the settings above:

    # Examine the sys.mailbox entry for the INBOX
    mailpile> print sys.mailbox.0011
    {
        "sys.mailbox.0011": "src:sdpslfj/INBOX"
    }

    # Examine tag "a":
    mailpile> print tags.a
    ...

    # Examine the VCard for the linked profile
    mailpile> vcard random-vcard-id-string
    ...

### Creating a new Mail Source

You can create a new mail source simply by adding an entry to the sources section. There are many, many ways to do this, but here is a relatively complete example to get you started:

    # Create a source with the ID "test"
    mailpile> set sources.test = {"name": "Testing", "protocol": "local"}
    ...

    # Take a look at the result:
    mailpile> print sources.test
    ...

You should probably link this new source with an account, to make sure it behaves "as expected" in the Mailpile user interface:

    # Create a new account (skipping this step is OK)
    mailpile> profiles/add user@example.com = User McTesterson
    ...

    # Examine the profile, look for the profile-tag and rid lines:
    mailpile> profiles --lines user@example.com
    ...
    19 990.3 x-mailpile-profile-tag: 16 (pref=None)
    25       x-mailpile-rid: 9c655ba4912
    ...
    mailpile> set sources.test.profile = 9c655ba4912
    ...
    mailpile> set sources.test.discovery.apply_tags.0 = 16
    ...

You may also want to change the value of `sources.test.discovery.local_copy` or other discovery settings if you want Mailpile to make copies of the mail or you don't like the default tag creation policy.

We can now in a single command add and configure a new mailbox:

    mailpile> add user@example.com /path/to/file.mbx
    ...

We could also add an entire folder full of mailboxes all at once:

    mailpile> add --recurse user@example.com /home/USER/Mail/

And finally, if we expect new mailboxes to show up in that folder now and then, we could configure auto-discovery:

    mailpile> set sources.test.discovery.policy = read
    ...
    mailpile> set sources.test.discovery.paths.0  = /home/USER/Mail/
    ...


### Changing Settings

Settings can be examined and changed using the "standard" `print` and `set` commands as illustrated above.

The only setting **you should never change** is the `protocol` attribute of a working mail source. If you change the protocol, any existing mailboxes and all of the mail they contain may become inaccessible (unless you enabled local copies).

If you change the authentication (username and password) or host or port of a remote source, the same thing may happen.


## Examples

### Auto-discover New Local Mailboxes

This is what a configuration similar to the one described above looks like when complete. It will discover new mailboxes (in mbox or Maildir format) automatically.

(Default values that do not matter have been omitted for brevity.)

    "sources.test": {
        "name": "Testing", 
        "enabled": true, 
        "interval": 300, 
        "protocol": "local", 
        "profile": "299af055371", 
        "discovery": {
            "apply_tags": {
                "0000": "1z"
            }, 
            "create_tag": true, 
            "guess_tags": true, 
            "local_copy": false, 
            "parent_tag": "20", 
            "paths": {
                "0000": "/home/USER/Mail/"
            }, 
            "policy": "read", 
            "process_new": true, 
            "visible_tags": false
        }, 
        "mailbox": {
            "000q": {
                "apply_tags": {
                    "0000": "1z"
                }, 
                "local": "", 
                "name": "mailpile-1407513984.mbx", 
                "path": "@000q", 
                "policy": "read", 
                "primary_tag": "21", 
                "process_new": true
            }
        }
    }

***

*Please feel free to edit and add your own configuration examples here!*