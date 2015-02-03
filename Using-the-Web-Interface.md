![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

Mailpile has a built-in web server and will eventually include a proper
web-based interface for searching, reading and composing e-mail.

If you want to run the web UI without the CLI interface, start the
program like this:

    $ ./mp --www --wait

The server listens on `localhost:33411` by default, meaning you cannot
access it from a different computer (for security reasons). You can change
the host and port by setting the `http_host` and `http_port` variables
(more about [internal variables](#internal-variables) below).
For example if you want to run the server to be accessible
from another computer as well, you can run Mailpile with:

    $ ./mp --www=0.0.0.0:33411 --wait

Alternately, you can change the `sys.http_host` variable to `0.0.0.0`. Setting
this variable to `disabled` disables the server entirely.
