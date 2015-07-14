![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

### Adding Mailboxes

1 Add mailbox `mailpile> add /path/to/your/something.mbox`

Once that is done importing the new mailbox you just added, you may want to apply some tags. Do that by the following commands

1. Add the tag "Custom Tag" `mailpile> tags/add Custom Tag`
2.Search for that mailbox `mailpile> search mailbox:something.mbox`
3. Tag that search with "Custom Tag" `mailpile> tag +custom-tag all`

There you go, now that mail will show up in your Mailpile under the 

### Deleting Mailboxes

Currently to make entire mailboxes disappear is a bit of a hack (and only
works from the CLI), but here you go:

1. Search for that mailbox mailpile> "search mailbox:something.mbox"
2. Tag that search as Trash mailpile> "tag -inbox +trash all" on the CLI
3. Quit mailpile and edit .mailpile/mailpile.cfg
4. Replace the mailboxes you are deleting with the path with /dev/null
5. Restart mailpile
