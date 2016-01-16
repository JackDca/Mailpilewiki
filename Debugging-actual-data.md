# Debugging actual data

If you happen to have issues with particular data (e.g. some emails in your inbox), you may want to reproduce issues in a development environment.

For that you can add your mails as individual files to *mailpile/tests/data/Maildir/cur/* (just copy-paste faulty mail source code in a new *.mbx* file).

You can then pop a fresh mailpile test instance. It will load all mails contained in *mailpile/tests/data*.

Be sure not to commit that files if they contain private information.