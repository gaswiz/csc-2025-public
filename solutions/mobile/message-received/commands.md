# Message Received Commands

Recommended terminal starting point:

```bash
cd ~
```

The commands below can be run from any directory, but `cd ~` keeps the shell clean.

## Sample triage

```bash
file /path/to/challenge.apk
shasum -a 256 /path/to/challenge.apk
unzip -l /path/to/challenge.apk
unzip -o /path/to/challenge.apk -d /tmp/challenge_unpacked
```

## Static string extraction

```bash
strings -n 4 /tmp/challenge_unpacked/classes.dex | rg -n 'com\\.blindsec|LoginActivity|MessageFragment|Sendmessage|legacyLogin'
printf '%s' '<base64-payload>' | base64 -d
```

## Result

Decoded Base64 suffix:

```text
[redacted competition answer]
```
