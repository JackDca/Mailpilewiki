![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

The most important command Mailpile supports is the `search` command.
The second most important is probably `help`. :-)

All commands can be abbreviated to only their first character (the less
commonly used commands use capital letters for this).

### Searching ###

Some searching examples:

    $ ./mp
    mailpile> search bjarni einarsson
    ...
    mailpile> search subject:bjarni
    ...
    mailpile> search from:bjarni to:somebody
    ...
    mailpile> search from:bjarni -from:pagekite
    ...
    mailpile> search group:family -from:mom
    ...
    mailpile> s att:pdf
    ...
    mailpile> s has:attachment
    ...
    mailpile> s date:2011-1-30 +date:2011-1-29
    ...
    mailpile> s year:2011 month:12
    ...
    mailpile> s dates:2011-12..2012-04-15
    ...
    mailpile> s mailbox:path/fragment/or/filename
    ...

The default search will search in message bodies, from lines, attachment
names and subjects.  Using a `to/from/subject/att/...` prefix will
search that part of the message only.  There's no way to *only* search
bodies, they're too full of crap anyway.

Adding terms narrows the search, unless the extra terms are prefixed with
a `+`, then results are combined.  Prefixing with `-` removes matches for
that term instead.

You can paginate through results using `next` and `previous`.

To view a message, use the `view` command with the number of the result
or one of the magic words `all` or `these`:

    mailpile> search year:2011 month:12
    ...
    mailpile> view 1 2 6
    ...

(Mailpile currently assumes you have `less` installed and in your path for
viewing e-mail. This is a temporary hack.)

You can also search from the command line with `mp -s term`,
but that will be a bit slower because the metadata index has to be
loaded into RAM on each invocation.


#### Special search terms ####

Here is a brief list of the special search terms:

    all:mail         All messages
    att:<word>       Search within attachment file names
    dates:<B>..<E>   Search dates from B to E
    in:spam          Same as tag:Spam
    in:trash         Same as tag:Trash
    is:unread        Same as tag:New
    group:<name>     Messages from people in a group
    has:attachment   Messages with attachments
    has:pgp          Messages with signed or encrypted content
    togroup:<name>   Messages to people in a group


### Sorting the results ###

The `order` command lets you sort results.  Available sort orders
are: `index`, `random`, `date`, `from` and `subject`.  Threading
may be disabled by prefixing the order with `flat-`, and the order
may be reversed by further prefixing it with `rev-`.  Examples:

    mailpile> order rev-subject    # Reverse subject order
    ...
    mailpile> order rev-flat-date  # Flat reverse date order
    ...
    mailpile> order                # Default sort order
    ...

You can also change the default sort order by using the `order`
setting:

    mailpile> set order = rev-flat-date  # Change default order
    ...
    mailpile> unset order                # Use program defaults
    ...


### Tags and filters ###

Mailpile allows you to create tags and attach any number of tags to each
message.  For example:

    mailpile> tag add Inbox
    ...
    mailpile> search to:bre from:klaki
    ...
    mailpile> tag +Inbox all
    ...
    mailpile> inbox
    ...

The `tag` command accepts a single tag name, prefixed with a `+` or `-`
(for adding or removing the tag), followed by a description of messages.
The message description can be:

- `all` will affect all messages
- `these` will affect currently listed messages
- A list of numbers or ranges (`1 2 3 5-10 15`)

All these are relative to the last search, so `1` is the first result
of the most recent search and `all` would be all matching messages.

Tags names are themselves recognized as specialized search commands in
the `mailpile` CLI.

If you want Mailpile to automatically tag (or untag) messages based on
certain search criteria, you can use the `filter` command instead:

    mailpile> tag add Lists/Diaspora
    ...
    mailpile> search list:diaspora
    ...
    mailpile> filter +lists/diaspora -inbox Diaspora Mail
    ...

This will tag all the search results and then apply the same rules as
new messages are received.

Filters are always processed in a fixed order, so even if one filter
adds a tag, a subsequent one may remove it again.  This allows you to
define common patterns such as "All mail goes to the Inbox and is
tagged as new, except this mailing list and that junk mail".  Run the
`filter` command on its own to get a brief summary of how to remove,
edit or reorder the filters.
