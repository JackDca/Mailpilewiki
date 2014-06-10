Modern social networks like Facebook & Twitter solve the problem of displaying the most recently up to date contact info, avatars, and email addresses by centralizing all of the relevant profile data each time a client requests anything from the app. One of Mailpile's core goals is decentralization, so clearly a profile sync server is out of the question. This is another reason we are not using CardDav to achieve this goal as it relies on a centralized server. _Note, we will probably end up supporting CardDav sync with a plugin as there is definitely demand for it._

Email, being beautifully decentralized, makes for interesting opportunities & solutions for this problem. The easiest solution such as: immediately sending emails that updating everyone in your address when you change anything, is not good due to the highly spammy nature. After some discussion, we have outlined a lose flow that we think solves this problem in a persistent (but not spammy way) while maintaining the beautifully decentralized nature of email.

**DRAFTS WORKS IN PROGRESS**

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
