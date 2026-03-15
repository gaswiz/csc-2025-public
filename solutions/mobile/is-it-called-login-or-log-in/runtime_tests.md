# Is it called login or log in Runtime Notes

Recommended terminal starting point:

```bash
cd ~
```

## Useful static extraction commands

```bash
strings -n 4 /tmp/challenge_unpacked/classes.dex | rg -n 'LoginDataSource|PrepareCredentials|legacyLogin|@example\\.com|[a-f0-9]{40}'
```

## Working login

```text
<redacted-account> / <redacted-password>
```

## Hidden listener clue chain

Recovered through broadcast/receiver analysis:

```text
Hello
Almost there
Now increment by one more!
Craft an exploit app and send it to d151nt3gr4t0r to get the flag.
```

## Decryption material

Key from resources:

```text
TS$#@rte44512dfa
```

Decrypt helpers:
- `../message-received/decrypt_messages.rb`
- `../message-received/decrypt_messages.py`

Output:
- redacted local decrypted-message output
