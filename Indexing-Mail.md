Mailpile will create and use a folder in your home directory named
`.mailpile` for its indexes and settings.

A simple test run might look like so:

    $ ./mp --add /var/spool/mail/YOURNAME --rescan all

The program prints details of its progress as it runs.  Note that just
opening the mailbox may take quite a while if it is large enough (it
takes about a bit over a minute to open my 500MB mailbox).  Once the
mailbox has been opened, my laptop (a 1.66Ghz Intel Atom with a 5400rpm
HDD) the program can index roughly four messages per second, so if you
are processing thousands of messages you should expect it to take a few
hours.

You can repeat the add command to specify multiple mailboxes.  Boxes
can be in mailbox or maildir format.  Boxes are not recursive, though.
If you have many maildirs in a tree, you must specify each one
individually.

Stopping the program with CTRL-C is (relatively) nondestructive - it
will try to save its progress and re-running should continue the scan
from where it left off.
