![](https://www.mailpile.is/img/logo-275x200.png)

The Beta III release of Mailpile is available for download from github, with
pre-built packages available for Windows and Mac OS X.

This release is a **technology preview release**, meant for developers, early
adopters, usability testers and our community of translators. It might also
be useful for reading and writing e-mail, you never know!

See the [release blog post](https://www.mailpile.is/blog/2015-07-20_Mailpile_Beta_III.html) for details.

**Note:** Please consult the list of known issues below before filing bugs!
Thanks!

## Getting the release

Pre-built packages:

* Live demo: <https://www.mailpile.is/demos/>
* Windows: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-III.exe)
* Mac OS X: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-III.dmg)

[The source code](https://github.com/pagekite/Mailpile):

    git clone -b release/beta https://github.com/pagekite/Mailpile.git

This branch may receive minor updates (bug-fixes) over the next couple of weeks, but core development will continue on the master branch. Please see the Getting Started page for further details.

## Highlights of this release

* A powerful e-mail client capable of reading, writing, organizing and searching large volumes of e-mail
* An attractive, intuitive web-based user interface
* Support for multiple accounts and multiple PGP keys
* Streamlined PGP key discovery
* Native Windows and Mac packages
* Native support for downloading mail using the IMAP and POP3 protocols
* We have published a [[Security Roadmap]], explaining the how, why and when of Mailpile security

Highlights since the previous Beta:

* Simplified setup process
* Much improved PGP key handling (discovery and passphrase management)
* Interactive browser for finding and configuring local mailboxes
* Tools for printing, saved searches and data extraction
* Usability fixes, bug fixes, ...

This release builds on our earlier progress:

* [[Beta II Release, January 2015|Release-Notes-201501-Beta-II]]
* [[Beta Release, September 2014|Release-Notes-201409-Beta]]
* [[Alpha II Release, July 2014|Release-Notes-201406-Alpha-II]]
* [[Alpha Release, January 2014|Release-Notes-201401-Alpha]]

## Known issues

These are the some of the more important issues we already know about, most of which we hope to fix by the 1.0 release. There is no need to report issues from this list to our bug tracker, but there should be an issue filed for each of them which interested parties can subscribe to for updates (this list will be updated with links to GitHub as we catch up on filing and organizing over the next few days).

Known issues in the Beta III release:

* **General**
   * The HTML user interface is not responsive and still rather slow
   * We lack a mechanism for tracking usage and notifying users about updates/news
   * Most translations are very incomplete due to rapid development and changes leading up to the release
   * Translations were not included in the demo and packages for this release
* **PGP and Security**   *(see the [[Security Roadmap]] for more details)*
   * Gravatar and key-server lookups are not sent over Tor, thus leaking meta-data about user communication
   * Non-ASCII characters in PGP key UIDs are not always handled correctly
   * Outgoing TLS (HTTPS, STARTTLS) connections do not validate certificates or prefer good ciphers
   * Encrypted/signed messages with attachments are incompatible with Google's end-to-end and other non-PGP/MIME capable mailers
   * PGP-encrypted attachments (non-PGP/MIME) are not handled natively
* **E-mail basics**
   * The composer address input field may lose input if focus changes
   * HTML mail is rendered as plain text, which may lose important formatting
   * Rough HTML viewing is implemented, but does not sanitize or check for malicious attachments at all
   * Messages written by the notmuch mail client may not display correctly
* **IMAP/POP3**
   * Can not yet delete from server or otherwise synchronize local changes
* **Platform integration (Mac, Windows, Linux, ...)**
   * We don't have official Linux packages yet
   * Image support (thumbnail generation) is broken on most Macs
   * It is currently only possible to run one instance of the app at a time (without manual tweaks)
   * The Windows launcher crashes after shutting down the python app (#889)
