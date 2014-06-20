This document outlines the details of the implementation of PGP in Mailpile, including backend interactions, frontend UI specifications, and overall policy.

We have several goals for Mailpile's security model that explain the use of PGP:

 * **Opportunistic end-to-end message security**
   * A third party should not be able to read messages.
 * **Message verifiability**
   * It should be possible to verify that a message is authentic and has not been tampered with.
 * **Simple key exchange with implicit trust on first use (TOFU)**
   * As the biggest problem is not MITM attacks against keys in transit, but that almost nobody uses in encryption.
 * **Easy key discoverability in the case of missing encryption keys**
   * Because people use a lot of methods to distribute public keys.
 * **Key validation/invalidation with explicit trust/distrust settings**
   * Because we sometimes want to be sure about things.
 * **Seamless key revocation**
   * Because sometimes keys are revoked.
 * **Seamless key rollovers**
   * Because sometimes new keys are issued.
 * **Per-recipient crypto policy management**
   * Because sometimes not everybody wants to receive encrypted e-mail all the time, even if they might sometimes.
 * **Proper user diagnosis of failures, including unexpected new keys**
   * Make sure users know when things are going wrong, or might be going wrong.
 * **Easy per-message crypto-management**
   * Make sure users can choose on an e-mail by e-mail basis whether to sign, encrypt, or what else.
 * **Seamless key publication when desired**
   * To make keys more publicly available.
 * **Follow standard practices when possible and not contradicted**
   * Even though we do things slightly differently than GnuPG's standard practices, we try to do so in a way that doesn't muddle up the keychain or cause trouble for power users who are using GnuPG elsewhere in a different context.

We also have several non-goals:

 * **Deniability**
   * In practice, denying having sent e-mail does not hold up in court. This is because courts don't care so much about cryptographic proof as they care about proof beyond reasonable doubt. As do most people, in practice. We therefore make no special effort to provide deniability to messaging. If you require deniability, don't use e-mail at all.
 * **Anonymity**
   * E-mail is generally not anonymous, although it may be pseudonymous. If you wish to use e-mail anonymously, consider using [[SMTorP]] to obscure your origin and make a creative pseudonym generation plugin. Or simply use another communications protocol that is better suited to anonymity.
 * **Perfect forward secrecy**
   * Perfect forward secrecy does not practically apply to e-mail /in storage/, as people have an expectation of being able to read old e-mails, regardless of age. It is reasonable to expect forward secrecy for e-mail /in transit/, but this obligation falls to TLS. It is not a consideration of PGP, and in fact, if somebody is in a position to hijack your PGP key, they are more than likely to also have access to your e-mail storage anyway.
 * **Support for keysigning and web of trust**
   * The Web of Trust leaks information, creates a historical record, is complicated for people to understand, nigh impossible to visualise, and in practice does not work. See below.


## Overall design

There are several separate points of functionality that relate to PGP usage in Mailpile:

 * Key discovery mechanisms
 * Key importers
 * Key selection mechanism
 * Crypto policy
 * GnuPG interface
 * Signing mechanism
 * Encryption mechanism
 * Decryption mechanism
 * Verification mechanism

Most of these simply require calls to GnuPG, which handles most of the details.


## Interaction with GnuPG

All interaction with GnuPG is managed through the `GnuPG` class in `mailpile/crypto/gpgi.py`. This class forks an instance of `gpg` as specified (defaulting to `gpg` from the OS's current execution path), operating with a specified GnuPG home directory (defaulting to GnuPG's platform-specific default).  The GnuPG interface is custom built. It was in some ways inspired by py-gnupg by Frank J. Tobin, but we wrote our own because:

 * None of the Python GnuPG wrappers we found were reporting sufficiently on their status, in particular, GnuPG's status file descriptor was not being read or its output parsed by the common Python GnuPG wrappers. 
 * In order to reduce the number of "moving parts" in Mailpile, we are being somewhat picky about the inclusion of dependencies. Of course we will use dependencies when it is appropriate, staying away from "not invented here" syndrome; but if we can't find something that does what we need, and it's a simple enough thing, it's better to write our own solution than to attempt to monkeypatch a small, weak dependency. 

The GnuPG interface defaults to using GnuPG's default home and default keychain. This can be altered by setting `sys.gpg_home`.


## Key Discovery

The main risks to downloading keys, is it exposes to 3rd parties who you are communicating with and provides remote third parties with information about which groups of users are conversing, leaking data about the social graph. Despite this, there are many benefits to fetching keys if possible, and therefore it should be done by default, but only with user permission.

If we have a p2p channel, requesting keys over that could make sense for a TOFU model. When downloading keys, it would be best to do so via Tor, and take care to randomize delays between downloads and ask Tor to create a new "identity" for each download.

It can be very important to download keys to "fill in the blanks", if a group message is sent encrypted to multiple people, and one is missing keys in order to reply - replying unencrypted should probably be prevented (see above), and so obtaining keys is necessary. In this case it is important to download keys by Key ID, checking the encrypted data being replied to, not use the e-mail addresses as they might not match.

Bearing all of this in mind, Mailpile uses a multi-pronged strategy to discover PGP keys. Our goal is to fetch keys when needed from wherever we can find them. We perform key lookup on user request, but provide multiple points at which users will be able to make such requests. There is a key lookup handler which uses available lookup strategies to build a list of key candidates for a particular set of conditions. These are evaluated based on a heuristic. The user gets presented with a list of candidates to choose from, ordered by score.  

The most important key lookup strategy is keys that have been received through e-mail from the address in question. The key lookup mechanism for that does a search in Mailpile for e-mails recieved from the address with attached keys. It may have to perform some processing to determine which keys have the greatest validity, or are expired, etc.

The lookup methods are:

| Method                               | Baseline score       |
|--------------------------------------|----------------------|
| Nicknym[lm1]                         | 10                   |
| DNS PKA[lm2]                         | 9                    |
| draft-wouters-dane-openpgp-02[lm3]   | 9                    |
| Received keys in E-mail              | 3                    |
| OpenPGP header in E-mail             | 2                    |
| keybase.io                           | 1                    |
| PGP Keyservers                       | 1                    |

A key discovered through multiple lookup methods gets additive bonus, but never higher than 20. 

The reason Nicknym gets the highest score is that the Nicknym protocol guarantees uniqueness of keys, so only one key exists per user. The Nicknym server for an e-mail address is also required to be on the domain of the address, which means it is somewhat more trustworthy than, say, PGP keyservers, where there is neither uniqueness nor any explicit control authority.

DNS PKA and draft-wouters-dane-openpgp-02 both operate on the same principle, of DNS records containing references to keys. These are more easily spoofed than Nicknym servers, but are still reasonably authoritative.

Keys received by E-mail are practically speaking the core of the TOFU model we're advocating, but we'd like to err on the side of caution when only a single instance of a key has been received for a given e-mail address, specially in the case where multiple instances of an erroneous key may have been received. This should also be implemented in such a way as to ignore keys on messages which are tagged as spam.

keybase.io is somewhat like a "supercharged keyserver". Although it has much the same security properties as a PGP keyserver, the fact that keys are managed by keybase users may make it somewhat more trustworthy in practice, but since we don't know that yet.

PGP keyservers are not very trustworthy, but are quite ubiquitous and useful. We give it a low trust score here, but may give it extra points with the bonuses below.

A key gets additional heuristic points for:

| Criterea                             | Additional score     |
|--------------------------------------|----------------------|
| Being explicitly trusted by user     | 50                   |
| Being implicitly trusted by user     | 10                   |
| Each explicitly trusted signature    | 1                    |
| Contradicting key found in E-mail    | -5                   |
| Being explicitly distrusted by user  | -10000               |
| Being revoked                        | -10000               |
| Being superceded by new key          | -50                  |

These are additive rather than bounded because it simplifies the heuristic logic. This may turn out to be a wrong approach, but in particular, we'd like to make sure that even if a key has been found on Nicknym, DNS PKA, DANE and Keybase, that if it has been explicitly distrusted, it still is considered to be untrusted. We could implement a mistrust cutoff, whereby no keys with negative heuristic values get reported.

These values are arbitrarily chosen, albeit with some understanding presumed about how good each method is. It is expected that we'll have to do some testing and alterations to these.

 [lm1]: https://leap.se/en/nicknym 
 [lm2]: http://www.gushi.org/make-dns-cert/HOWTO.html  
 [lm3]: http://tools.ietf.org/html/draft-wouters-dane-openpgp-02   
 

## Key Trust

Mailpile's approach to security includes an attempt to reduce metadata leakages, and an effort to minimize operational complexity for users. As a result, Mailpile does not work on the basis of PGP's web of trust. This is because the web of trust is overly complicated and highly prone to failure in practice. It also leaks information about the social graph.

Instead, Mailpile works on the basis of Trust On First Use principle (TOFU), which means that when a new key is discovered and accepted by the user, it is stored and considered to be implicitly trusted. At any time, the user can perform actions that will revoke the key, explicitly distrust it, or explicitly trust it.

We want to be explicit about communication with the user about the state of keys which are discovered. We should report:

 * You have not seen this key before, and have not verified it.
 * You have seen this key before, but have not verified it.
 * You have seen this key before, and have verified it.
 * This key is different from what that person presented last time.
   * Is new key signed by old key? (OK, shutup)
   * The old key was about to expire
   * The old key was revoked
   * This might be weird.

Mailpile does award heuristic bonuses for trusted signatures, but that is the extent of the use of the web of trust.

When a user indicates that he wishes to encrypt a message to a recipient, an appropriate key is used for that recipient if one is known. If none is known, the key lookup mechanism is used. If an acceptable key is found (either determined automatically or through user selection), it is imported to the local keyring and used, with implicit trust granted. A user can choose to explicitly trust a key.  For purposes of calculating the heuristic value, the existance of a key in a user's keyring is equivalent to implicit trust.  Resultingly, keys have the following trust states:

 * Revoked
 * Explicitly distrusted
 * Superceded (see below)
 * Untrusted
 * Implicitly trusted
 * Explicitly trusted


## Key Revocation

Key revocation is handled in two ways: through GnuPG's own revocation mechanism, and through a supercedance mechanism built into Mailpile.

If an incoming e-mail contains a key revocation certificate, it is automatically processed.

If an incoming e-mail contains a public key, and it is signed by a previously implicitly trusted public key, the new public key is imported and the older key is considered to be superceded. The same occurs if a user confirms the replacement of a previously explicitly trusted public key. 


## Outgoing message signing, encryption, and crypto policy

When sending mail to a group of recipients, it is important to only assume the lowest common denominator of security and make that clear and visible to the user. We want them to think "why isn't this encrypted?" so they can take steps to fix that (contact the user without a key for example, or trigger key discovery of some sort).

Replying to a message, or forwarding, should maintain security if at all possible. We can't prevent the user from downgrading the security but there should be clear, understandable warnings.

The current lock is good, but it might be good to also have a lock for the header section, and some sort of indication on the send button or elsewhere, telling us how secure the transport is.

For certain workflows, it would be useful to have a "lock security" button, which enforces stronger security for message headers, bodies or transports. In this mode addresses which don't fulfill the security requirements would not be available as recipients and other behaviors which might degrade security would be prevented. This enables the user to think like so: "I am about to say something sensitive, but to avoid accidentaly fat-fingering it and leaking information, I am going to put Mailpile in secure composition mode before I even start writing."

Normally (when not in locked mode?), the crypto policy determines the default behavior. Messages are encrypted or signed according to the crypto policy. The policy types are determined per user (including the default policy of the actuve user profile) and are as follows:

 * none
 * sign
 * sign-encrypt
 * default

There is no 'encrypt' option which does not imply signing, because deniability is not a consideration that is relevant in a reality where people automatically trust all e-mails as being authentic, which therefore implies that there is no practical deniability. However, verifiability is a consideration that we care about.

If there are multiple users with policies, the crypto policies for all the users are merged like so:

      +      |  none       |  sign       | sign-encrypt |
-------------|-------------|-------------|--------------|
none         |  none       |  none       |    none      |
sign         |  none       |  sign       |    sign      |
sign-encrypt |  none       |  sign       | sign-encrypt |

This table works on the premise that we never want to upgrade the security beyond what a user is capable of receiving, and that for the case of multiple recipients the security is limited to the lowest common denominator. Furthermore, although technically we could send anyone a signed message, if they have an explicit policy rejecting that (a policy of none) there's probably a good reason for that and we should honor it.

For instance, if we are sending a message to Alice, Bob and Charlie, and their policies are like so:

    Default: sign-encrypt
    Alice: sign-encrypt
    Charlie: sign

Then we say:  sign-encrypt + sign-encrypt + sign = (sign-encrypt) + sign = sign

In this way, messages are encrypted by default if all recipients have been explicitly set to receive encrypted e-mail and have known implictly or explicitly trusted keys. Messages can be optionally encrypted if the user elects to.


## Incoming message processing

Mailpile automatically tries to decrypt incoming encrypted messages in two scenarios:

 * Indexing
 * Displaying them to the user

On both of these conditions, Mailpile automatically checks for valid signatures. This processing leads to [[signature_state]] and [[crypto_state]] structures being internally accessible to Mailpile.

There are several different states an encrypted or signed message or message part can have (see below):

 * encryption: none, decrypted, missingkey, error, mixed-*
 * signature: none, invalid, expired, revoked, unknown, verified, unverified, mixed-*

These are displayed to the user via icons on the message and appropriate message parts. In each case interacting with the icon will provide details.

## Key generation

When a user begins a setup process, Mailpile automatically detects whether there is a keyring available on the computer. If so, it uses it. If not, it generates a key automatically, using best practices.

Current best practice is considered to be:
 * 4096 bit RSA/RSA
 * Automatic expiration in 2-3 years

Mailpile periodically checks whether keys are expiring and if close (within 1-2 months), generate a new key, sign it with the old key, and proceed to distribute it.

We ask a minimum of questions on key generation: Name, e-mail address, and whether to upload to keyserver?


## Message Statuses

There are 3 places where PGP data is exposed in our data structures: the **message summary**, **message text part**, and **message metadata**

### Message Summary

There is a crypto overview for messages that pertain to the WHOLE message, this overview will ALWAYS have information about encryption and signature states and contains mixed-* states as well as the normal states

```
messages: {
    MID: {
        crypto: {
            encryption: {
                context: "",        # a number for comparing text_part crypto state 29
                have_keys: [],      # start of a key? B8127F5124462421
                missing_keys: [],   # start of a key? 8E86CED32B2BBB65
                protocol: "",       # one of: openpgp
                status: ""          # one of: none, decrypted, missingkey, error, mixed-*
            },
            signature: {
                context: "",        # a number for comparing text_part crypto state 30
                email: "",          # an address: name@email.org
                keyinfo: "",        # a fingerprint or key ID 23GF7C78D83C580EF939
                name: "",           # name of contact: Josephine Smythe
                protocol: "",       # one of: openpgp
                status: "",         # one of: none, invalid, expired, revoked, unknown, verified, unverified, mixed-*
                timestamp: ""       # a UNIX timestamp 1389280400
            }
        }
    }
}
```

### Message Text Part

Each part of text data MIGHT have information about the encryption, signature, both or neither. This ONLY contains normal PGP states and never mixed-* values.

```
messages: {
    MID: {
        text_parts: [
            {
                crypto: {
                    encryption: {
                        context: 29,
                        status: "none"
                    },
                    signature: {
                        context: 30,
                        email: "name@email.org",
                        keyinfo: "23GF7C78D83C580EF939642F230904D9190FG52B",
                        name: "Josephine Smythe",
                        protocol: "openpgp",
                        status: "mixed-unverified",
                        timestamp: 1389280400
                    }
                }
            },{
                crypto: {
                    encryption: {
                        context: 38,
                        status: "none"
                    },
                    signature: {
                        context: 39,
                        protocol: "openpgp",
                        status: "none",
                        timestamp: 1389280400
                    }
                }
            }
        ]
    }
}
```

### Message Metadata

Each message item in search results and threads also ALWAYS exposes crypo overview about both the encryption and signature of a given message. This returns the various mixed- states as well the normal states

```
metadata: {
    MID: {
        crypto: {
            encryption: "",   # one of: none, decrypted, missingkey, error, mixed-*
            signature: ""     # one of: none, invalid, expired, revoked, unknown, verified, unverified, mixed-*
        }
    }
}
```

* **none**:         no signature
* **invalid**:      the signature was invalid (bad)
* **expired**:      the signature was made with an expired key
* **revoked**:      the signature was made with a revoked key
* **unknown**:      the signature was made with an unknown key, so we can't verify it
* **verified**:     the signature was good, and came from a verified key
* **unverified**:   the signature was good, and came from a key that isn't verified
* **error**:        there was some weird error.


Possible situations:

   * Not encrypted or signed
   * Encrypted, but couldn't decrypt. No signature.
   * Encrypted, but couldn't decrypt. Invalid signature.
   * Encrypted, but couldn't decrypt. Unknown signing key.
   * Encrypted, but couldn't decrypt. Good signature.
   * Encrypted, decryption successful. No Signature.
   * Encrypted, decryption successful. Invalid signature.
   * Encrypted, decryption successful. Unknown signing key.
   * Encrypted, decryption successful. Good signature.
   * Not encrypted. No signature.
   * Not encrypted. Invalid signature.
   * Not encrypted. Unknown signing key.
   * Not encrypted. Good signature.

## Original Icons

The following icons have been designed originally for Mailpile and are open & free for anyone to use for relevant crypto UI feedback in other applications! The more applications which adopt the same (or similar) icons, the better this will be for the user experience (and understanding of encryption) as they will already have an understanding of meaning as they move from app to app.

![Signature Icons](https://brennannovak.com/uploads/images/designs/Mailpile_Icons_Signatures.png)

![Encryption Icons](https://brennannovak.com/uploads/images/designs/Mailpile_Icons_Encryption.png)


## Notes

### Regarding the Web of Trust

We recommend against using the Web of Trust (WoT), because it generates a persistent and public social graph that can be mined by adversaries. And a historic record. And it does not work.

If you disagree with us, fine. But we're not implementing support for the Web of Trust in Mailpile's core.

### Regarding choices and options

We are intentionally dropping most of GnuPG's features on the floor, because they do not matter to most people. Here we make a distinction between lifestylers vs. normal humans. Lifestyle GnuPG users can go enjoy the command-line or their favourite with-kitchen-sink GUI if they do not like the Mailpile GPG model, or alternatively develop a plugin that supports all the features you're after.

