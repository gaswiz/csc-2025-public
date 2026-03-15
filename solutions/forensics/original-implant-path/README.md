# Original Implant Path

## Challenge
What is the path of the original implant

Flag format:
`C:\Users\username\folder\folder\folder\implant`

## Goal
Identify the filesystem path of the first malware/implant used on the victim VM.

## Access
- Access details were supplied in the private competition materials and are intentionally omitted from the public repository.

## Safe approach
Do not execute malware. Use filesystem, logs, persistence locations, recent files, and process execution artifacts to recover the first-seen implant path.

## Solve map
1. Mount or open the victim VM.
2. Look for likely initial-access and execution artifacts:
   - `Downloads`
   - `Desktop`
   - `AppData\\Roaming`
   - `AppData\\Local`
   - `Temp`
   - `Startup`
3. Review execution evidence:
   - Prefetch
   - Recent files / Jump Lists
   - Run keys
   - Scheduled tasks
   - Startup folders
   - Browser/mail download locations
4. Build a timeline:
   - earliest malicious file written
   - earliest malicious executable launched
5. Return the full original implant path as the flag.

## Likely evidence sources
- `C:\Users\<user>\Downloads`
- `C:\Users\<user>\AppData\Local\Temp`
- `C:\Users\<user>\AppData\Roaming`
- `C:\Windows\Prefetch`
- `NTUSER.DAT` run history and autoruns
- LNK files in `Recent`

## Next step
Download the VM image and inspect it without executing any suspicious binaries.
