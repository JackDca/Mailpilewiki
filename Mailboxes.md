![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

### Adding Mailboxes

**Note:** The primary way to do this, is to use the **Browse** tool
from the Accounts (Home) page in the app. If you would rather do it by
hand on the CLI, see below.

1. Add mailbox `mailpile> add your@account.com /path/to/your/something.mbox`

This will add the mailbox to your Pile, associating it with the specified
account (which should already exist).

Once that is done importing the new mailbox you just added, you may want
to apply some tags. Do that by the following commands

1. Add the tag "Custom Tag" `mailpile> tags/add "Custom Tag"`
2. Search for that mailbox `mailpile> search mailbox:something.mbox`
3. Tag that search with "Custom Tag" `mailpile> tag +custom-tag all`

There you go, now that mail will show up in your Mailpile under the
new tag.

### Deleting Mailboxes

Currently to make entire mailboxes disappear is a bit of a hack:

1. Search for that mailbox `mailpile> search mailbox:something.mbox`
2. Tag that search as Trash `mailpile> tag -inbox +trash all`
3. Find the ID of the mailbox `mailpile> print sys.mailbox`
4. Replace the mailbox path: `mailpile> set sys.mailbox.1234 = /dev/null`

(Replace 1234 in step 4, with the ID found in step 3.)
