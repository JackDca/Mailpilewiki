New to Mailpile development? Want to contribute? Thinking of writing a plugin?
Read on...


## Turning on debugging

If you're developing Mailpile, you probably want a lot more debug output than
otherwise. Simply:

`set sys.debug = [terms]`

where [terms] is a space-separated list of types of debug messages you want to
get. Options include:

* log
* http
* compose
* sendmail

## Writing plugins
* Look at the [[Plugin API]]
* Look at the [[Front-end Plugin API]]
* Look at existing plugins sketches https://github.com/mailpile/plugins

## Committing to Mailpile core

* Come hang out on our IRC channel - introduce yourself. It's easier to get
  into things if you chat with us. :-)
* If you're a backend developer, go read mailpile/app.py, mailpile/command.py
  and mailpile/ui.py at the very least. Take note of the "session" and
  "config" objects that are floating about. They're very central.
* If you're a frontend developer, familiarize yourself with the
  [[Commands]] structure, the [[Search Result Json]] and the Jinja2
  templating engine. Then visit [[Themes]]
