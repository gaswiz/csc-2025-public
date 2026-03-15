# Findings

## Local auth design
- `LoginDataSource.login()` performs local credential validation with unsalted SHA-1.
- `PrepareData.PrepareCredentials()` embeds all users and their password hashes inside the APK.
- `LoginDataSource.legacyLogin()` contains a separate backdoor path with hardcoded credentials.

## Recovered credentials
- Embedded employee directory includes multiple internal user records.
- A weak SHA-1 hash from that dataset was recoverable to plaintext.
- A valid UI login was confirmed during the solve, but the exact account details are omitted publicly.

## Hidden receiver / listener path
- Relevant app actions found in the APK:
  - `com.blindsec.AUTH_SEND_MESSAGE`
  - `com.blindsec.SEND_MESSAGE`
  - `com.blindsec.SEND_PROJECT`
  - `com.blindsec.Y3Nje2JyMDRkYzRzdF9SM2NlaXYzcl8xc19mNGlsaU59`
- The hidden listener replies with:
  - `Hello`
  - `Almost there`
  - `Now increment by one more!`
  - `Craft an exploit app and send it to d151nt3gr4t0r to get the flag.`

## Decryption result
- A hardcoded resource key was present in the APK and enabled decryption of bundled conversations.
- Decrypted bundled conversations recovered from `PrepareData`.
- The final competition answer was present in those messages, but is omitted from the public repository.

## Assessment
- The intended challenge is not just login access.
- The real flag is embedded in the encrypted message dataset and recovered after reversing the receiver flow and decrypting the app’s static conversation store.
