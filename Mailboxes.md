

### Deleting Mailboxes

Currently to make entire mailboxes disappear is a bit of a hack (and only
works from the CLI), but here you go:

1. Search for that mailbox mailpile> "search mailbox:something.mbox"
2. Tag that search as Trash mailpile> "tag -inbox +trash all" on the CLI
3. Quit mailpile and edit .mailpile/mailpile.cfg
4. Replace the mailboxes you are deleting with the path with /dev/null
5. Restart mailpile
