Contacts are stored on disk as [RFC6350](https://tools.ietf.org/html/rfc6350) compliant VCARDs.

Base code for Contacts is in mailpile/plugins/contacts.py

## Importers
* [[VCARD Importer]]
* [[Mork Importer]]
* [[CardDAV Importer]]
* [[GnuPG Contact Importer]]
* Google Contacts

## Exporters
* [[VCARD Exporter]]

## Synchronization
Modulo some complexity, import + export = syncrhonization. Let's get import working first.

## Field validators
We want to make sure there's no crud in some fields, because we expect those fields to be actionable. Field validators may therefore be useful.

Not done:
* E-mail field
* Website field
* ...?

## Context providers
A context provider is an import filter which, when provided with certain information about a contact, extracts useful context about the contact to deepen the user's understanding of what the user is doing, who they are, and how best to contact them. 

This is a slightly creepy feature, but actually fairly reasonable considering it is primarily using public info or info that is otherwise available to the Mailpile user in question. Heck, it might even encourage people to think a bit more about privacy...

This is not NOT DONE in anyway yet, but here are the API's we're considering about:

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
* IndieWeb
  * Auth: None - simple HTTP requests, hot dog!
  * Available Data (varies):
    * Name
    * Avatar
    * Various URLs
    * Email
    * Phone
  * Docs: http://indiewebcamp.com/How_to_set_up_web_sign-in_on_your_own_domain
  * MicroFormats: http://microformats.org/wiki/hcard
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