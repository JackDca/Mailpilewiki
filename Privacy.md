![Security](https://github.com/pagekite/Mailpile/wiki/images/page-security.png)

### Information leaks
* Beware that Mailpile currently polls Gravatar, leaking MD5 hashes of your contact's e-mail addresses. This is only really a risk if an attacker either a) makes a dictionary attack against MD5 (unlikely) or b) wants to monitor whether you know specific people by tracking your traffic for particular hashed e-mail addresses (also unlikely). This is a temporary violation for purposes of development and will be changed before the alpha release.