![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

Mailpile is developed and tested most heavily on Debian and Debian-based distributions, but should work fine on any modern distribution (Fedora, Mandriva, even Arch if you are so inclined). There may be [packages available for your system](https://www.mailpile.is/download/). If not, consider [[submitting packages]].

## 1. Clone the source repository

You will need `git` installed on your system. When it is up, clone the repository:

    git clone https://github.com/mailpile/Mailpile.git

This will clone the main development branch of Mailpile. If you want to clone a specific branch, specify it like so:

    git clone -b branchname https://github.com/mailpile/Mailpile.git

A [full list of existing branches is available on Github](https://github.com/mailpile/Mailpile/branches). Generally you'll want the highest version number available.

## 2. Install all the dependencies

Mailpile requires a number of different packages in order to run. Specifically:

 * GnuPG _(preferably on the 1.x branch for now, as Mailpile doesn't currently act as a GPG Agent)_
 * OpenSSL
 * Python 2.7+

Python 2.7 or newer is standard on virtually all Linux systems now. On Debian, you could install the other two packages by running

    sudo apt-get install gnupg openssl

And everything that's listed in [requirements.txt](https://github.com/mailpile/Mailpile/blob/master/requirements.txt).

If you want to use PIP (Python's package manager), you need to install these dependencies first, to enable PIP to run. For Debian...

    sudo apt-get install python-dev libxml2-dev libxslt-dev libz-dev python-pip

Then PIP can install required python components in requirements.txt

    sudo pip install -r requirements.txt

If you don't want to use PIP, you need to install these dependencies:

 * python-dev
 * libxml2-dev
 * libxslt-dev
 * libz-dev
 * python-jinja2
 * python-lxml
 * python-pgpdump
 * spambayes

On Debian, you could install these packages like this:

    sudo apt-get install python-dev libxml2-dev libxslt1-dev zlib1g-dev python-jinja2 python-lxml python-pgpdump spambayes

Under Fedora this yum command might work for installing the dependencies:

`sudo yum install gnupg openssl python-devel libxml2-devel libxslt-devel zlibrary-devel python-jinja2 python3-jinja2 python-lxml python3-lxml python-pgpdump python3-pgpdump`

If all is well, you should now be able to run Mailpile.

## 3. Start Running Mailpile

You can run Mailpile directly using the command:

    ./mp

This should drop you into the Mailpile shell in the terminal and open up a new tab or window on your default browser that you can use to configure Mailpile and start using it.

 * [[Configuring Mailpile]]
 * [[Using Mailpile's command line]]
 * [[Troubleshooting]]

If you want to configure Mailpile to launch automatically on startup, consider adding it to either your window system config (if you're running it on a personal computer for instance), or to your init scripts (if you're using Mailpile on a server).

## 4. Accesing The GUI Over Internet

At the moment, we do not recommend exposing Mailpile directly to the wider Internet.

Putting it behind a security-mindful reverse proxy (NginX, Pound, etc...) will add an important layer of protection. That proxy then handles the SSL. A slightly more geeky solution which also provides strong security is to use SSH tunneling from your desktop to the VPS.

We have not decided what our long-term solution is for this use-case. On Linux, making something like Pound a dependency is not a problem and is going to be the best overall technical solution. For desktop (win/mac) installations that people still want remote access to, we may need to bundle something ourselves.

### Using Apache as Proxy

This requires the following modules `mod_ssl` and `mod_proxy` and `mod_proxy_http` and perhaps others... you can enable these (with most apache setups) by typing `sudo a2enmod mod_ssl ...` 

Create the following site configuration file `example.com.conf` which usually exists in `/etc/apache2/sites-available/` directory.

```
<IfModule mod_ssl.c>
    <VirtualHost *:80>
        ServerName webmail.mailpile.com
        Redirect permanent / https://webmail.mailpile.com/
    </VirtualHost>

    <VirtualHost _default_:443>
        ServerName webmail.mailpile.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        SSLEngine on
        SSLCertificateFile  /etc/apache2/ssl/mailpile.crt
        SSLCertificateKeyFile /etc/apache2/ssl/mailpile.key

        ProxyPass / http://localhost:33411/

        <FilesMatch "\.(cgi|shtml|phtml|php)$">
                SSLOptions +StdEnvVars
        </FilesMatch>
        <Directory /usr/lib/cgi-bin>
                SSLOptions +StdEnvVars
        </Directory>

        BrowserMatch "MSIE [2-6]" \
                nokeepalive ssl-unclean-shutdown \
                downgrade-1.0 force-response-1.0
        BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

    </VirtualHost>
</IfModule>
```

Once you've added the above the virtual host file, enabled the site by typing `sudo a2ensite example.com` if you encounter any problems, Google + StackOverflow are handy!

### Using Nginx as a Proxy

If you want to use Mailpile at the root location of a domain, then the following configuration will get you started:

```
server {
  listen 80;
  server_name server.com;
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl;
  server_name server.com;

  # see https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
  # for notes on the good SSL on nginx
  ssl_certificate /etc/nginx/ssl/server.com.crt;
  ssl_certificate_key /etc/nginx/ssl/server.com.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
  ssl_prefer_server_ciphers   on;
  ssl_session_cache shared:SSL:10m;
  add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
  ssl_dhparam /etc/nginx/ssl/dhparam.pem;

  location / {
    access_log /var/log/nginx/mailpile_access.log;
    error_log /var/log/nginx/mailpile_error.log info;

    proxy_pass http://127.0.0.1:33411;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```


If you want to host Mailpile at a non-root location, then you will need a recent development version.  You can set what Mailpile views as root location with `mailpile> set sys.http_path = /foobar` (see [#1329](https://github.com/mailpile/Mailpile/issues/1329)). Then the following is a sample Nginx configuration: 

```
server {
  listen 80;
  server_name server.com;
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl;
  server_name server.com;

  # see https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
  # for notes on the good SSL on nginx
  ssl_certificate /etc/nginx/ssl/server.com.crt;
  ssl_certificate_key /etc/nginx/ssl/server.com.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
  ssl_prefer_server_ciphers   on;
  ssl_session_cache shared:SSL:10m;
  add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
  ssl_dhparam /etc/nginx/ssl/dhparam.pem;

  location /mailpile {
    access_log /var/log/nginx/mailpile_access.log;
    error_log /var/log/nginx/mailpile_error.log info;

    rewrite ^(.*)$ $1 break; # prevents additional rewrites of the path via proxy_pass
    proxy_pass http://127.0.0.1:33411/foobar;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```  
