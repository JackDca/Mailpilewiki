![Guide](https://github.com/pagekite/Mailpile/wiki/images/page-guide.png)

You can configure Mailpile through the settings menu.

![Mailpile settings dropdown](https://github.com/pagekite/Mailpile/wiki/images/Mailpile-Settings-Dropdown.png)

### Profiles

You can see your profile details from CLI with :

    profiles --lines

#### Changing your signature

Signature is handled per-profile and is **as far as now accessible only via CLI**. When no signature is set, a promotional message for Mailpile and open-source is inserted as signature.

To set a signature for your profile jdoe@example.com,

    vcards/addlines jdoe@example.com "=x-mailpile-profile-signature: this is my new signature"

You can add a blank signature if you prefer :

    vcards/addlines jdoe@example.com "=x-mailpile-profile-signature: "

### Mail Sources

### Sending Routes

### Language

From CLI, for exemple to go french :

    set prefs.language fr_FR

For now, the list of language codes can be read [from the source](https://github.com/mailpile/Mailpile/tree/master/mailpile/locale).

### Encryption Keys

### Security

# See also

* [[Manual configuration]]
* [[Technical details about configuration|Config]]