![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

Mailpile has a built-in web server and will eventually include a proper
web-based interface for searching, reading and composing e-mail.

The web interface currently has just one input field, where you can
type terms to search for.  If you start the line with a `/` character
you can use any of the normal CLI commands, including viewing tags or
reading messages (`/view 1-15`).

Maybe someday you will build a fancier UI for us. :-)

If you want to run the web UI without the CLI interface, start the
program like this:

    $ ./mp --www

The server listens on `localhost:33411` by default, meaning you cannot
access it from a different computer (for security reasons). You can change
the host and port by setting the `http_host` and `http_port` variables
(more about [internal variables](#internal-variables) below).
For example if you want to run the server to be accessible
from another computer as well, you can run Mailpile
with:

    $ ./mp --set sys.http_host=0.0.0.0

Setting `sys.http_host` to `disabled` disables the server.
