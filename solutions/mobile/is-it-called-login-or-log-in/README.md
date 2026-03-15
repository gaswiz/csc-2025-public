# Is it called login or log in

## Challenge
`Is it called login or log in`

`30`

`You may have found some secret info but you cannot actually access the app right ?`

`NOTE: Use the same app from the first challenge`

## Summary
This challenge extends the same APK from `Message Received` and moves from static string triage into local credential recovery and hidden in-app data extraction.

The app stores employee credentials locally, uses unsalted SHA-1 password hashes, and also contains a hidden listener/broadcast flow. The practical solve path was:
- recover a weak password from the embedded credential store
- log into the app
- trigger and reverse the hidden receiver flow
- decrypt the bundled message dataset
- recover the flag from the decrypted conversations

## Core findings
- `LoginDataSource.login()` compares `SHA1(password)` against locally embedded hashes.
- `PrepareData.PrepareCredentials()` contains the employee directory and password hashes.
- A weak embedded credential was recoverable from the local auth dataset.
- The APK also contains a hidden listener flow and app-specific broadcast actions.
- The bundled message collection can be decrypted using a hardcoded app key, omitted here.

## Solve path
1. Decompile or string-extract the APK.
2. Identify the local auth implementation:
   - `LoginDataSource`
   - `PrepareCredentials`
   - `legacyLogin`
3. Recover a weak password from the embedded SHA-1 hash.
4. Log in with a valid employee account from the bundled dataset.
5. Reverse the hidden listener path and inspect the decrypted message store.
6. Decrypt the messages using the app key from resources.
7. Recover the final competition answer from the decrypted conversation data.

## Public note
The exact credential pair, decryption key, and recovered answer are intentionally omitted from the public repository.

## Shared artifacts
- Decrypt helpers:
  - `../message-received/decrypt_messages.rb`
  - `../message-received/decrypt_messages.py`
