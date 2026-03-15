# Message Received

## Challenge
`Message Received`

`20`

`Following last year's insident on MC Teoms we dicided to create our own super secure internal employee messaging application. As a penetration tester you are tasked with testing the security of our application. We have hidden some easter eggs or flags as you pentesters call them.`

## Summary
This first APK challenge is solved entirely through fast static triage. The target is an Android APK that embeds an app-specific action string containing a Base64-encoded competition answer.

## Solve path
1. Unpack the APK.
2. Extract strings from `classes.dex`.
3. Filter for app-defined `com.blindsec.*` identifiers.
4. Identify the suspicious action that ends with an encoded payload.
5. Decode the Base64 suffix after the final dot.
6. Recover the embedded competition answer.

## Public note
The exact answer and raw challenge artifact are intentionally omitted from the public repository.

## Shared artifacts
- Supporting commands:
  - [commands.md](commands.md)

## Follow-up note
The second challenge in the same APK chain is documented separately under:
- [../is-it-called-login-or-log-in](../is-it-called-login-or-log-in/README.md)
