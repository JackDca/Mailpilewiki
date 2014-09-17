# The Mailpile Security Objectives and Roadmap

## What is this?

The two primary goals of the Mailpile project are to improve the privacy and independence of its users. We have a relatively clear idea of how we hope to achieve these goals, but as the project is a large one we know we won't be able to deliver everything at once.

This document aims to outline in more detail what our security-related goals are, where we are now, where we want to be in the future, and how we get from A to B. This document will inevitably be a work in progress for quite some time!

This is a very broad and complex subject, so please accept our apologies in advance for how obtuse and technical this document is. It is our hope that this will communicate to our users and project backers how Mailpile approaches security, encourage feedback from the security community, and inform other projects in this space so they may benefit from our efforts.

(This document and strategy is informed by formal threat model work done with Eleanor Saitta, extended discussions and consultation between her and the core Mailpile team, and the collective experience of Mailpile's authors)


## Long term goals

1. Basics: It should be safe, easy and convenient to read, write, search and organize your e-mail.

2. People should be able to communicate privately. For e-mail that means:
   1. Delivery: Messages are delivered intact and in a timely fashion.
   2. Privacy: The contents of the messages have not been eavesdropped on.
   3. Integrity: Messages are authentic (not forged).
   4. Metadata Privacy: The identities of the people communicating are known only to the people involved in the conversation.

3. People should be able to store their e-mail long term, with a strong confidence that:
   1. Storage Privacy: It cannot be read by unauthorized 3rd parties.
   2. Storage Integrity: It cannot be modified by unauthorized 3rd parties.
   3. Availability: They have convenient and reliable access to the contents.


## Non-goals

1. Mailpile is not attempting to enable anonymous communication, which is communication where the participants in a conversation do not know each others' identities. Most people consider e-mail from anonymous strangers to be spam, and we have no particular interest in making it easier to send spam. This is different from point 2.4 above which states that outsiders shouldn't know who you are communicating with.
2. Mailpile is not attempting to enable deniable communication, which is the ability to send a message which you can later disavow having written. This is a non-goal, simply because potential for abuse is high, while the real-world benefit of this feature is largely theoretical.
3. Mailpile is not a research project.
4. The Mailpile project will not create new cryptographic protocols or implement cryptographic algorithms unless it is absolutely unavoidable. Using established standards and tried-and-tested implementations is preferred whenever possible.
5. Mailpile is not designed to be "Software as a Service", as given current technology, we believe SaaS to be incompatible with goals 2.2, 2.3, 2.4, 3.1 and 3.2, in addition to going against the high level goal of fostering user independence.


## Overall strategy

### Goal 1: Basics

* Implement a convenient web-based user interface for reading and writing mail.
* Implement a fast search engine and a tag-based model for sorting and categorizing mail.
* Implement a statistical (bayesian) spam filter to ensure that important messages aren't lost in a sea of junk.
   * The spam filter also serves the purpose of protecting the user from malware.
* In order to limit the impact of malware, Mailpile will not display HTML mail until sanitization, sandboxing and anonymization mechanisms are in place.
* Attachment types known to be risky should be marked as such in the user interface.
* When possible, lossless data format transformation should be applied to incoming attachments, to disarm implentation exploits (e.g. convert PDF to Postscript and back again, or JPG/PNG -> TIFF -> JPG/PNG).

### Goal 2.1: Delivery

* Mailpile should minimize the number of intermediate relays, as each one may delay, reject or lose e-mail.
* When possible, direct peer-to-peer delivery will be prefered (bypassing the legacy Internet SMTP network entirely, e.g. using SMTorP, DIME (if applicable) or other emerging protocols).
* If direct delivery is impossible, traditional delivery using established SMTP relays shall be used.
* Direct delivery from the mail client to a legacy SMTP server is not desirable, as this is known to trigger spam filters and may prevent reliable message delivery.
* Mailpile should employ appropriate queue-and-retry strategies, and clearly inform the user if a message cannot be delivered in a timely fashion.

### Goals 2.2 and 2.3: Privacy and Integrity

* Mailpile will support the OpenPGP and PGP/MIME standards for encryption and digital signatures of e-mail (see below for details).
   * These standards are flawed in that they do not protect the message headers (including the subject, from and to lines), but they are the only reasonable standard available to us at the moment.
* This end-to-end encryption should be augmented by using TLS and/or Tor for message delivery whenever possible, as that can provide at least partial protection for metadata (2.4) and information not protected by the standard OpenPGP encryption, and can compensate for situations where end-to-end encryption is infeasible. Industry best practices should be used in all cases, which means using strong ciphers supporting forward secrecy whenever possible.
* When the details of the DIME (Dark Mail) protocol are made public, it should be evaluated as an alternate or complementary strategy, as it aims to also provide Privacy, Integrity and in some cases Metadata Privacy (2.4) as well.
* Another project to integrate with is LEAP, for similar reasons.

### Goal 2.4: Metadata Privacy

* This requires anonymizing any secondary data collection performed by Mailpile, including:
   * outgoing requests to search engines
   * the Gravatar image database
   * and PGP key servers
* We plan to use Tor for this.
* Once HTML mail is allowed, it will be important to anonymize or disable entirely downloads of remote images, as those can be used for tracking and targeting.

### Goals 2.2, 2.3, 2.4: User Interface concerns

In general,  Mailpile needs to clearly indicated where messages come from and who they are being sent to, as well as any information that is available about the privacy and authenticity of the mail. A balance needs to be struck between making things clear to non-technical users (reducing cognitive load and complexity), and not obscuring or omitting critical information.

Critical for Privacy, Integrity and Metadata Integrity is implementing a user interface which helps the user avoid making common mistakes. This includes things like detecting when previously encrypted content is being forwarded over an insecure channel, explaining what is going on when a signature fails to validate, as well as communicating the basic limitations of what is and what is not encrypted/signed.

For users for whom security is of greater importance, implementing an alternate user interface mode which simply disables and disallows all insecure communication protocols would greatly lessen the cognitive load and reduce the risk of user error. This mode cannot be the default, because that would render Mailpile useless for most people and nip in the bud any long term plans to incrementally improve security - and for similar reasons we need to strive to interoperate with other encrypted e-mail solutions.

### Goals 3.1 and 3.2: Storage Privacy and Storage Integrity

These are largely a matter of using trusted hardware to store the e-mail (this means user education, packaging for consumer operating systems and collaboration with home-server projects), encrypting and checksumming data at rest, and deleting e-mail from upstream 3rd party mail servers.

The public-facing interfaces of Mailpile (if any) must also be secure against intrusions.

### Goal 3.3: Availability

This requires detecting and repairing on-disk corruption, as well as having secure backups of all data, including the public and private key material. Users need to be educated and assisted with this.

### Goals 3.x: Remote access

Storage Privacy, Storage Integrity and Availability, in today's world of smartphones and ubiquitous Internet access, also imply that many Mailpile instances will need to be reachable from the public Internet so users can interact with their mail even though they are not carrying it on their own person.

For non-technical users this means building in native support for accessing the Mailpile web interface (and REST API) as a Tor hidden service, over a relay system like PageKite, or other solutions which side-step the problems posed by IPv4 scarcity, ubiquitous firewalls and NAT (Note: The Mailpile strategy for remote access is incomplete and requires further research).


## PGP Key Strategy

When deploying OpenPGP, the "chicken and egg" problem of encryption not being usable because nobody has PGP keys, and nobody having PGP keys because encryption is never used, needs to be resolved.

Further, the existing methods for assessing the authenticity of PGP keys have traditionally depended on centralized infrastructure (key servers) and the Web of Trust. We have rejected the Web of Trust as a reasonable model due to the fact that it publicizes the social graph of the participants. In accordance to goal 2.4 (Metadata Privacy), the social graph should be kept private. Keyservers may still be useful, but in order to acheive 2.4 care must be taken to communicate anonymously with them (over Tor or other anonymizing networks) so the keyserver itself cannot build profiles of who communicates with whom.

Mailpile's PGP key strategy is as follows:

1. Generate keys for all users when Mailpile is first installed.
2. Help users make safe backups of their key material and passphrase.
3. Sign mail by default, encrypt when the user requests it or there is a reasonable expectation that the recipient will be able to read it.
4. Distribute public keys in an ad-hoc/opportunistic manner by attaching them to outgoing mail.
5. Assess key validity using a "Trust upon first use/contact" (TOFU) strategy.

To elaborate on point 3, we consider it strategic (and critical to goal 2.3, Integrity) to make availability of encryption visible by signing. However, it would be counterproductive to encrypt mail when that is likely to inconvenience users to the point that they turn the security systems off. Of particular concern are the many users who read their mail both on a desktop/laptop and on a mobile phone - even if they have a PGP enabled mail client on one device, they may not have it on the other.  Mailpile will need to build up an internal historic profile of how a contact communicates, so it can make a reasonable guess as to when it is "safe" to automatically encrypt outgoing mail.

Regarding 5: Although TOFU is not impervious to all attacks, it has the benefit of being simple to implement, entirely decentralized (and thus capable of Internet-scale), and provides strong assurance that the person you are communicating with "today" is the same person as you communicated with "yesterday". We believe this is sufficient for many common use cases and is a very clear improvement over today's norm of unencrypted and easily forged mail. Secondary user interfaces will be provided for users that need stronger security assurances than can be provided by the TOFU model.

Our strategy for handling key expiration and revocation is incomplete at this time. It is also very likely that interoperating with other projects, in particular LEAP, will offer alternate models for handling key trust and discovery. This needs to be explored in more detail.


## Roadmap

### Beta release security goals

* Basic mail client (read, write, organize, search) and spam filter
* Render HTML-only mail as readable plain text
* Standard SMTP delivery, queue-and-retry
* Experimental 1st draft of SMTorP peer-to-peer message delivery
* Basic support for PGP encryption and signatures
* Basic support for PGP key discovery and importing (key servers and searching received mail for keys).
* Assist users with backing up critical key material and their passphrase
* Encryption by default of data at rest: downloaded mail, message metadata, search index and settings
* Prevent unauthorized access to the Mailpile user interface and REST API
* Prevent accidental exposure of Mailpile to public Internet (listens only on localhost by default)
* Show as correct contact information as possible (including e-mail address) on senders, recipients, etc.

### 1.0 release security goals

* Incremental improvements (bugfixes) to all Beta security deliverables
* Fully implement TOFU key validation and ad-hoc key distribution
* Implement 1st draft of historic security profiling, to enable automatic opportunistic encryption
* Fully integrate Tor for anonymized communication with key servers (and Gravatar, and others)
* Finalize SMTorP specification
* Improve compatibility of Mailpile's OpenPGP implementation with Google's end-to-end, Mailvelope and other active e-mail crypto projects
* Implement internal API for content sanitzation
* Implement delete-from-server in Mailpile's IMAP and POP3 clients

### Post 1.0 security goals

* Implement secure deleting of mail
* Implement native support for off-site backups (e.g. upload encrypted data to an IMAP server)
* Implement content (attachment) sanitizaton
* Implement HTML mail sanitization and sandboxing
* Enable opportunistic use of SMTorP by default
* Implement "secure UI mode" which disables insecure protocols
* Implement remote access protocols, so Mailpile can be left running on secure hardware in a secure location
* Implement automated test suites which test and verify the key aspects of Mailpile's security systems, to prevent regressions

### Post 1.0 security ideas

* Implement DIME
* Implement LEAP
* PGP Smartcard support ([#926](https://github.com/pagekite/Mailpile/issues/926))

