![](https://www.mailpile.is/img/logo-275x200.png)

The Beta release of Mailpile is available for download from github, with pre-built packages availble for Windows and Mac OS X.

This release is a **technology preview release**, meant for developers, very patient early adopters, usability testers and our community of translators. It might also be useful for reading and writing e-mail, you never know!

See the [release blog post](https://mailpile.is/blog/2014-09-13_Mailpile_Beta.html) for details.

**Note:** Please consult the list of known issues below before filing bugs! Thanks!

## Getting the release

Pre-built packages:

* Live demo: <https://www.mailpile.is/demos/>
* Windows: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-1.exe), [Bittorrent download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-1.exe.torrent)
* Mac OS X: [HTTPS download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-1.dmg), [Bittorrent download](https://www.mailpile.is/files/releases/Mailpile-Installer-Beta-1.dmg.torrent)

[The source code](https://github.com/pagekite/Mailpile):

    git clone -b release/beta https://github.com/pagekite/Mailpile.git

This branch may receive minor updates (bug-fixes) over the next couple of weeks, but core development will continue on the master branch. Please see the Getting Started page for further details.

## Highlights of this release

* A powerful e-mail client capable of reading, writing, organizing and searching large volumes of e-mail
* An attractive, intuitive web-based user interface
* An interactive user-friendly setup procedure
* Native Windows and Mac packages
* Native support for downloading mail using the IMAP protocol
* We have published a [[Security Roadmap]], explaining the how, why and when of Mailpile security

This release builds on our earlier progress:

* [[Alpha II Release, July 2014|Release-Notes-201406-Alpha-II]]
* [[Alpha Release, January 2014|Release-Notes-201401-Alpha]]

## Known issues

These are the some of the more important issues we already know about, most of which we hope to fix by the 1.0 release, which is tentatively planned for **December 2014**. There is no need to report issues from this list to our bug tracker, but there should be an issue filed for each of them which interested parties can subscribe to for updates (this list will be updated with links to GitHub as we catch up on filing and organizing over the next few days).

Known issues in the beta release:

* **General**
   * The HTML user interface is entirely unoptimized and can be very slow
   * We lack a mechanism for tracking usage and notifying users about updates/news
   * Most translations are very incomplete due to rapid development and changes leading up to the release
* **PGP and Security**   *(see the [[Security Roadmap]] for more details)*
   * Gravatar and key-server lookups are not sent over Tor, thus leaking metadata about user communication
   * Keys are not yet attached to outgoing mail automatically
   * There are still some bugs in generation or validation of PGP/MIME messages
   * Non-ASCII characters in PGP key UIDs are not handled correctly during setup
   * Outgoing TLS (HTTPS, STARTTLS) connections do not validate certificates or prefer good ciphers
   * Encrypted/signed messages with attachments are incompatible with Google's end-to-end and other non-PGP/MIME capable mailers
* **E-mail basics**
   * HTML mail is rendered as plain text, which may lose important formatting
   * Messages written by the notmuch mail client may not display correctly
   * MacMaildir (Mail.app) mail sources do not work yet
   * Upload feedback for non-image attachments is poor
   * It is not possible to remove an attachment from a draft using the web interface
* **IMAP**
   * There may be noticable delays between when a message is downloaded and when it appears in UI
   * Does not yet recognize whether messages are "new" or already read (so all are marked as new)
   * Can not yet delete from server or otherwise synchronize local changes
   * Non-ASCII characters in folder names are not properly decoded
   * Support for STARTTLS is missing
* **Platform integration (Mac, Windows, Linux, ...)**
   * We don't have official Linux packages yet
   * Image support (thumbnail generation) is broken on most Macs
   * It is currently only possible to run one instance of the app at a time (without manual tweaks)
   * The Windows launcher crashes after shutting down the python app (#889)
