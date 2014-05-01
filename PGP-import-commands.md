PGP import commands all return a structure like this:

```json
{
 ...
 "result": {
  "failed": [], 
  "imported": [], 
  "results": {
   "count": 2, 
   "imported": 0, 
   "imported_rsa": 0, 
   "no_userids": 0, 
   "not_imported": 0, 
   "num_revoked": 0, 
   "num_signatures": 0, 
   "num_subkeys": 0, 
   "num_uids": 0, 
   "sec_dups": 0, 
   "sec_imported": 0, 
   "sec_read": 0, 
   "skipped_new_keys": 0, 
   "unchanged": 2
  }, 
  "updated": [
   {
    "details": 0, 
    "details_text": "unchanged", 
    "fingerprint": "B2216FD2779AE5B59D79743CD5DC2A79C2E4AE92"
   }, 
   {
    "details": 0, 
    "details_text": "unchanged", 
    "fingerprint": "61B9BB290AE6A0A169AA50B8D20E8D9B5AAD7379"
   }
  ]
 }, 
...
}
```