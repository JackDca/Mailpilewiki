# Accounts

(Note: Work in progress)

In order to match common user expectations, the old fragmented concepts of profiles, mail sources and mail routes will be unified under the Account concept.

The flexibility of being able to define a stand-alone mail source (mail not associated with any particular account) will be retained, but will be considered a secondary use-case and not given much visibility within the app's user interface. The ability to configure default settings for sending mail without a valid profile (Account) will on the other hand go away, and some settings which used to be global for the entire app will become per-Account preferences instead.


## What is an Account?

Conceptually, an Account is an e-mail address and all the meta-data required to make use of it:

* Name
* E-mail
* Profile picture
* Settings for sending e-mail
* Settings for receiving e-mail
* Preferences, including security & signature
* Encryption key(s)
* Communication history
* Tag(s) used to label mail associated with this account


## Implementation

Accounts are manipulated using the /profiles/ API endpoints and the primary storage for an Account is a Mailpile VCard (kind=profile). Accounts inherit basic VCard semantics, but each account SHOULD also have:

* A dedicated Tag (x-mailpile-profile-tag, MailpileCard.tag)
* Zero or more Mail Sources (x-mailpile-profile-source, MailpileCard.add_source(s), MailpileCard.sources())
* Zero or one Mail Routes (x-mailpile-profile-route, MailpileCard.route)

The data associated with these items are not currently stored on the VCard itself, but are linked using the named attributes. Other profile-specific VCard attributes are:

* Human-readable message signature (x-mailpile-profile-signature, MailpileCard.signature)
* Domains this account sends mail to (x-mailpile-profile-scope, MailpileCard.add_scope(e), MailpileCard.sends_to(email))
* Outgoing encryption and signing policy (x-mailpile-crypto-policy, MailpileCard.crypto_policy)+
* Outgoing encrypted mail format (x-mailpile-crypto-format, MailpileCard.crypto_format)+
* PGP key fingerprint (key)+

(+ The last three may be used by individual contacts as well, to specify per-recipient customizations)


## Incoming mail, Tags and Accounts

All incoming mail (and drafts) associated with a particular account will be tagged using a tag specific to that account. This tag will be an internal-use tag, not editable through normal user interfaces.

This will allow us to retain the default unified "Inbox", "Spam", "Drafts" etc, but make it easy to specialize each view to only show messages from a particular account if desired.


## User Interface

In recognition of the importance that identity and identity management has to most people, Accounts should be a central theme of any Mailpile user interface; the Beta III will by default present users with the list of configured accounts as a "Home Page", with accessible buttons for changing settings, creating new accounts and accessing the mail associated with each one.

The Tags view in the sidebar should be augmented to allow the user to choose between the default unified view, or scoped to a particular account.

This scoping should also effect which outgoing e-mail address is selected by default when composing a new message, even overriding (possibly with a warning?) the addressing information gleaned from message headers when composing a reply.
