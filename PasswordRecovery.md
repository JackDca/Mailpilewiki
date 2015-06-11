## Abstract

NOTE: WORK IN PROGRESS!

This document proposes a protocol for decentralized passphrase and password recovery.

## Discussion

One of the most common failures in PGP key management is loss of the passphrase.

In many common configurations, a strong passphrase is used to protect the private key material: the user is asked to cleverly create an unguessable passphrase and then commit it to memory. In general a longer and more complex passphrase is considered more secure, but unfortunately such passphrases are also more easily forgotten.

This problem is a special case of the general problem of passwords and memory, but is exacerbated by the unforgiving nature of strong cryptography in general and the culture around PGP in particular:

1. PGP keys have no "password reset" mechanism
2. PGP passphrases are often considered "too important" to entrust them to keychain managers or hard copies
3. Forgetting a passphrase is equivalent to losing a key, which is equivalent to losing *all the data* that has ever been encrypted to that key - no matter how diligent the user has been about making backups.

To put it another way: the high value placed on PGP keys inevitably implies that the cost of losing a passphrase is also very high.

Although some high-risk users explicitly need their passphrase to be only ever committed to human memory, there is a significant number of users whose need to avoid data-loss greatly outweighs the potential risk recoverable passphrases might pose to confidentiality of their data.

(Aside: The PGP community has traditionally focused exclusively on the former case, which may be an overlooked factor contributing to the low adoption of PGP by the wider public - backups are important! In a parallel universe where the community recognizes this, people might organize recovery code swapping parties in place of key-signing parties...)

Tankred Hase of Whiteout has proposed [an elegant use of IMAP as a key synchronization channel](https://github.com/whiteout-io/mail-html5/wiki/Secure-OpenPGP-Key-Pair-Synchronization-via-IMAP), which Mailpile will adopt, not only to facilitate synchronization, but also to guarantee an off-site backup of the user's key material. This greatly reduces the risk of catastrophic data loss, but the problem of the passphrase getting lost or forgotten remains.

This document describes a decentralized protocol for implementing secure passphrase recovery using [Trivial Secret Sharing](https://en.wikipedia.org/wiki/Secret_sharing#Trivial_secret_sharing).


## Previous work

Mailpile's initial attempt at solving the problem was to admonish users to print out and keep safe a hard-copy of their passphrase.

Although Mailpile has so far only been used by a small number of technically skilled users, this "nagging" was almost universally ignored.  One of the most common questions from our Alpha and Beta programs was "how do I reset my passphrase?", which does not bode well for the technique if the software is adopted by a wider, less technical audience.


## Generic Protocol

Passphrase recovery can be made possible by employing a [Secret Sharing](https://en.wikipedia.org/wiki/Secret_sharing) algorithm to generate a set of N recovery codes. These recovery codes will have the property that each of them is worthless on its own, but any subset of M codes (for a fixed M, where 1 < M <= N) may be recombined to reconstruct the original passphrase.

The recovery protocol therefore has the following preparation steps:

1. Choose numbers N and M
2. Generate N recovery codes
3. Store each recovery code in a different location

Recovery of the passphrase is then accomplished by:

1. Fetching at least M recovery codes from storage
2. Recombining the recovery codes to reconstruct the lost passphrase

The key to this approach is each individual storage node does not need to be trusted, the security of the protocol can be improved even by adding untrusted nodes. It should be possible to choose a diverse enough set of nodes to thwart most attacks, including attempts by the nodes themselves to collude against the user.


## Mailpile's Proposed Implementation

Mailpile will invite the user to enable passphrase recovery once a certain threshold of app use has been passed; metrics to track include progress configuring accounts and keys, volume of e-mail downloaded or sent, number of custom tags created. In short, once it appears the user has invested time and effort into using their Mailpile, they should be prompted to take steps to protect that investment by enabling backups and key/passphrase recovery.

When recovery is enabled, the following locations will be chosen as recovery code storage nodes:

1. The folder containing the key itself (GNUPGHOME or Mailpile's data directory)
2. All configured IMAP accounts
3. The e-mail addresses of at least two frequent correspondents (the user may choose)
4. An optional hard-copy printout

Recovery codes will be constructed in such a way as to allow require an answer from all-but-one of the correspondents and all-but-one of the IMAP servers (assuming there is more than one available). With the exception of managing the optional hard-copy, distribution of recovery codes can be fully automated.

Implementation notes:

* The implementation must take care when correspondents are chosen, and attempt to avoid accounts hosted with the same providers as the user's own IMAP accounts (this can be ascertained with moderate confidence by using DNS MX-record lookups).
* Mailpile should not keep a local record of the codes themselves or which correspondents received recovery codes, but if a hard-copy is made their names and addresses will be listed there.
* Recovery codes sent by mail should be placed in attachments, so replies do not accidentally leak the codes via quoted content.

*Note: Work in progress... this is unfinished and uncoded!*


## Implementing Rationale

This section will explore in detail the practical considerations of implementing this protocol which resulted in the above strategy.

### Algorithm Choice

In this document we propose using the Trivial Secret Sharing algorithm which is based on generating random bit-strings and using the XOR operation to combine them into recovery sets.

The main downside to this approach is each recovery code will be a multiple of the size of the original passphrase. For a value of M = N-1, the size is multiplied by N, for many lower values of M the ratio gets even worse (look, a [calculator](http://www.numberempire.com/combinatorialcalculator.php)). Other secret sharing algorithms exist which offer smaller recovery codes, but the math quickly gets more complicated which adds unwelcome complexity to the implementation.

### Choosing Storage Locations

The strength (or weakness) of this protocol is largely dependent on how well the storage locations for the recovery codes are chosen:

* The fewer the locations (smaller M), the easier it is for an attacker to assemble all the recovery codes
* The more locations are chosen (larger M), the harder it may be for the user to access enough of them to perform a successful recovery
* Availability of the storage locations matters (affects M/N ratio)
* The channels used to transmit the recovery codes to and from their storage locations, should be as diverse as the locations themselves
* If storage locations are kept secret, they may be forgotten
* If storage locations are not kept secret, attacks and collusion are made easier
* Manual work during recovery is more acceptable than manual work during the preparation phase, otherwise the preparation phase might never be completed

There are a few potential storage locations considered in this document:

1. The user's brain
2. The user's own computer
3. The user's external media
4. The user's IMAP accounts
5. Hard-copy printouts
6. Friends' and colleagues' computers
7. Friends' e-mail inboxes

As a special case; the physical media where the key material itself resides should always be one of the N storage locations. The key material needs to be stored *somewhere*; not storing one of the recovery codes alongside it would lower M needlessly.

The case of losing the recovery code which is stored alongside the key itself need not be taken into account when generating recovery sets, thus reducing overhead somewhat.

#### Storage: The user's brain

As the recovery codes will be at least as long as the original passphrase (probably much longer), this is not feasible. If the user could remember the recovery code, they could remember the passphrase itself.

That said, using an extremely weak secret (a "security question") to encrypt one of the recovery codes may still have value in scenarios where not many storage locations are available.

#### Storage: The user's computer

As mentioned above, one of the recovery codes should be stored alongside the secret key material itself. In most cases, that is the user's computer. So this location is already spoken for.

#### Storage: The user's external media

USB sticks, external hard drives or other computers are reasonable storage locations, as long as the user keeps them separate from their main machine. 

Downsides: This implies manual effort during the preparation phase. Hardware malfunctions and gets lost.

#### Storage: The user's IMAP accounts

This appears to be one of the best storage locations available to most users. Many users have multiple e-mail accounts on geographically and administratively different computer systems. These servers have good availability and are professionally maintained. Preparation and recovery can be fully automated.

Spreading recovery codes over multiple IMAP servers seems like a generally good strategy.

Downsides: If a full set of M recovery codes resides on IMAP servers, it is very likely that a compromise of the device the user uses to read e-mail will grant access to all M codes at once.  Individual IMAP servers are vulnerable to coercion, insiders and technical attacks.

#### Storage: Hard-copy printouts

Most people know how to keep small, valuable pieces of paper safe. As such, hard-copy printouts are suitable storage locations for recovery codes.

A hard copy is also a suitable place to print recovery instructions, including a list of which storage nodes are in use for a given secret.

Downsides: Manual labour during preparation. Recovery may be tedious and error prone for complex recovery codes.

Note: This is a sufficiently onerous procedure for most users (making a hard copy and placing it in safe storage) that it may be better use of the effort involved to treat hard copies as full backups rather than single nodes of a shared scheme.

#### Storage: Friends' and colleagues' computers

Devices belonging to friends, relatives and colleagues share all the same benefits as the user's own devices, but improve security by adding diversity.

Downsides: Even more manual setup work and less convenience than the user's own hardware.

#### Storage: Friends' e-mail inboxes

Sending e-mail containing recovery codes to friends, relatives and colleagues shares the same security and setup benefits as the user's own IMAP account, without suffering from the same "single point of access" vulnerability.

Downsides: Recovery requires asking for help and remembering who to ask!  Choosing who to rely on may be difficult. Although automatable, is easy to make mistakes; friends must be chosen who do not use the same e-mail infrastructure, the sent messages must not be kept anywhere local (drafts, outbox, sent, ...) and the messages may be at risk of interception during transit - to name just a few potential pitfalls.



