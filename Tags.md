Tags are a central concept in Mailpile's design. Tagging of messages is used in various ways to denote status, context, importance, display properties and many other things.

## Tag metadata

| Field         | Values           | Default | Description                                               |
| ------------- | ---------------- | ------- | --------------------------------------------------------- |
| type          | tag, folder, group, attribute, unread, drafts, trash, spam, ham | tag | Tag type       |
| display       | priority, tag, subtag, archive, invisible  | tag     | Display context in UI           |
| display_order | _float_          | 0       | The order of tags in a list                               |
| icon          |                  | ""      | The icon to display with this tag                         |
| label         | true, false      | true    | Whether the tag has a label                               |
| label_color   | _color_          | ""      | Color the tag in a particular way                         |
| name          | _string_         | ""      | A name for the tag                                        |
| parent        | _b36int_         |         | Parent tag tid                                            |
| slug          | _string_         | ""      | A URL-friendly version of the name                        |
| tid           | _b36int_         | auto    | Automatically generated unique identifier for the tag     |
| url           | _url_            | ""      | The URL to access the tag through                         |
| search_terms  | _string_         | "in:%(slug)s" | The search query to perform to populate the tag            |
| search_order  | _string_         | ""      | Default search order                                      |
| flag_hides    | true, false      | false   | Hide tagged messages from searches?                       |
| flag_editable | true, false      | false   | Mark tagged messages as editable?                         |
| template      | _string_         | "index" | Default tag display template                              |


### type
**Valid values**: tag, folder, group, attribute, unread, drafts, trash, spam, ham

The type field explains the role of the tag. The most basic and default type is 'tag', with other types having other roles.

* tag: Non-exclusive identifier. A message can have multiple 'tag'-type tags simultaneously.
* folder: Exclusive identifier. A message can not exist in two 'folder'-type tags simultaneously.
* group: Used to delineate a social group, based on senders and recipients.
* attribute: An internally meaningful behavioral characteristic that applies to the messages.
* unread: An attribute suggesting that a message is unread.
* drafts: An attribute suggesting that a message is a draft.
* trash: A non-exclusive identifier of messages to be discarded.
* spam: Junk messages.
* ham: Explicitly non-junk messages.

### display
** Valid values**: priority, tag, subtag, archive, invisible

* priority: Of high relevance to the user
* tag: A normal tag (default)
* subtag: A tag with a parent, for treelike displays.
* archive: An archive, used to store older messages but not intended to display generally.
* invisible: A tag that never displays.

### display_order
### icon
### label
### label_color
### name
### parent
### slug
### stats
There are three statistics on each tag, all automatically generated.

* all: The number of messages with this tag.
* new: The number of messages with this tag that are also tagged as unread.
* not: The number of messages in the pile that do not have this tag.

### tid
### url
### search_terms
### search_order
### flag_hides
### flag_editable
### template
