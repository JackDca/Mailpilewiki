![](https://www.mailpile.is/img/logo-275x200.png)

The Beta II release of Mailpile is available for download from github, with pre-built packages availble for Windows and Mac OS X.

This release is a **technology preview release**, meant for developers, very patient early adopters, usability testers and our community of translators. It might also be useful for reading and writing e-mail, you never know!

See the [release blog post]() for details.

**Note:** Please consult the list of known issues below before filing bugs! Thanks!

## Getting the release

Pre-built packages:

* Live demo: <https://www.mailpile.is/demos/>
* Windows: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-II.exe)
* Mac OS X: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-II.dmg)

[The source code](https://github.com/pagekite/Mailpile):

    git clone -b release/beta https://github.com/pagekite/Mailpile.git

This branch may receive minor updates (bug-fixes) over the next couple of weeks, but core development will continue on the master branch. Please see the Getting Started page for further details.

## Highlights of this release

* A powerful e-mail client capable of reading, writing, organizing and searching large volumes of e-mail
* An attractive, intuitive web-based user interface
* An interactive user-friendly setup procedure
* Native Windows and Mac packages
* Native support for downloading mail using the IMAP and POP3 protocols
* New interfaces for discovering and importing PGP keys
* We have published a [[Security Roadmap]], explaining the how, why and when of Mailpile security

Highlights since the previous Beta:

* We fixed a bug in key generation:
     * instead of 4096 bit PGP keys, we were generating relatively weak 1024 bit keys for new users
* Usability studies on PGP key management and working with encrypted mail led to many UI/UX improvements
* Performance of the web UI was improved
* IMAP and POP3 support are much improved, but still a work in progress
* Usability fixes, bug fixes, ...

This release builds on our earlier progress:

* [[Beta Release, September 2014|Release-Notes-201409-Beta]]
* [[Alpha II Release, July 2014|Release-Notes-201406-Alpha-II]]
* [[Alpha Release, January 2014|Release-Notes-201401-Alpha]]

## Known issues

These are the some of the more important issues we already know about, most of which we hope to fix by the 1.0 release. There is no need to report issues from this list to our bug tracker, but there should be an issue filed for each of them which interested parties can subscribe to for updates (this list will be updated with links to GitHub as we catch up on filing and organizing over the next few days).

Known issues in the Beta II release:

* **General**
   * The HTML user interface is not responsive and still rather slow in places
   * We lack a mechanism for tracking usage and notifying users about updates/news
   * Most translations are very incomplete due to rapid development and changes leading up to the release
* **PGP and Security**   *(see the [[Security Roadmap]] for more details)*
   * Gravatar and key-server lookups are not sent over Tor, thus leaking metadata about user communication
   * There are still some bugs in generation or validation of PGP/MIME messages
   * Non-ASCII characters in PGP key UIDs are not handled correctly during setup
   * Outgoing TLS (HTTPS, STARTTLS) connections do not validate certificates or prefer good ciphers
   * Encrypted/signed messages with attachments are incompatible with Google's end-to-end and other non-PGP/MIME capable mailers
   * There is a nasty 5 minute delay on importing new PGP keys before they become actually usable [Issue #1123](https://github.com/mailpile/Mailpile/issues/1123)
* **E-mail basics**
   * HTML mail is rendered as plain text, which may lose important formatting
   * Rough HTML viewing is implemented, but does not sanitize or check for malicious attachments at all
   * Messages written by the notmuch mail client may not display correctly
   * MacMaildir (Mail.app) mail sources do not work yet
* **IMAP**
   * Does not yet recognize whether messages are "new" or already read (so all are marked as new)
   * Can not yet delete from server or otherwise synchronize local changes
   * Support for STARTTLS is missing
* **Platform integration (Mac, Windows, Linux, ...)**
   * We don't have official Linux packages yet
   * Image support (thumbnail generation) is broken on most Macs
   * It is currently only possible to run one instance of the app at a time (without manual tweaks)
   * The Windows launcher crashes after shutting down the python app (#889)


## Changes since the Alphas

A few things have changed since our Alpha releases, which may catch long-time users or testers off guard:

* A GnuPG key is required for the Beta to work and will be created if you do not already have one
* The `~/.mailpile` folder is no longer the default location for Mailpile's data, we now use platform-specific defaults: `~/.local/share/Mailpile` on Linux, `~/Library/Application Support/Mailpile/` on the Mac and a similarly appropriate location on Windows. The old folder will be used if it exists already, but new installations will not create it anymore.

Migration from a very old configuration may not work well, so if you do not have any critical data in your pile, then removing the `~/.mailpile/` folder before upgrading to the Beta is recommended.