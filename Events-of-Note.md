This page documents some of the more interesting events that can be found in the event log.

## mailpile.mail_source.*

All Mailpile mail sources will record their internal state in an "incomplete" event.  Thus the state of any mail source can be examined via. the event log.  The following sections are standard in all Mail Source events.

     "id": <the mail source ID>, 
     "mailbox_state": { ... },
     "copying": {
         "batch_size": 5, 
         "complete": true, 
         "copied_bytes": 0, 
         "copied_messages": 0, 
         "mailbox_id": "001n", 
         "running": false, 
         "total": 0
     }, 

### `copying` - progress info

The final section, `copying`, appears if mail was (or is being) copied from a source mailbox to a local mailbox. It shows statistics which can be used to generate a progress-bar or otherwise inform the user about in-progess copies.

The `running` attribute tells us whether the copy is still in progress or whether it has finished.

If `complete` is true, then that means all known mail has been copied (or downloaded).


## mailpile.mail_source.* / mailpile.commands.Rescan

The `data` section of these events will contain two subsections, `rescan` and `rescans`, pertaining to what happened during the process of reading new mail (indexing for the search engine, initial tagging, etc.)  These data sections look like so:

     "rescan": {
         "added": 0, 
         "batch_size": 5, 
         "complete": true, 
         "errors": [], 
         "mailbox_id": "001j", 
         "running": false,
         "total": 2, 
         "updated": 0
     }, 
     "rescans": [
         [<Mailbox ID>, <Count>, <Human readable message>, { ...stats... }],
         ...
     ]

### `rescan` - progress info

The first, `rescan`, describes the most recent rescan operation, which may still be in progress. This can be used to generate a progress bar.

The `running` attribute will be True if the operation is still running.

The `complete` attribute will be True if all known messages have been processed.

### `rescans` - history

The second section, `rescans`, gives a summary of the recent rescan operations and how they went.  Each entry in the history is a list of four values; the Mailbox ID, the count of messages added to the index (or -1 if there was an error), a human readable description and finally a dictionary describing the final state in machine-readable form: how many messages were updated, how many were added, and whether the rescan was considered complete or not.


## mailpile.mail_source.imap.*

The IMAP Mail Source (and other network sources such as POP3 in the future) will record information about network activity on its event, like so:

     "connection": {
         "error": [false, "Nothing is wrong"], 
         "live": true
     }, 

The `error` attribute is a list of two items, the first being an error state (False if there is no error or one of "network", "auth", "timeout" and "imap" if there was a problem) and the second being a human readable description of the problem.

The `live` event is True if the connection is currently in a usable state, otherwise False.
