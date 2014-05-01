PGP import commands all return four items in the result structure:

 * `results`: a statistical overview of what happened
 * `failed`: details about keys which failed to be imported
 * `updated`: details about keys which were updated (as they previously existed in the keyring)
 * `imported`: details about keys which were imported as new

## Results

Most of these statistics are not useful in most cases but may be of interest in edge cases. They are mostly included for completeness, since we get these statistics anyway from the GnuPG result.

```json
{
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
}
```

 * count: How many keys were processed
 * imported: How many keys were imported as new
 * imported_rsa: How many of the newly imported keys were RSA keys
 * no_userids: Number of keys without a User ID
 * not_imported: Number of keys specifically not imported
 * num_revoked: Number of keys that were revoked through the action
 * num_signatures: Number of new signatures seen
 * num_subkeys: Number of new subkeys seen
 * num_uids: Number of User ID's imported
 * sec_dups: Private key duplicates seen
 * sec_imported: Private keys imported
 * sec_read: Private keys processed
 * skipped_new_keys: New keys which were ignored
 * unchanged: How many keys were left unchanged

### Imported, Updated

Imported and updated contain lists of details, where each entry is like so:

```json
   {
    "details": 0, 
    "details_text": "unchanged", 
    "fingerprint": "B2216FD2779AE5B59D79743CD5DC2A79C2E4AE92"
   }
```

`details_text` contains a rough description of what happened. `fingerprint` contains the key's fingerprint.

The `details` field conforms to the details field in [GnuPG's IMPORT_OK status][1], which is an integer field comprised of these values OR'd together:

 * 0 : Not actually changed
 * 1 : Entirely new key.
 * 2 : New user IDs
 * 4 : New signatures
 * 8 : New subkeys
 * 16 : Contains private key.

## Failed

The failed structure is very similar to the Updated/Imported structure, but the `details` field is different, conforming to [GnuPG's IMPORT_PROBLEM status][1]:

 * 0 : No specific reason given.
 * 1 : Invalid Certificate.
 * 2 : Issuer Certificate missing.
 * 3 : Certificate Chain too long.
 * 4 : Error storing certificate.


 [1]: https://gitorious.org/gnupg/mainline/source/927377bc91288d121a7d8bdbb3c32d8fc728e9fb:doc/DETAILS