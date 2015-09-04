At the moment, we do not recommend exposing Mailpile directly to the wider Internet.

Putting it behind a security-mindful reverse proxy (NginX, Pound, etc...) will add an important layer of protection. That proxy then handles the SSL. A slightly more geeky solution which also provides strong security is to use SSH tunneling from your desktop to the VPS.

- [Using Apache as Proxy](#using-apache-as-proxy)
- [Using Nginx as Proxy](#using-nginx-as-proxy)
- [Using Lighttpd as Proxy](#using-lighttpd-as-proxy)
- [Serving from a Sub Directory](#serving-from-a-sub-directory)

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

### Using Nginx as Proxy

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

### Using Lighttpd as Proxy

In lighttpd 1.4 add this to your lighttpd.conf:

```
# either this or add mod_proxy to your existing modules list
server.modules =~ (
        "mod_proxy",
)

# this will redirect www.your.domain.org/email to 127.0.0.1:33411, where mailpile listens
$HTTP["url"] =~ "(^/email/)" {                                                                          
             proxy.server  = ( "" => ("" => ( "host" => "127.0.0.1", "port" => 33411 )))                
             }     
```

For lighttpd 1.5 the [oficial documentation](http://redmine.lighttpd.net/projects/1/wiki/Docs_ModProxyCore)
says this should work:

```
# either this or add mod_proxy to your existing modules list
server.modules =~ (
        "mod_proxy_core",
)

# this will redirect www.your.domain.org/email to 127.0.0.1:33411, where mailpile listens
$HTTP["url"] =~ "^/email" {
  proxy-core.rewrite-request = (
    "_uri" => ( "^/email/?(.*)" => "/$1" ),
    "Host" => ( ".*" => "127.0.0.1" ),
    "port" => '33411
  )
}
``` 
**But:** This has not been teste and need to be revised by another nice community member. ;)

### Serving from a Sub Directory

If you want to serve Mailpile from a subdirectory `your-domain.com/email` you need to specify the path in your proxy server's configuration file, but you also need to tell Mailpile about this. 

Type the command `mailpile> set sys.http_path = /email` into your Mailpile CLI