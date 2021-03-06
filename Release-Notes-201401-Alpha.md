![](https://www.mailpile.is/img/logo-275x200.png)

The first alpha release of Mailpile is available for download from github. This release is a **technology preview release** and is not meant for production use. Use at your own risk! See the [release blog post](https://mailpile.is/blog/2014-01-31_Alpha_Release_Shipping_Bits_and_Atoms.html) for elaboration.

### Reference platform and reporting
Our reference platform for this release is Ubuntu Linux 12.04.  Other operating systems may work, but for best results we recommend testing on the reference platform. 

If you use Mailpile with other setups, please consider letting us know how well it worked on [[Platform support/Alpha]] and [filing issues](https://github.com/pagekite/Mailpile/issues) if you run into any bugs.

### Getting the release

The code can be downloaded like so:

    git clone -b release/alpha https://github.com/pagekite/Mailpile.git

This branch may receive minor updates (bug-fixes) over the next couple of weeks, but core development will continue on the `master` branch. Please see the [[Getting Started]] page for further details.

There is also a live demo accessible on <https://www.mailpile.is/demos/>. 

### Highlights of this release

* **User interface**
   * Modern HTML5-based interface design
   * [Original typeface](https://github.com/mailpile/fonts)
   * [[Integrated user-friendly PGP support|GnuPG interface security]]
   * Alternate text and shell-based user interfaces available for power users
* **Encryption and security**
   * Support for reading and writing PGP/MIME encrypted or signed e-mail
   * The contents of incoming encrypted and signed mail is searchable
   * Contacts, configuration and meta-data are stored encrypted
   * First pass of Mailpile's [[threat model]] exists
* **Search**
   * Fast, extensible, custom search engine
   * The search index is stored using a one-way hash to protect sensitive data
* **Tags**
   * Can be used to organize indexed mail
   * Used internally to implement common e-mail metaphors such as "unread" and "inbox"
   * Messages can be tagged automatically, using static filter rules or Bayesian classifiers
* **Spam filtering**
   * [A basic Bayesian spam filter is configured by default](https://www.mailpile.is/blog/2014-01-12_A_Plan_For_Spam.html)
* **Interoperability**
   * Support for reading mail stored in mbox, Maildir, Thunderbird, Mac Mail.app and GMVault formats.
   * Support for importing contact details from the GnuPG keychain and Thunderbird (Mork)
   * Selected messages can be exported en-masse to a portable mbox file
   * Mailpile has been tested and verified to run on various versions of Debian and Ubuntu Linux, and on Mac OS X 10.9.1
* **Internationalization**
   * Message strings in Python and HTML can be translated
   * [Work has begun translating Mailpile into roughly 30 languages](https://www.transifex.com/projects/p/mailpile/)
* **Extensibility**
   * [[Plugin hooks for numerous types of interactions|Plugin API]]
   * Most core functionality is built using the plugin architecture
   * All API actions available as JSON-based API endpoints
   * Mailpile can be loaded as a Python module

### Known bugs and limitations

* **User Experience**
  * The HTML user-interface is "read only" unless Javascript is enabled
  * Auto-saving of drafts doesn't work yet
  * HTML/Javascript performance has not been optimized
* **Security**
  * The search index is not yet fully encrypted
  * Drafts, mailbox state and Bayesian rules are not stored encrypted
  * Encryption features are only available to users who have a PGP key
  * PGP keys are not created by default
* **Protocol support**
  * Native IMAP/POP3 is missing because it depends on local mailbox encryption
  * Multiple profile support is very primitive
* **Features**
  * Contact management support is very primitive
  * Tag management support in HTML UI is very primitive