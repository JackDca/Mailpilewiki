![Features](https://github.com/pagekite/Mailpile/wiki/images/page-features.png)

![Social Messaging](https://raw.github.com/wiki/pagekite/Mailpile/images/Social-Messaging.png)

We've been [creating a plugin](https://github.com/mailpile/social-archiver) that downloads social media messages from Facebook (and eventually Twitter) and formats the messages so they can be imported into Mailpile or other standards compliant email applications.

Private conversations on social media (and chat clients) are usually many short messages in close succession. This is a fundamentally different type of conversation than the bulk of email conversations (longer and further spaced in time). However, the metadata around the two conversations is nearly identical, so identical that Facebook's Conversation API spits out data that looks like email and has the attribute `email:653983917@facebook.com` to represent a participant.

### Directory & File Name

How to store personal messages downloaded to a local disk. There are a few goals this should achieve

* Gracefully scale to 10 / 100s of thousands of messages
* Segment messages intelligently so that it informs of the conversations contained therein
* Be easy to index by other software applications and services
* Be easy for a person to browse via their operating system's GUI file manager & the command line

Organization by date is the first most obvious idea

* `/2014/05/conversation-id.file`
* `/2014/05/25/conversation-id.file`

This would scale nicely for importing data from multiple different services

* `/2014/05/facebook-conversation-id.file`
* `/2014/05/twitter-conversation-id.file`

Another direction may be to organize based on conversation or contact id at the highest level, then cascade into more granular date directories.

* `/contact-id/2014/05/`
* `/facebook-contact-id/2014/05/`

One downside is, this is less intuitive to normal people browsing their filesystem and seeing long strings of numbers compared to the date hierarchy. However, many of these social sites have usernames of the person(s) in a thread, which are more human

* `/rickjames/2014/05/conversation-id.file`
* `/rickjames/2014/05/facebook-conversation-id.file`
* `/rickjames-salliejoe-1202033566/facebook-conversation-id.file (third segment is a user with no username)`


### The "From" Value

The data that Facebook spits out for conversation from id looks like `653983917@facebook.com` Twitter IDs do not resemble this structure at all, but can be forced to do so. Jabber addresses look like email addresses. All that is needed for nice standards compliant recording of these values is to prepend the Name and < > around the address.

* `Brennan Novak <653983917@facebook.com>`
* `Brennan Novak <17958179@twitter.com>`
* `Brennan Novak <bnvk@jabber.ccc.de>`


### UNIX Mbox Format Messages

Conversation threads are segmented by data or number of messages into separate files that follow the UNIX Mbox format for storing emails.

* Each email message is a file that contains a segmented Facebook conversation thread
* Media are embedded as base64 encoded email attachments, they are also saved to disk as normal files 
* The Plain text part of the email has a simple chat style conversation that should degrade nicely to older clients
* The HTML part of the email contains [Microformats](http://microformats.org) data that can be extracted with a Microformats parser that maintains data integrity.
    * This will allow random clients that don't follow mime standards perfectly to still display all the data perfectly
    * This keeps scale & load of single sentence messages in high volumes in a meaningful way

**DRAFT / WORK IN PROGRESS**

```
From social-archiver
To: Martin Luther King Jr. <mlkjr@facebook.com>
From: Malcom X <mx@facebook.com>
Cc: Chelsea Manning <cmanning@facebook.com>, Edward Snowden <esnowden@facebook.com>
Subject: Conversation with Martin Luther King Jr., Malcom X, Chelsea Manning, and Edward Snowden
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary=RANDOMSTRING

--RANDOMSTRING
Content-Type: multipart/alternative; boundary=OTHERBOUNDARYSTRING
Content-Transfer-Encoding: 8bit

--OTHERBOUNDARYSTRING
Content-Type: text/plain; charset=utf-8
Content-Disposition: inline

May 25, 2014 at 3:01, Edward Snowden: 
Hi everyone. I highly suggest we stop using Facebook and move to a platform with heavy encryption!

May 25, 2014 at 3:15, Chelsea Manning:
I definitely agree. Maybe we can host a crypto party at the hacker space?

May 25, 2014 at 3:17, Malcom X:
I'm not sure what encryption or crypto parties are, but I trust you two.

May 25, 2014 at 3:21, Martin Luther King Jr.:
I'll start organizing with the community and find a location to do this at! Chelsea, what is a hackerspace?


--OTHERBOUNDARYSTRING
Content-Type: text/html; charset=utf-8
Content-Disposition: inline
    
<div class="h-entry">
  <time class="dt-published" datetime="2014-05-25T03:01:00+00:00">May 25, 2014 at 3:01</time>
  <a href="mailto:123456789@facebook.com" class="p-author h-card">
    <span class="p-name">Edward Snowden</span>
    <span class="u-uid" hidden="true">123456789</span>
    <span class="u-url" hidden="true">https://facebook.com/123456789</span>
  </a>
  <span class="e-content p-name">Hi everyone. I highly suggest we stop using Facebook and move to a platform with heavy encryption!</span>
  <span class="u-uid" hidden="true">m_mid.5d2c646</span>
  <span class="p-category" hidden="true">inbox</span>
  <span class="p-category" hidden="true">read</span>
  <span class="p-category" hidden="true">source:web</span>
</div>
<div class="h-entry">
  <time class="dt-published" datetime="2014-05-25T03:15:00+00:00">May 25, 2014 at 3:15</time>
  <a href="mailto:987654321@facebook.com" class="p-author h-card">
    <span class="p-name">Chelsea Manning</span>
    <span class="u-uid" hidden="true">987654321</span>
    <span class="u-url" hidden="true">https://facebook.com/987654321</span>
  </a>
  <span class="e-content p-name">I definitely agree. Maybe we can host a crypto party at the hacker space?</span>
  <span class="u-uid" hidden="true">m_mid.453ic628</span>
  <span class="p-category" hidden="true">inbox</span>
  <span class="p-category" hidden="true">read</span>
  <span class="p-category" hidden="true">source:web</span>  
</div> 
<div class="h-entry">
  <time class="dt-published" datetime="2014-05-25T03:17:00+00:00">May 25, 2014 at 3:17</time>
  <a href="mailto:987612345@facebook.com" class="p-author h-card">
    <span class="p-name">Chelsea Manning</span>
    <span class="u-uid" hidden="true">987612345</span>
    <span class="u-url" hidden="true">https://facebook.com/987612345</span>
  </a>
  <span class="e-content p-name">I'm not sure what encryption or crypto parties are, but I trust you two.</span>
  <span class="u-uid" hidden="true">m_mid.234w65d4</span>
  <span class="p-category" hidden="true">inbox</span>
  <span class="p-category" hidden="true">read</span>
  <span class="p-category" hidden="true">source:web</span>  
</div> 
<div class="h-entry">
  <time class="dt-published" datetime="2014-05-25T03:21:00+00:00">May 25, 2014 at 3:21</time>
  <a href="mailto:34569871@facebook.com" class="p-author h-card">
    <span class="p-name">Martin Luther King Jr.</span>
    <span class="u-uid" hidden="true">34569871</span>
    <span class="u-url" hidden="true">https://facebook.com/34569871</span>
  </a>
  <span class="e-content p-name">I'll start organizing with the community and find a location to do this at! Chelsea, what is a hackerspace?</span>
  <span class="u-uid" hidden="true">m_mid.2342413313</span>
  <span class="p-category" hidden="true">inbox</span>
  <span class="p-category" hidden="true">read</span>
  <span class="p-category" hidden="true">source:web</span>  
</div> 

--OTHERBOUNDARYSTRING--

--RANDOMSTRING
Content-Type: image/jpeg
Content-Disposition: attachment
Content-Transfer-Encoding: base64

BASE64 ENCODED IMAGE DATA

--RANDOMSTRING
Content-Type: image/jpeg
Content-Disposition: attachment
Content-Transfer-Encoding: base64

BASE64 ENCODED IMAGE DATA

--RANDOMSTRING--
```