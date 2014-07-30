# Mail sources

Mailpile's Mail Sources take care of importing mail into Mailpile's local storage and adding to the index. Mail sources can represent remote servers (IMAP, POP3, ...) or local sources such as a Thunderbird installation or Unix mail spool.

Eventually mail sources will also be able to synchronize changes back, e.g. for keeping a remote IMAP server in sync with Mailpile's internal state.


## Architecture

Each configured Mail Source gets a dedicated thread, which implements its own schedule for updates. The updates are twofold:

1. Discovery and processing of new mailboxes (or folders) containing email
2. Discovery and processing of new email messages

(Later implementations may add a 3rd stage, which synchronizes local changes back to the remote source.)

In order to prevent mail source activity from overloading the app's responsiveness, all mail sources will attempt to acquire the `GLOBAL_RESCAN_LOCK` before performing any intensive operations (so we only ever have one mail source update active at any given time), and all mail sources are expected to periodically invoke `play_nice_with_threads()` to release the GIL.


## Supported source types

As of 2014-07-30, the following mail sources have been implemented:

* local folder containing mbox files
* local folder containint Maildirs (e.g. a nested Maildir)
* remote IMAP4rev1 servers

Expected by Mailpile 1.0:

* Mac Mail.app
* POP3


## Configuration

**Note:** This section describes the mail source configuration as of 2014-07-30, please consult the definition in `mailpile/defaults.py` if any time has passed.

Below is the current mail source spec. The bottom of this section has some real-life examples of correctly configured mail sources.

        'name':            (_('Source name'), str, ''),
        'protocol':        (_('Mailbox protocol or format'),
                            ["mbox", "maildir", "macmaildir", "gmvault",
                             "imap", "imap_ssl", "pop3"],
                            ''),
        'pre_command':     (_('Shell command run before syncing'), str, ''),
        'post_command':    (_('Shell command run after syncing'), str, ''),
        'interval':        (_('How frequently to check for mail'), int, 300),
        'username':        (_('User name'), str, ''),
        'password':        (_('Password'), str, ''),
        'host':            (_('Host'), str, ''),
        'port':            (_('Port'), int, 993),

The above values are the global settings for this source, specifying what kind of mail source it is and details such as access credentials.

The next section defines the "discovery policy", defining how Mailpile searches for new mailboxes or folders, and what it does when it finds one:

        'discovery':       (_('Mailbox discovery policy'), False, {
            'paths':       (_('Paths to watch for new mailboxes'), str, []),
            'policy':      (_('Default mailbox policy'),
                            ['unknown', 'ignore', 'watch',
                             'read', 'move', 'sync'], 'unknown'),
            'local_copy':  (_('Copy mail to a local mailbox?'), bool, False),
            'create_tag':  (_('Create a tag for each mailbox?'), bool, True),
            'process_new': (_('Is a potential source of new mail'), bool, True),
            'apply_tags':  (_('Tags applied to messages'), str, []),
        }),

The above discovery policy will be used to auto-create mailbox entries, as describe in the final section.

Usually, the discovery policy should be set to `unknown`, so new mailboxes will be analyzed but no action taken until the user has confirmed the proposed configuration.

        'mailbox': (_('Mailboxes'), {
            'path':        (_('Mailbox source path'), str, ''),
            'policy':      (_('Mailbox policy'),
                            ['unknown', 'ignore', 'read', 'move', 'sync'],
                            'ignore'),
            'local':       (_('Local mailbox path'), str, ''),
            'process_new': (_('Is a source of new mail'), bool, True),
            'primary_tag': (_('A tag representing this mailbox'), str, ''),
            'apply_tags':  (_('Tags applied to messages'), str, []),
        }, {})


## Examples

(TO BE WRITTEN)