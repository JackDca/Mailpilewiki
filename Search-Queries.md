Underneath the hood of Mailpile is a powerful search query engine that you can feed all sorts of custom queries to narrow or expand your search! These commands can be typed into the web GUI search box or into the command line interface (CLI). If you are typing them in the CLI they are args for the `search` command.

### Tags

| Query    | Description                                                    |
| -------- | ---------------------------------------------------------------|
| in:tag   | messages with a given tag assigned                             |
| -in:tag  | omits messages with a given tag assigned                       |

### Email Addresses

| Query                     | Description                                   |
| ------------------------- | ----------------------------------------------|
| from:name@address.com     | messages sent from a given address            |
| to:name@address.com       | messages sent to a given address              |
| contacts:name@address.com | messages either from or to address            |

### Mailboxes

| Query          | Description                                              |
| -------------- | ---------------------------------------------------------|
| mailbox:name   | messages from one or more mailboxes which match name     |
| mailbox:boxid  | messages from only the specified mailbox id              |

### Date

| Query    | Description                                                    |
| -------- | ---------------------------------------------------------------|
| dates:2011..2012        | messages between a range of years               |
| dates:2011-06..2011-07  | messages between range of years and months      |
| date:2011-06-11         | messages from exact date (can add +date values) |
| year:2001               | messages from specified year                    |
| month:8                 | messages from specified month                   |

### Various

| Query    | Description                                                    |
| -------- | ---------------------------------------------------------------|
| subject:keyword  | messages with keyword in subject only                  |
| att:jpg          | messages with attachments of file type "jpg"           |
| has:attachment   | messages with one or more attachments                  |
| has:crypto       | messages that are encrypted and/or signed              |
