# GnuPG interface security

(These are misellaneous security notes, collected during and
around the threat modeling sessions with Eleanor Saitta.)

## Composing and replying

When sending mail to a group of recipients, it is important to only
assume the lowest common denominator of security and make that clear
and visible to the user.  We want them to think "why isn't this
encrypted?" so they can take steps to fix that (contact the user without
a key for example, or trigger key discovery of some sort).

Replying to a message, or forwarding, should maintain security if
at all possible. We can't prevent the user from downgrading the
security but there should be clear, understandable warnings.

The current lock is good, but it might be good to also have a lock
for the header section, and some sort of indication on the send
button or elsewhere, telling us how secure the transport is.

For certain workflows, it would be useful to have a "lock security"
button, which enforces stronger security for message headers, bodies
or transports. In this mode addresses which don't fulfill the security
requirements would not be available as recipients and other behaviors
which might degrade security would be prevented.  This enables the
user to think like so: "I am about to say something sensitive, but to
avoid accidentaly fat-fingering it and leaking information, I am going
to put Mailpile in secure composition mode before I even start writing."


## Downloading keys by default?

The main risks to downloading keys, is it exposes to 3rd parties who
you are communicating with and provides remote third parties with
information about which groups of users are conversing, leaking data
about the social graph.

* Should probably be done by default unless a message signals somehow
  that it is meant to be anonymous.
* If we have a p2p channel, requesting keys over that could make sense
  for a TOFU model.
* When downloading keys, it would be best to do so via. Tor, and take
  care to randomize delays between downloads and ask Tor to create a
  new "identity" for each download.
* It can be very important to download keys to "fill in the blanks",
  if a group message is sent encrypted to multiple people, and one is
  missing keys in order to reply - replying unencrypted should probably be
  prevented (see above), and so obtaining keys is necessary. In this case
  it is important to download keys by Key ID, checking the encrypted data
  being replied to, not use the e-mail addresses as they might not match.


## Keys and trust

Things to tell the user:

   * You have not seen this key before, and have not verified it.
   * You have seen this key before, but have not verified it.
   * You have seen this key before, and have verified it.
   * This key is different from what that person presented last time.
       * Is new key signed by old key? (OK, shutup)
       * The old key was about to expire
       * The old key was revoked
       * This might be weird.


## Message part status

Things to tell the user (as data structure):

    encryption_info: {
        protocol: "",           # one of: openpgp
        status: "",             # one of: none, decrypted, missingkey, error
        description: "",        # Descriptive status text
        have_keys: [ ... ],     # List of keys it's encrypted to
        missing_keys: [ ... ],  # List of keys you don't have
    }

    signature_info: {
        protocol: "",           # one of: openpgp
        status: "",             # one of: none, invalid, expired, revoked, unknown, verified, unverified, error
        description: "",        # Descriptive status text
        name: "",               # Name of signer
        email: "",              # Address of signer
        keyinfo: ""             # Key identification (e.g. fingerprint) of signer
        timestamp: 0123457689,  # Unix timestamp of signature
    }

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


![Signature Icons](https://brennannovak.com/uploads/images/designs/Mailpile_Icons_Signatures.png)

![Encryption Icons](https://brennannovak.com/uploads/images/designs/Mailpile_Icons_Encryption.png)



## Regarding web of trust

Ella recommends against using it, because the WoT generates a
persistent and public social graph that can be mined by adversaries.

And a historic record.

And it does not work.


## Key generation

Should be automatic, using best practices, 4K RSA, expiration?
2-3 years?

Questions to a minimum: Do you have a key? Upload to keyserver?

On expiration, Mailpile could automatically generate new keys
and sign with old keys and upload. Depending on pres.

Life-stylers vs. normal humans, punt to the command-line for
people who do not like the Mailpile GPG model. Or if possible
make it a "build your own plugin" problem.

