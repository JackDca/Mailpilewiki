A lot of people use Thunderbird (or Icedove) as their main mail application, and may wish to use Thunderbird as their mail importer and frontend during this phase in the development of Mailpile. There are two things Mailpile can import from Thunderbird presently: Thunderbird's mboxes, which contain mail, and its Mork databases, which are contact address books.

## Finding your profile
Your Thunderbird data resides in your Thunderbird profile directory. On Windows, that will be `\AppData\Roaming\Thunderbird\Profiles\<Profile name>\`, on MacOS you can find it in either `~/Library/Thunderbird/Profiles/<Profile name>/` or `~/Library/Application Support/Thunderbird/Profiles/<Profile name>/`, and on most other systems it will be in `~/.thunderbird/<Profile name>`.

The `<Profile name>` part is the name of the profile, since you can have multiple profiles in Thunderbird. Generally this name is something like `a1b2c3de.default` - i.e., some random characters. If you have multiple profiles, you might need to dig around a bit to find the one you want.

## Importing mail
In the profile directory, there are folders called `Mail`, which contains POP3 mail, and `ImapMail` which contains your IMAP folders. In those directories there will two files and possibly a directory for each folder represented in Thunderbird's folder list. The file with a ".msf" ending can be ignored, but the other file, with no particular ending, is a mailbox that you may wish to index.

The directory will have a ".sbd" ending, and inside it you will find any subfolders in the same format.

If you are using Linux or MacOS, you can find all of the mail directories by running:

    $ find . -name "*.msf" | sed 's:.msf::'

from the Mail or ImapMail. Remember to expand to the full path.

Once you have the directories, add them to Mailpile using the `add` command, e.g.:

    mailpile> add ~/.thunderbird/a1b2c3de.default/ImapMail/mail.example.com/INBOX

Do this once for each directory.

### Setting IMAP to download
Note that if you're using Thunderbird as an IMAP client, you need to make sure Thunderbird downloads all the mail. To do so, go to Thunderbird's settings, choose Synchronization & Storage, and hit Advanced. There, make sure all the relevant folders are checked as "Download".

## Importing contacts
Thunderbird stores your contacts in two places in the profile: a file called `abook.mab`, and one called `history.mab`. The difference here is that `abook.mab` contains the address book that you have created through your interactions in Thunderbird, whereas `history.mab` has addresses that Thunderbird has collected from your e-mail.

To add them to Mailpile, use the `prefs.vcard.importers.mork` attribute in the settings. Simplest is to go to the Mailpile command line and type, e.g.:

    mailpile> set prefs.vcard.importers.mork.0.filename = ~/.thunderbird/a1b2c3de.default/abook.mab

The 0 there is the number of the importer. If you have more than one Mork file, remember to increase that number for each one.

## Synchronizing
Once you've done all of this, just `rescan` and you'll be on your way.


