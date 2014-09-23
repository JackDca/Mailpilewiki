![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

Searching is all about disk seeks.

Mailpile tries to keep seeks to a minimum: any single-keyword search can
be answered by opening and parsing one relatively small file, which should
take on the order of 200-400ms, depending on your filesystem and hard
drive.  Repeated searches or searches for closely related keywords will be
up to 10x faster, due to help from the OS cache.

This *includes* the time it takes to render the list of results.

This level of performance is possible, because all the metadata about the
messages themselves is kept in RAM.  This may seem extravagant, but on
modern computers you can actually handle massive amounts of e-mail this way.

Mailpile stores in RAM about 180 bytes of metadata per message (actual size
depends largely on the size of various headers), but Python overhead brings
that to about 250B.  This means handling a million messages should consume
about 250MB of RAM - not too bad if you consider how much memory your
browser (or desktop e-mail client) eats up.  Also, who has a million
e-mails? :-)

(Caveat: Really common terms will take longer due to the size of the result
set - but searching for really common terms won't give good results anyway.)
