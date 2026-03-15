# MITRE User Execution Technique

## Challenge
You hackers with god damn TTPs and MITRE IDs must be very proud of your implants always changing their flow, just execute the malware already!!!!!

Flag format: `T0000.000`

## Logic
- Start from the phishing email and its password-protected ZIP attachment.
- Extract the attachment and inspect the delivered files.
- The package contains a legitimate-looking `openvpn-gui.exe` alongside attacker-controlled DLLs.
- The victim must manually launch the executable for the attack chain to begin.
- The prompt wording points to the execution trigger, not the later DLL side-loading stage.

## ATT&CK Mapping
- Technique: `User Execution`
- Sub-technique: `Malicious File`
- Flag: `T1204.002`

## Why Not `T1574.002`
`DLL Side-Loading` is also present in the payload behavior, but it occurs after the user runs the file. The challenge wording emphasizes the initial execution step, so `T1204.002` is the better fit for the expected flag.
