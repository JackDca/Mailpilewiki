![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

WAT? Mailpile is an amazing email client made with much love, care, and awesomeness- why would you ever want to uninstall Mailpile? Well, OK, we are still a little rough around the edges. For example, we have not even had a proper release yet.

So, if you want to delete your install we understand...

## Uninstalling manually

**IMPORTANT:** We recommend making backups of all data before deleting, in case you make a mistake and end up regretting it! Alternately, consider renaming folders and files instead of deleting them.

To uninstall Mailpile do the following:

* Delete the Mailpile app itself. Depending on how you installed, that is either:
   * The `Mailpile` folder you cloned from Github
   * The `Mailpile.app` in Applications, on a Mac
   * Use the uninstaller app created during installation on Windows

* Delete your Mailpile data folder, it lives in your home directory.
   * On *nix system that is usually `/home/username/.local/share/Mailpile`
   * On a Mac that is `/Users/username/Library/Application Support/Mailpile`
   * On a Windows XP and earlier: `C:\Documents and Settings\username\Application Data\Mailpile`
   * On a Windows Vista and later: `C:\Users\username\AppData\Roaming\Mailpile`
   * Note that early versions of Mailpile (pre-beta) stored data `$HOME/.mailpile` (the value of `$HOME` depends on your operating system), so check that location as well.

* You may need to delete your GnuPG data as well, if you have lost the passphrase to your PGP key.
   * On Linux and the Mac, that is probably a folder named `$HOME/.gnupg`
   * On a Windows XP and earlier: `C:\Documents and Settings\username\Application Data\GnuPG`
   * On a Windows Vista and later: `C:\Users\username\AppData\Roaming\GnuPG`

Be warned, deleting the Mailpile data folder will delete any mail that was sent from your Mailpile, and deleting your GPG keyring will permanently make any encrypted mail you have been sent unreadable.

So that's that. So long and thanks for all the fish!

We hope you'll stop by again and give us a try once we are a little more finished ;)