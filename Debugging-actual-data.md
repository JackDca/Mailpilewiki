# Debugging actual data

If you happen to have issues with particular data (e.g. some emails in your inbox), you may want to reproduce issues in a development environment.

For that you can add your mails as individual files to *mailpile/tests/data/Maildir/cur/* (just copy-paste faulty mail source code in a new *.mbx* file).

You can then pop a fresh mailpile test instance:

    ./scripts/mailpile-test.py -i
    
It will load all mails contained in *mailpile/tests/data*. You can run the web interface typing `www` in the shell (you may have to try it twice).

Be sure not to commit that files if they contain private information.