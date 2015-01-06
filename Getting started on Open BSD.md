# Install Mailpile on OpenBSD 5.5 ![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png) ![OpenBSD](http://www.openbsd.org/art/puffy/ppuf100X91.gif)

First mention that I am a fan of OpenBSD and security. So I want a way to combine Mailpile and OpenBSD.

Well, then let's start with the packages you need to have installed.

We get as root and we move to the temporary directory

`su`

`cd /tmp`

Then we needed packages install, The command can change according to the current versions, look in the current versions of OpenBSD FTP server

`pkg_add git curl python-2.7.6p0 py-setuptools-1.1.6v0 py-lxml-2.3.2 py-libxml-2.9.1 py-libxslt-1.1.28`

`curl -L -o get-pip.py https://raw.github.com/pypa/pip/master/contrib/get-pip.py`

`python get-pip.py`

After installing these basic needs, put the following command

`STATIC_DEPS=true pip install lxml`

Create a symbolic link of python, because in OpenBSD binary is named differently in linux for example

`ln -s /usr/local/bin/python2.7 /usr/local/bin/python2`

Then we move where our mailpile be located, in my case

`cd /var/www`

And we clone our repository

`git clone -b release/alpha https://github.com/pagekite/Mailpile.git`

We move to our new directory

`cd Mailpile`

We put this command without this did magic

`pip install -r requirements.txt`

## This is ready! 

### MY EXAMPLE CONFIGURATION

`./mp`

`mailpile> setup`

`mailpile> set profiles.0.email = yourmail@domain.com`

`mailpile> set profiles.0.name = Your Real Name`

Basic: Send mail in the clear over port 25

`mailpile> set profiles.0.route = smtp://user:password@smtphost.com:25`

or

Better: Send mail using TLS over the submission port, 587

`mailpile> set profiles.0.route = smtptls://user:password@smtphost.com:587`

`./mp --set sys.http_host=0.0.0.0`

`./mp --www`

I hope you serve, I'm already researching how to use virtual domains and mailpile rid of roundcube. 

I hope we do, I want to first thank the staff for their hard work Mailpile and effort. 

Mailpile walks to be very safe, thanks to its creator and the community. 

Currently I'm already tested on my dedicated server with OpenBSD, still does not work 100% but hopefully time to refine the work.