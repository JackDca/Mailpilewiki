Social networks like Facebook & Twitter solve the problem of displaying the most recently up to date contact info, avatars, and email addresses by centralizing all of the relevant profile data each time a client requests anything. Since decentralization is one of Mailpiles core goals, creating a centralized contact syncing server is out of the question. This is also why we're not using CardDav. _Note, we will probably end up supporting CardDav with a plugin, as there is demand for it._

Email, being decentralized, makes for interesting solutions to this problem. After some discussion, we've outlined a flow that we think solves this problem in a persistent (but not spammy way) while maintaining the beautifully decentralized nature of email.

**DRAFT / WORK IN PROGRESS**

* I e-mail you, my PGP key and VCard are attached and a header says "I am a Mailpile compatible address book"
    * This could happen a) always, b) manually, or c) based on some clever heuristics
    * Automatically attach details to messages sent to new people we haven't contacted before
    * Include a checkbox in the composer to manually toggle that on or off.
    * We keep a list of the last 500 people to get sent keys/cards, and when people drop off the list they become candidates for getting things again.
* Your Mailpile sees the attachments, offers to update keychain and address book
* You say yes, and tick a little box that says "subscribe to updates" or "keep up to date automatically"
* Your Mailpile sends a specially formatted e-mail back to me, requesting a subscription to my key and contact info
* Time passes
* I update my photo and add a new address in my Mailpile
* Might want manual action "We noticed you updated your profile. Do you want to notify your subscribers of this update?"
* An updated VCard is sent to you automatically
