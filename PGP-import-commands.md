PGP import commands all return four items in the result structure:

 * `results`: a statistical overview of what happened
 * `failed`: details about keys which failed to be imported
 * `updated`: details about keys which were updated (as they previously existed in the keyring)
 * `imported`: details about keys which were imported as new

## Results

```json
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
```

### Imported, Updated

Imported and updated contain lists of details, where each entry is like so:

```json
   {
    "details": 0, 
    "details_text": "unchanged", 
    "fingerprint": "B2216FD2779AE5B59D79743CD5DC2A79C2E4AE92"
   }, 
```

The `details` field conforms to the details field in [GnuPG's IMPORT_OK status][1], which is an integer field comprised of these values OR'd together:

 * 0 : Not actually changed
 * 1 : Entirely new key.
 * 2 : New user IDs
 * 4 : New signatures
 * 8 : New subkeys
 * 16 : Contains private key.

## Failed

...


 [1]: https://gitorious.org/gnupg/mainline/source/927377bc91288d121a7d8bdbb3c32d8fc728e9fb:doc/DETAILS