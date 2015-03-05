![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

Contacts are stored on disk as [RFC6350](https://tools.ietf.org/html/rfc6350)
compliant VCARDs.

Base code for Contacts is in mailpile/plugins/contacts.py


## How contacts work

Mailpile's contacts are internally built upon the
[RFC6350](https://tools.ietf.org/html/rfc6350) standard, or VCards.

Internally these data structures are used to represent a few different
types of data:

   * Profiles (Mailpile owner personalities/identities)
   * Contacts (People the owner communicates with)
   * Groups (Groups of Contacts)

Profiles and Contacts share many attributes, the most important of which
pertain to encryption (keys, preferences) as well as mapping which of a
users profiles is preferred for communicating with a given contact or
Internet domain.

At a low level, the trickiest part of contacts is synchronizing the
different data sources. The Mailpile contacts attempt to provide a
unified view of various different sources of contact information,
including:

   * User preferences (manual changes)
   * User behavior (inferred changes)
   * The user's GPG key-chain
   * A Thunderbird Mork database
   * A CardDAV server
   * The Gravatar online image database
   * ... more! ...

Mailpile's contacts make extensive use of the VCard data source and
synchronization specification for merging all of these disparate sources
of information into a single VCard.

Many of these data sources are read-only. However, for bidirectional
synchronization, it may be appropriate to update the GPG key-chain
and/or CardDAV servers to reflect certain user initiated changes.
However, in order to prevent infinite loops and unexpected behavior,
Mailpile should NEVER automatically synchronize data from one external
source to another. Only manual user-initiated changes are candidates for
syncing back.


## Data merging strategy

The initial pass of data merging is taken care of by the VCard spec's
built-in features, which allow lines on a card to be associated with
different data sources and specifies an algorithm for updating the card
when the source data changes.

However, this only gets us half-way, as once the data is on the card,
Mailpile frequently has to make a choice about which of multiple e-mail
addresses, names or encryption keys to use.

In general, we want Mailpile's contacts to have the following
characteristics:

* User preferences (manual settings) override all others
   * In particular, a user can edit, suppress or "delete" data,
     without it reverting/reappearing on sync.
* When a user has not expressed a preference, updates from
  remote sources should result in updates to the data present
  on the card.


## Current status

Mailpile's current VCard/Contact implementation is incomplete, in that
it lacks the following:

* Deleting/suppressing imported data is impossible
* Existing importers are flawed in that they show only the importer
  as a source, whereas they should list each individual source entity
  (e.g. GPG key) separately, so the user can suppress entire sources
  or simply understand where data comes from.
* Interfaces for writing data back to sources (sync) are missing.
* Interfaces for triggering and directing updates from sources (in
  particular the GPG key chain) are missing.

Impact:

* Changes to keys and settings (e.g. importing keys) are not reflected
  in the UI in a timely fashion.
* Users cannot disable or edit their contacts in a meaningful way.
* Users cannot disable/ignore a key via. the contact manager, they have
  to go all the way to the key-chain, which won't scale if we ever have
  multiple key-chains as data sources or multiple types of encryption
  active at once.


## Importers

* [[VCARD Importer]]
* [[Mork Importer]]
* [[CardDAV Importer]]
* [[GnuPG Contact Importer]]
* Google Contacts?

## Exporters

* [[VCARD Exporter]]

## Synchronization

Modulo some complexity, import + export = syncrhonization. Let's get import
working first.

## Field validators

We want to make sure there's no crud in some fields, because we expect those
fields to be actionable. Field validators may therefore be useful.

Not done:
* E-mail field
* Website field
* ...?

## IDEA: Context providers

A context provider is an import filter which, when provided with certain
information about a contact, extracts useful context about the contact to
deepen the user's understanding of what the user is doing, who they are, and
how best to contact them.

This is a slightly creepy feature, but actually fairly reasonable considering
it is primarily using public info or info that is otherwise available to the
Mailpile user in question. Heck, it might even encourage people to think a bit
more about privacy...

This is not NOT DONE in anyway yet, but here are the API's we're considering
about:

* Facebook
  * Auth: oauth2 api requests with application specific tokens (HARD)
  * Can't programaticaly get my "friends" email addresses (for east comparisons), even if they list email address in their profile.
  * Docs: https://developers.facebook.com
  * Available Data:
    * Name
    * Avatar
    * Current Location
    * Home Location
    * Website
    * Birthday
    * Schools
    * Work
    * Gender
    * Languages
* Twitter
  * Auth: oauth1 api requests with unique application specific tokens (DIFFICULT)
  * Docs: https://dev.twitter.com
  * Available Data:
    * Name
    * Avatar
    * Website
    * Location
    * Bio
* Gravatar
  * Auth: Send an MD5 hash of an email address appended to a URL endpoint (VERY EASY)
  * Available Data:
    * Name
    * Mutliple Avatars
    * Chat Accounts
    * Bio
    * Location
    * Various URLs
    * Validated social media profile URLs
  * Docs: http://en.gravatar.com/site/implement/profiles/
  * Example: http://en.gravatar.com/brennannovak.json or .vcf
* IndieWeb (Microformats)
  * Auth: None - simple HTTP requests, hot dog.
  * Available Data (varies):
    * Name
    * Avatar
    * Various URLs
    * Email
    * Phone
  * Docs: http://indiewebcamp.com/How_to_set_up_web_sign-in_on_your_own_domain
  * MicroFormats: http://microformats.org/wiki/hcard
  * Example: http://indiewebify.waterpigs.co.uk/validate-h-card/?url=https%3A%2F%2Fbrennannovak.com%2F
* Identengine
  * Auth: None
  * Docs: http://www.identengine.com/documentation/
  * Available Data:
    * Varies depending on contacts & type of query
  * Example: http://www.identengine.com/api/identities/http%3A%2F%2Ftwitter.com%2Fglennjones/ (Twitter Lookup)
  * Example: http://www.identengine.com/api/identities/glennjonesnet%40gmail.com/ (webfinger)

Due to the technical overhead and steps required for Facebook & Twitter, we are considering creating a Mailpile web service that handles making the OAuth requests and returns the data for you contacts. However, we are aware of the privacy issues of this approach which will make it unacceptable for many users. For those privacy minded users we will try to make the process as easy as possible so they the only requests will be between their Mailpile <---> Twitter / Facebook

## Hooks

* `register_contact_importer(importer)` - importer is subclass of `ContactImporter`.
* `register_contact_exporter(exporter)` - exporter is subclass of `ContactExporter`.
* `register_contact_field_validator(field, validator)` - field is string, validator is subclass of `ContactFieldValidator`.
* `register_contact_context_provider(provider)` - provider is subclass of `ContactContextProvider`.
