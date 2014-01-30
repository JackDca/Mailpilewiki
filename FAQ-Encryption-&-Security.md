### Is mailpile an encrypted mail service?

Not a service, but Mailpile does support strong encryption.

### What Is your PGP public key?

### Can you please include *insert-some-random-encrypted-service*.

### I was looking for private email server that can hide my email address from everyone that I haven't mailed to, does your service provide this? 

Nope. That's actually impossible. If anybody tells you they can do this, they are probably lying. Sorry!

### Is  it possible to delete message that you send to someone, but you don't  want them to keep in their account after certain period?  (If not it  should be developed)

No, that isn't possible. You can't un-tell somebody something. You can ask them nicely to delete the mail, but if they decide not to, you can't make them. Sorry. Nobody is going to be able to develop that feature in a way that is guaranteed to work, ever. This isn't James Bond.

### Can  you create a pseudonym email address that only recipient can see to  identify us and send us message to, but it does not reveal my actual  email address?

That's not really part of the intended use of Mailpile. Somebody might provide this kind of service though - it sounds useful!

### How will you prevent governments from accessing your user database?

Simple: We will not have a user database.

### How safe is it to store e-mail metadata in RAM? 

It's pretty safe. If your computer is compromised, the storage is probably compromised anyway. One attack it exposes you to that storing the metadata on the hard drive doesn't (assuming your hard drive is encrypted!) is a coldboot attack. If you need to be worried about the coldboot attack, you probably need to worry about a lot bigger things than whether Mailpile's metadata is secure anyway.

### Will it, by default, deploy using encrypted configuration data? 

Yes. Currently this is a configuration option which is turned off by default, but before our first stable release we will switch to having configuration data encrypted by default.

### Would it possible to add support for one-time passwords?

This sounds reasonable. There are a lot of two-factor authentication methods we may consider implementing, but we've not nailed anything down right now.

### How secure is the database? Is it stored on an encrypted volume? 

Yes. All configuration data, including the search index, will be encrypted by default when we get to the full release.

### How are attachments stored? Are they encrypted, as well? 

They are stored wherever your mail is stored. If that's secure, the attachments are secure. We will provide methods to secure your e-mail, although really you should be using full disk encryption!

### Will search work with encrypted files? Or, they are residing on some encrypted file system, and seem un-encrypted to it? 

Mailpile decrypts incoming e-mails, indexes them, and reencrypts them. The search index is stored encrypted, and the terms are stored as hashes. Good enough?

### Does Mailpile have no weak points when it comes to privacy/ security? How can they be closed?

Yes. E-mail metadata is hard to secure, because the standards don't really allow for keeping it all secret. We can't do much about that. Also, if the recipient of your e-mail doesn't have a publicly available encryption key, you can't send them encrypted e-mail. Sorry, that's the way public key cryptography works.

### The decrypted message is vulnerable/ visible on compromised system? Can it be obfuscated?

If your system is compromised, you have a very big problem that Mailpile can't really help you solve. We will try to do what we can, but endpoint security is very important.

### Will Mailpile work with .onion (Tor) domains? 

We plan to provide support for that.

### Will it work when residing on an in-Tor server? 

Yes!

### Will Maipile support address aliases for incoming mail? (132txche6763fhjf@domain.onion = green_leaf_dude@domain.onion) 

That's really up to the mail server to decide. Mailpile is a mail client, not a mail server. Sorry!

### Isn't e-mail forwarding a possible breach of security? 

Yes, it is. It's also unavoidable. We can't prevent people from forwarding information - even if we were to do all sorts of magic to make it impossible in Mailpile, somebody could still write down your e-mail on a postcard and send it to a friend. This is called the analog gap. The only solution is, don't send secrets in e-mail to people you don't trust.

### How will this service compare with something like Hushmail which claims to offer message en/decryption in browser as well as IMAP (if one pays).

Hushmail stores your email data on their servers. Mailpile does not have servers and we do not store users email. One can use Mailpile for free to send & receiving encrypted email through any IMAP servers.

### Is MailPile a viable non-centralized solution to issues like those faced by other privacy focused email companies like Lavabit or Silent Circle?

Yes, this is one of our core beliefes and underlying design principles.

### What happens when you are communicating with people who are still hosted by Google or a self hosted insecure server? Do they need to implement something too? 

People can continue using Gmail or their acamdemic institutions email "server" as Mailpile is just the "client" that allows for sending of encrypted messages.

### Does everyone need to be on your system (and using Mailpile) to be truly secure?

In order to send encrypted email all recipients need to have public & private PGP keys and be encrypting their messages. Many other email clients allow people to send PGP encrypted emails-  Hopefully, Mailpile is the easiest to use ;)

### You don't mention anything about secure public key distribution. Is this a problem you plan to tackle?

We plan to attach users public keys with outgoing emails. Additionally, since some instances of Mailpile will be accessible over the web, that makes for interesting new opportunities for sharing of keys.

### If  I write an e-mail from my Mailpile to a friend's Gmail, how does the encryption prevent his inbox from scaning the contents of my e-mail ?

As long as both people use encryption, even if one or both people have @gmail.com addresses, Gmail cannot read encrypted email data even if the data ends up being stored on Gmail's servers.

### Will you be storing my private key on your mail server?

No. We do not have servers and we do not store users private keys. You install our software on your computer which keeps your key nice, safe, and under your control.

### Will Mailpile function similar to Lavabit and use different passwords for IMAP connections that are not related with privatekey password used to to decrypt mails?

No. Mailpile is entirely different than Lavabit.

### Do you have plans to implement RFC6698 DANE, and move towards CA free authentication?

We are uncertain about this at the moment.

### Do the mails stay encrypted in your servers or are they stored in plaintext? 

We don't have servers, so no.

### If emails are encrypted, how do you intend to implement a searching function? 

Mailpile decrypts incoming e-mails, index them, and re-encrypts them. The search index is stored and also encrypted as well as the terms which are stored as hashes. Good enough?

### So what exactly will be encrypted and what about the metadata?

The e-mail body will be encrypted. We are trying to find ways to make most of the metadata encrypted as well, but some of it can't be because of how e-mail works.

### Is Mailpile going to send GPG encrypted email, where the mail body is encrypted, but all the headers and the metadata are not?

Yes. We are experimenting with ways to encrypt headers as well using standard GPG + SMTP protocols, but nothing concrete is implemented.

### Or are you planning to somehow also encrypt the metadata (like in Bitmessage)? Would that even be possible with email?

We're going to try and encrypt all the metadata using PGP + SMTP, but email still requires that some of the metadata be unencrypted so mail servers know where to deliver it to. We plan on integrating Bitmessage before 1.0

### Will you use the Perfect Forward Secrecy in your mail ?

We can't actually guarantee that without breaking a vital part of e-mail's functionality: people want to be able to read their old e-mails. If we implemented PFS, then nobody could ever view their archives. We consider this an antifeature, so we won't even try. Instead, we're going to just try to protect your e-mail as well as we can while it is in transit (using PGP and opportunistic SSL), and as well as we can when it is at rest (using PGP, mostly).

### Will you implement start TLS in your e-mail system?

Of course. Although we do urge all e-mail providers to provide TLS natively, because, while opportunistic encryption is better than no encryption, it's way better to support always-on encryption.

### Will you hide the IP when send an e-mail with your service?

We aren't providing a sending service. Our e-mail client can't obfuscate IP addresses - but if you route your mail through Tor or a Mixmaster, it might improve things for you.

### Does Mailpile automatically search for recipients keys or generate new keys which is then submited to the public key servers?

Yes we search & create keys. We are not sure yet about automatically submitting to keyservers.