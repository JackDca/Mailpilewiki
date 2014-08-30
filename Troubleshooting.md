This is currently a catch all page for people who run into issues with new or existing installs of Mailpile that get broken due to upgrading and such! Feel free to submit situations you encounter and how you manage to resolve the issue.

# Search index

## I broke my search index, how to fix it ?

If you use development version and follow updates, it's possible your search index get damaged at some point (e.g: format change), the symptom is only recent messages are searchable. To rebuild your whole search index :

This operation is harmless (do not destroy tag information or anything else).

1. remove `~/.mailpile/kw-journal.dat` file (if any)
2. remove `~/.mailpile/search/` directory
3. in mailpile shell, do an empty search : `search` 
4. in mailpile shell, do a `rescan all` and be patient :).
