### Is Mailpile an encrypted e-mail service?

Mailpile is not an e-mail service, it is software - an e-mail client - that supports strong encryption.

### What is your PGP public key?

Our team PGP key is downloadable from here: <https://www.mailpile.is/files/team@mailpile.is.asc>

### Can you please support *insert-some-random-encryption-scheme*

Yeah, we'll get right to that - after we get the standard, widely adopted protocols supported robustly... ;-)

### Will Mailpile be able to send messages that expire or self destruct?

No, that is impossible.

You can't un-tell somebody something. You can ask them nicely to delete the message, but if they refuse, you can't make them. Sorry! The only way to do that is to not send the message in the first place.

(OK, so maybe it's not completely impossible. If you control their computer, you can [make their computer delete things without their consent](http://www.theguardian.com/money/2012/oct/22/amazon-wipes-customers-kindle-deletes-account). But that's not very polite. And besides, Mailpile is an e-mail program, not an evil hacking tool from a James Bond film.)

### How will you prevent governments from accessing your user database?

Simple: We will not have a user database.

We do not have any server infrastructure that contains user information. Users install the Mailpile client on their own computers. We do not track people that want to use Mailpile.

### Would it possible to add support for one-time passwords?

Yes. This may not happen until after 1.0, but it is an important feature Mailpile needs to have.

### How secure is Mailpile's internal database and configuration?

Mailpile will by default encrypt all configuration data, contacts and the search index. It may also encrypt downloaded e-mail, depending on user preferences.

Encryption is either provided by the GnuPG program, or OpenSSL's AES support.

### How are attachments stored? Are they encrypted, too? 

Email attachments are stored wherever your e-mail is stored.

If that's secure, the attachments are secure. We will provide methods to secure your e-mail, although we recommend that you use full disk encryption!

### Will search work with encrypted messages?

Yes.

Mailpile will decrypt incoming e-mail on the fly and update the search index. The message is then stored to disk in its original encrypted form. The search index itself is strongly encrypted.

### What are Mailpile's weak points, security-wise? What can be done?

Mailpile is an e-mail client and interoperates with existing standards. These standards are often lacking, in particular when it comes to protecting various types of metadata, anonymity or privacy.

For example, the OpenPGP encryption standard does not encrypt message headers, so the Subject, From and To lines are sent unencrypted. Mailpile is taking part in efforts to improve these standards.

Another example is the PGP web of trust. The web of trust is meant to help verify the authenticity of a given encryption key. Unfortunately, the web of trust relies on people publishing, in public, details about who they know and who they communicate with (in the form of signatures on keys uploaded to key servers). This is a very basic privacy problem; even Facebook recognize the need to let people restrict access to their friends list! As a result, Mailpile does not encourage use of the web of trust and relies instead on a "trust on first use" scheme to discover and authenticate keys.

The most fundamental problem of course, is that you cannot send encrypted e-mail to someone who does not themselves have an encryption key and software capable of reading and writing encrypted mail. Mailpile itself is an attempt to remedy this, by making an encryption capable e-mail client that people can easily install and use.

For all the nitty-gritty details about Mailpile's security and its limitations, please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap).

### Will Mailpile work with Tor and .onion domains?

If you mean sending mail to .onion domains: probably! Check out our [SMTorP](https://github.com/mailpile/Mailpile/wiki/SMTorP) draft proposal.

If you mean accessing Mailpile's web interface as a Tor hidden service: Yes!
We plan to provide support for that.

Mailpile will also use Tor to download information anonymously from key servers and web services like Gravatar, and potentially to send or download e-mail, when that can be done without messages getting misclassified as spam or exit nodes snooping on sensitive traffic.

Check out our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for more details.

### How does Mailpile compare with something like Hushmail? Protonmail? Whiteout?

Usually, these solutions store your e-mail data on their servers.

Usually, they also provide a web interface to read and write mail, possibly using Javascript-based cryptography to support claims that they cannot read your mail since all the encryption and decryption takes place in your browser, on your computer.

This may be better than nothing, but it can also provide a false sense of security: if the same entity is responsible for storing the e-mail and providing you with encryption software, then all they need to do to read your mail is modify the code so it goes behind your back and sends them a copy of the keys. This is not a theretical problem, it [has already happened](http://www.wired.com/2007/11/encrypted-e-mai/) and if your adversary is a government you would do well to steer clear of these solutions.

Mailpile is just software. The authors of Mailpile will never have a copy of your e-mail (or encryption keys), unless you mail them to us! Furthermore, we are committed to distributing Mailpile in ways that make it hard or impossible for us target individual users for "Trojan horses".

### Does everyone need to be using Mailpile to be truly secure?

Of course! World domination, here we come!

But seriously, no. Mailpile implements public standards and you can exchange mail with any other mail client that supports those same standards. When we feel the standards fall short, we will use open processes to propose improvements and work with authors of other e-mail systems to make sure our respective users can communicate as easily and securely as possible.

### Does Mailpile solve secure public key distribution?

Mailpile will by default use an ad-hoc, opportunistic model of key distribution and discovery.

Public keys will be attached to outgoing e-mail and Mailpile will when necessary search the local mail store for key material before reaching out to key servers or other external services. The trust model will be "TOFU/C", Trust on First Use/Contact, augmented by historic analysis of observed behavior, with key servers and traditional fingerprint verification as fallback strategies.

Please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for details.

### Will you be storing my private key on your mail server?

No. We do not operate mail servers and we do not store users' private keys.

You install Mailpile on your own computer, which keeps your key nice, safe, and under your control.

### Do plan to implement RFC6698 DANE and move towards CA free authentication?

Not for version 1.0, but hopefully later!

Please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for details.

### Will you implement Perfect Forward Secrecy (PFS)?

There is no accepted standard for how to do this for e-mail itself, so that is not on our initial roadmap.

However, Mailpile will certainly prefer ciphers which offer PFS whenever making a TLS connection for sending or receiving mail (IMAP, POP3, SMTP, etc.).

Please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for details.

### Will you implement STARTTLS?

Yes.

### Will you hide the client's IP address when sending e-mail?

Perhaps.

We are considering using Tor for this sort of thing, and hopefully the community will provide plugins implementing support for MixMasters and anonymous mail.

However, we are not committed to this plan, due to concerns that it would increase the risk that legitimate mail gets classified as Spam. There is little point sending mail securely if that prevents it from being delivered at all!

Please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for details.

### Will Mailpile support sending S/MIME emails?

Yes, but not right away.

Once OpenPGP and PGP/MIME work well, both from the user experience and technical points of view, we will look into adding other protocols, with S/MIME very near the top of the list.

Please consult our [Security Roadmap](https://github.com/mailpile/Mailpile/wiki/Security-roadmap) for details.
