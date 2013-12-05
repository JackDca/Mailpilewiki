# Threat Model

A preliminary round of requirements threat modeling, using the [Trike
methodology](http://octotrike.org/) has been completed.  This has given
us an initial set of Security Objectives (see below).

[Eleanor Saitta](https://twitter.com/Dymaxion) has been guiding this
process (and being helpful about security stuff in general).


### How to use Security Objectives

1. During design work, the Security Objectives should be kept in mind and
reviewed before a design is finalized.  Often they will be obvious, but they
should help us avoid forgetting important things.

2. When designing and implementing the security test framework, the Security
Objectives should inform what is tested for.

3. Security Objectives can be used to rank and prioritize bug reports.

4. Security Objectives define what problems Mailpile is and is not solving, in
the security realm. This is what the user interface should communicate, both
what is protected against, and what is not.


### What happens next?

The next step of Threat Modeling is to do the Architecture Model, which has
the following benefits:

1. Help formalize what workflows within the system actually look like - in
   this case what the software does.

2. Helps identify places in the architecture where systemic violations of
   Security Objectives might occur - finding design bugs.

3. Identify and document assumed security properties of systems we rely on -
   what is out of scope, and what can break us? What risks are we accepting,
   what can and what can we not do?

4. For any workflow in the system, the Architecture Model identifies what can
   go wrong at any given step. During development, this lets us have a list of
   what checks need to be performed and what invariants need to be maintained.
   During test, that gives us a complete list of which things need to be
   tested for. Helps interpret and triage found bugs and evaluate their actual
   impact and priority.

5. Helps write documentation which explains security trade-offs.


### When?

The Architecture Model is pretty involved and Mailpile is currently not ready
to start this work, things are still very much in flux and we are still
deciding which features to provide in the first release - our requirements
have not stabalized. However, the Architecture Model is there to help identify
design flaws and from that perspective, the sooner work begins, the better.

So we will revisit this and begin work after the Alpha release.

Architecture Model work can happen in stages and we do expect that to be the
case for Mailpile.


# Our 1st Threat Model

## 1st Pass, Inputs

In the first round, we identified the following things:

* Actors - People and robots
   * Owner
   * Mail Sender
   * Mail Recipient
   * Mail Admin
   * Internet Mailserver
   * Anonymous
* Assets - things we care about
   * Message Content
   * Pile
   * Contact
   * Group
   * Workflow
   * Message Identity
* Who can acton what, when? (matrix)
* Threats, ranked by severity and classified by type
* Security objectives


## 1st Pass, Security Objectives

The Security Objectives are the result of evaluating what the Actors
should or should not be able to do with our Assets.

### Elevation of Privilege around message integrity and confidentiality

**SO1.1:** Mailpile shall not allow Anonymous, Sender, Mail Admin or
Recipients to read, update or delete Message Content or Message Identity. If
such an attacker tries, Mailpile shall thwart the attack.

### Denial of Service around reading and sending mail

**SO2.1:** Mailpile shall not allow Anonymous, Sender, or Mail Admin to prevent
Owner from reading Message Content. If such an attacker tries, Mailpile shall
thwart the attack.

**SO2.2:** Mailpile shall not allow Anonymous, Sender, Mail Admin, or
Recipient to prevent Owner from creating Message Content and Identity. If such
an attacker tries, Mailpile shall thwart the attack.

**SO2.3:** Mailpile shall not allow Anonymous, Sender, Mail Admin, or Recipient
to prevent OWner from executing a Workflow. If such an attacker tries,
Mailpile shall thwart the attack.

**SO2.4:** Mailpile shall not allow Anonymous, Sender, Mail Admin, or Recipient
to prevent Owner from executing a Workflow. If such an attacker tries,
Mailpile shall thwart the attack.

### Mail forgery and prevention of forgery detection

**SO3.1:** Mailpile shall not allow Anonymous, Sender, or Mail Admin to update
a contact or group. If such an attacker tries, Mailpile shall thwart the
attack.

**SO3.2:** Mailpile shal not allow Anonbymous, Sender, or Mail Admin to create
Message Content with an incorrect MEssae Identity. If such an attacker tries,
Mailpile shall thwart the attack.

**SO3.3:** Mailpile shall not allow Anonymous, Sender, or Mail Admin to
prevent Owner from reading a Contact or Group. If such an attacker tries,
Mailpile shall thwart the attack.

### Spamming

**SO4.1:** Mailpile shall not allow Owner, Sender, Mail Admin, or Anonymous to
prevent others from creating, reading, updating, or deleting other
applications data in Internet Mailservers. If such an attacker tries, Mailpile
shall rate limit the attack.

