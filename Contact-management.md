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
A context provider is an import filter which, when provided with certain information about a contact, extracts useful context about the contact to deepen the user's understanding of what the user is doing. This is a slightly creepy feature, but actually fairly reasonable to implement as long as it's primarily using public info or info that is otherwise available to the Mailpile user in question. Heck, it might even encourage people to think a bit more about privacy...

Not done:
* Twitter
* Facebook
* ...