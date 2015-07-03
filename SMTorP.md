![SMTorP Image](https://raw.github.com/wiki/pagekite/Mailpile/images/SMTorP-Wiki.png)

SMTorP is the Simple Mail Tor Protocol (a.k.a. SMTP over Tor).

The idea can be summarized as follows:

* SMTorP is e-mail, but delivered over a secure channel which protects
  the metadata as well as message contents.
* SMTorP defines an e-mail address format: addresses ending in .onion
* SMTorP servers are enhanced SMTP servers which run as Tor hidden services
* Mailpile as an MUA should ship with both an SMTorP client and server
  built-in, and be bundled with Tor (Win, Mac) or depend on Tor on Linux.

**Note:** This document is a rough draft of notes and ideas, once this
has been polished and proof of concept code written, we should draft an
RFC and get the community to weigh in.

### Proof of concept

A very rough 1st proof of concept was added to Mailpile at the Tor developer
summit hackdays (Reykjavík, Feb 20th & 21st, 2014) validating that the idea
is at least quite simple to implement. To test it yourself:

1. Install Tor and the Python socksipy or socksipychain module
2. [Configure Tor for a hidden service mapping](https://www.torproject.org/docs/tor-hidden-service)
   `something.onion:25` to `localhost:33412`
3. Enable the smtp_server plugin:

    mailpile> append sys.plugins smtp_server

4. Restart Mailpile.
5. Set the SMTP server port to the port used above:

    mailpile> set sys.smtpd.port = 33412

6. Restart Mailpile again.

You should now be able to send mail to `foo@bar.onion`, and receive
incoming on the Tor hidden service domain configured in step 2.

**WARNING:** This exposes the built-in SMTP server to the Tor network,
anyone who knows (or guesses?) your .onion address may connect to the
SMTP server and wreak havoc.  The SMTP server is currently known to be
a security hole, see https://github.com/pagekite/Mailpile/issues/532
for details and progress on this issue.


## High level goals

The primary goal of SMTorP is to provide a fully decentralized and largely
backwards-compatible upgrade path for e-mail, which aims to make it very
difficult for an adversary to know that two users have communicated. The
protocol should protect both message content and the metadata of e-mail
from spying.

#### Non-goals:

* SMTorP does not aim provide stronger security guarantees than the
  Tor network itself, which means that at the very least a sufficiently
  powerful adversary may be able to infer that two users are probably
  communicating, based on statistical analysis of network traffic at both
  endpoints (timing attacks). This is a problem inherited from Tor itself
  and is considered out of scope for SMTorP.
* Anonymous communication (allowing 2 parties to communicate securely
  without knowing each others identity) is not a primary goal for SMTorP,
  although users who take special care not to reveal personally identifying
  information may well be able to use it in this manner.


## Preventing spam

We are interested in implementing a mandatory hashcash-like proof of
work system, to make bulk unsolicited e-mail over SMTorP economically
infeasable.

Since hashcash was invented, hardware advances (primarily widespread GPU
availability) have rendered the original proposals obsolete. However,
research is being done into developing hashing algorithms which are
"SIMD-hard", "Memory-hard" or difficult in other dimensions which may
make them potential candidates.  We need to look at the relevant
literature (Wikipedia probably is probably a good place to start, if not
Ella tells us to talk to Matt Green).

We also probably need to specify an upgrade path, so broken algorithms
can be phased out and replaced with harder problems to solve. One thing
to do here, is offer multiple algorithms, but scale the difficulty on
deprecated algorithms faster than on the algos we actually like.


## SMTorP message format

The SMTorP message format is the same as SMTP e-mail (RFC822 and
successors).  PGP/MIME is recommended.


## The SMTorP server

The SMTorP server is a standard ESMTP server, exposed to the Internet
using TLS on a Tor hidden service. TLS is mandatory (probably a
minimum of version 1.2).

Open questions:

* Does SMTorP need additional protocol verbs for implementing hashcash
  or checking the state of relayed mail (when using an outgoing relay)?
* Would ever want to use STARTTLS? Why?
* Should we provide a stand-alone server implemetation, or just Mailpile
  native?
* How to manage keys and prevent MITM? TOFU? Fingerprints in addresses?


## SMTorP address format

An SMTorP address has the same format as a normal SMTP e-mail address,
with additional constraints on the domain part:

    <user>@[<tls-cert-fingerprint>.]<tor-hidden-service-hash>.onion

This syntax has the benefit of being largely backwards compatible with
existing MUAs and in line with user expectations, allowing a staged
rollout of SMTorP.

The optional(?) tls-cert-fingerprint part of the address can be used to
signal to the sending MUA what TLS credentials to expect the server to
present upon connection. This improves the security of self-signed
certificates and makes active man-in-the-middle attacks much more
difficult. The difficulty level (and security) can be tuned by adjusting
how many bits of the fingerprint are included in the e-mail address.
MUAs could be encouraged to "upgrade" the addresses stored in a user's
address book to the full length automatically on first use, the
abbreviated form would primarily be manual scenarios.

The main drawback of this address syntax is it is not very human
readable, hashes are notoriously long and hard to write. Some potential
mitigation strategies are discussed below.

#### Auto-upgrade

Users with SMTorP addresses may choose to advertise them in their
e-mail signatures or in a custom header, allowing compatible MUAs to
recognize that a more secure path is available and opportunistically
upgrading to the more secure protocol.

#### QR-codes

An SMTorP address fits in a reasonably sized QR-code, for printing
on business cards.

#### mailto: URLs

The format of SMTorP addresses is a subset of normal SMTP e-mail
addresses and the standard mailto: URL spec can be used, although
there is risk of poor user experience when legacy e-mail clients
attempt to send mail to an SMTorP address (see deployment strategy
below).

#### DNS-based address discovery

DNS TXT records could be used to map individual addresses, or entire
domains to SMTorP addresses. The pros and cons of this approach should
be explored further.

## Deployment strategy

Ideally, SMTorP will be natively supported by MUAs, and Mailpile will
provide the initial proof-of-concept.

However, since SMTorP inherits most of its characteristics from legacy
e-mail it should be easy to implement plugins or proxies which add
SMTorP support to legacy infrastructure.

Any traditional e-mail provider or in-house mail server should be able
to upgrade their SMTP submission servers to support SMTorP addresses in
addition to legacy e-mail, if they so desire, merely by installing Tor
and configuring the SMTP server to use a different policy for delivery
of .onion addresses.  One partial implementation for exim was discussed
[by Johannes Berg](http://johannes.sipsolutions.net/Projects/exim-tor-hidden-mail).


## Peer-to-peer SMTorP or relay-based SMTorP

Whether SMTorP should always be purely peer-to-peer or whether it should
allow for intermediate relays (analogous to fallback MX servers in the
SMTP world) is an open question. There are two primary relay strategies
available:

* Sending relays (an SMTP submission server)
* Receiving relays (an SMTP MX server)

SMTorP could support either or both, but as the system is expected to
operate independently of DNS, the question remains how to encode a
fallback strategy into the addresses themselves without everything
becoming illegible (always delivering through a relay is just asking
for MITM attacks, we prefer direct delivery whenever possible).

[TODO: Write more. Bjarni doesn't like relays, Ella contends they are
necessary for users with intermittent networking. We met in the middle
agreeing that sending relays may be a reasonable compromise.]


## User Privacy

Running a hidden service of this nature makes it easier to track whether
a particular user is online, as long as you know their SMTorP address.

Although this is an active attack, it may be possible to check that the
hidden service is online, without the hidden service ever seeing
traffic, which makes this undetectable by the user.

If this is considered a serious problem, then it argues in favour of
running SMTorP relays.


## SMTorP and PGP/MIME

When deployed as a peer-to-peer solution, SMTorP alone provides strong
protection against network-based monitoring.

However, the security of Tor hidden services is not generally considered
to be entirely future-proof and SMTorP cannot protect the integrity of
data at rest, once the mail has been delivered. So a "belt and
suspenders" approach of using PGP/MIME for messages delivered over
SMTorP is still recommended.

The only hypothetical reason to not use PGP/MIME, would be to allow
for plausible deniability. It is our opinion that this currently has no
real world benefit.


## User interface guidelines

* When a message is being sent over SMTorP, the user should be made
  aware, e.g. by putting a lock or SMTorP logo on the send button.

* Mixing SMTorP and legacy e-mail addresses in the same e-mail should
  be prevented by the MUA, both for security reasons and to prevent
  the usability nightmare of the legacy recipients being unable to
  reply-all to everyone.


