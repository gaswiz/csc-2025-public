# Findings

## Sample
- APK: redacted from the public repository
- SHA-256: redacted from the public repository

## Commands run
```bash
file /path/to/challenge.apk
shasum -a 256 /path/to/challenge.apk
unzip -l /path/to/challenge.apk
unzip -o /path/to/challenge.apk -d /tmp/challenge_unpacked
strings -n 4 /tmp/challenge_unpacked/classes.dex | rg -n 'com\\.blindsec|LoginActivity|MessageFragment|Sendmessage|legacyLogin'
printf '%s' '<base64-payload>' | base64 -d
```

## Evidence
- App package string found in `classes.dex`: `com.blindsec.internalemployeemessenger`
- Interesting app-defined actions found in `classes.dex`:
  - `com.blindsec.AUTH_SEND_MESSAGE`
  - `com.blindsec.SEND_MESSAGE`
  - `com.blindsec.SEND_PROJECT`
  - one additional app-specific action ending in an encoded payload
- The last `com.blindsec.*` value contains Base64 after the final dot.
- Decoding that payload yields the competition answer, which is omitted from the public writeup.

## Assessment
- The intended issue is broadcast-receiver themed.
- The competition answer is statically embedded in an app-specific action name and does not require dynamic execution.

## Public note
The recovered answer is intentionally redacted from the public repository.

## Scope note
- Follow-up login and hidden message-chain findings for the second APK challenge were moved to:
  - `../is-it-called-login-or-log-in/`
