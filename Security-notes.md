# Misc Security Notes

(These are misellaneous security notes, collected during and
around the threat modeling sessions with Eleanor Saitta.)


## Security testing

* Fuzzing: e-mail parser and httpd should be fuzzed constantly
* We should accept the Syndis offer to get audited and pentested
* We should use SSLlabs to make sure we don't forget to upgrade
  our TLS configs when new vulnerabilities are found
* Look into XSS/CSRF/... test suites, beg for free licenses from
  vendors if necessary. (Ella can give advice & leads)
* We should test the GnuPG interface code extra carefully, as
  not all versions of gpg are the same.

**Side note:** it might make sense to run the GPG tests on setup and
make sure the app isn't making promises it can't keep.  Reporting back
incompatibilities would help development, as long as we respect user
privacy.


## Downloading data from the Internet

Any data downloaded from the internt (keys, gravatars, etc.) should
be downloaded using Tor if possible, and we should make an effort to:

* Look like other common apps/browsers so we can't be targeted with
  exploits
* Change Tor identity frequently to prevent the data provider or
  exit node operator from being able to infer information about our
  mail or social graph.


## Logging

Anonymizing logs is important so users can send us bug reports
without leaking confidential data.

The Tor project has done some interesting work on privacy friendly
global logging for gathering of statistics, we should look at what
they are doing.


## Multiple personalities and GPG

Users need to understand that the multiple personalities features of
Mailpile are "soft" and if they want to be sure no data leaks from one
personality to another, they should run separate instances of the app.

Ella had some very specific scenarios where information about GPG
keys and trust levels could leak from one persona to another. She
felt this pertained most strongly to SMTorP use.


## Software updates

Use the [UW protocol](https://updateframework.com/)?

Or... bittorrent prevents us from targeting individual users!


## Securing Workflows

It can be important to make sure workflows can only be done in
the order specified. This can be done by passing variables that
link step1 to step2 so skipping step1 (or tricking someone into
skipping step1) is impossible.


## Watch out for interacting with the filesystem

Comparisons with strings in Python may differ with those in the
OS/FS layer, especially due to Unicode complicating everything.

Treating paths as byte-lists and strictly limiting which chars
are allowed (kill the unicode) can help.


## Unicode scaryness

An entire class of bugs can be eliminated if data is dumbed down
to ASCII as soon as it enters the system. This may be OK for things
like the URL path, impossible for input variable values.


## Faking type-strictness in Python (taint checking)

Wrapper classes that have funny instance variables or getters
which signify "untainted" data.  Passing a string directly or
some other data which has not been "cleaned" will trigger an
AttributeError exception.


## Libraries

This library is an anti-XSS library. Check it? 
http://code.google.com/p/reform/

OWASP anti-samy, helps make HTML safe to display.


## Viruses

Statement of intent?  How do we deal with them?  Format verification,
include ClamAV?  Recommended way would be to neutralize content into
known-safe forms by converting images to themselves, PDF rendered in
JS, etc.


## Anonymity

Support Tor for connecting to IMAP/SMTP.

Mixmaster/mixminion (later): We know the NSA can probably do
timing analysis on Tor. Mix networks, if they had traffic and
were maintained, would resist this. Clearsigned e-mails
exiting from mix nodes could provide cover traffic. Ask users
"do you wanna help make the network anonymous?". Delays
things... button to "send fast"? ..... Ella says: IMPORTANT.
Consider this as an alternate transport.


## The SSL/TLS nightmare

Backwards compatibility is bad. We want to just support new browsers
and real security if at all possible.

Optimal case:

   1. Help people buy domains
   2. Buy keys
   3. Make end-to-end TLS over pagekite easy

Make sure SPF gets configured, think about DKIM.

Others: self-signed, pagekite MITM.


