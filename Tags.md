Tags are a central concept in Mailpile's design.

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

### display

### display_order
### icon
### label
### label_color
### name
### parent
### slug
### stats
all: 468,
new: 0,
not: 7649

### tid
### url
### search_terms
### search_order
### flag_hides
### flag_editable
### template

