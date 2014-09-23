![Technical documentation](https://github.com/pagekite/Mailpile/wiki/images/page-technical.png)

# Search results

*(note: Work in progress)*

Search results are the most important part of the Mailpile API and
largely define what can and can not be done in the UI.

This result format is used by all the search and view API endpoints.

The following is an example the data returned by a Mailpile search,
formatted as JSON (e.g. the output of /in/inbox/as.json) with some
unix-style comments prefixed with the `#` character.

    {
      "command": "message",  
      "elapsed": "0.268",  
      "message": "OK",  
      "status": "success"
    
      "result": {

        # For result pagination. The full results are (will be) cached in
        # RAM in the Mailpile server, so paging through duplicate results
        # should be pretty fast.
        "stats": {
          "start": 1,  
          "end": 1,  
          "total": 1
          "count": 1,  
        }
    
        # This is what was searched for.
        "search_tags": [],  
        "search_terms": [],  
    
        # These are the actual results, in the requested sort order.
        # The IDs reference details found in the "data" section below.
        # Depending on what is searched for, only some of these may
        # be present.
        "people": [  "EID",  ...  ],
        "threads": [  "MID",  ...  ],
        "messages": [  "MID",  ...  ],
    
        # This section contains actual data about tags, contacts,
        # threads and messages.  Depending on how the search function
        # is invoked, this section may be missing entirely or may only
        # contain partial data.  Most critically, the "message" section
        # generally only contains full details about a few messages,
        # that data cannot be served directly from RAM.
        "data": {
          "tag": {
            "TID": {  ...  }
          },
          "contact": {
            "EID": {... /search/address/ style details ...},
            ...
          },
          "thread": {
            "MID": [["EID",  "MID"],  ...],
          },
          "metadata"  {
            "MID": {
              "id": "yqgQdw184xFp9qPkpUfEJmxn4tc",  
              "mid": "2CLQ",  
              "urls": {
                  "thread": "/thread/=2CLQ/",
                  "message": "/thread/=2CLQ/"
                  "raw_message": "/thread/=2CLQ/"
              }
              "timestamp": 1234512345,  
              "from": {
                "name": "Lulu.com",  
                "email": "lulu@email.lulu.com",  
                "eid": "EID",
              },
              "subject": "Black  Friday.  Mastered.  Holiday  Shopping.  Done.",  
              "tag_tids": ["1",  ...  ],  
              "to_eids": ["EID",  ...  ],  
              "thread_mid": "MID",
              "body": {
                "snippet": "Black  Friday.  Mastere .... "
              },
              #  These  flags  are  based  on  interpreting  the  metadata.
              "flags": {
                "from_me": 1,
                "unread": 1,
                "trash": 1,
                "spam": 1,
                "encrypted": 1,
                "signed": 1,
                "ghost": 1,
              }
            },
          },
          "message": {
            "MID": {
              "raw_headers": [["Date",  "..."],  ...  ],
              "attachments": [...],
              "html_parts": [
                {
                  "charset": "utf-8",  
                  "data": "<div><body><div>\n<table ... ",
                  "openpgp_data": "",  
                  "openpgp_status": "",  
                  "type": "html"
                }
              ],
              "text_parts": [
                {
                  "charset": "utf-8",  
                  "data": "\n\nBlack  Friday.  Mastered.\nHoliday ...",
                  "openpgp_data": "",  
                  "openpgp_status": "",  
                  "type": "text"
                }
              ]
            },
          },
        },
      },  
    }
