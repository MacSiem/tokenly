# Secure storage, per platform

Every credential Tokenly handles — provider API keys, Claude.ai session cookies, ChatGPT WHAM bearers, and the pairing material that links your viewer to your Mac — lives in the operating system's secure store on the device that holds it. Nothing is written to disk in plaintext.

## macOS (publisher)

- **Store:** Apple Keychain.
- **Service identifier:** `dev.macsiem.tokenly.mac`.
- **Accessibility flag:** `kSecAttrAccessibleWhenUnlocked` — secrets are unreadable when the user is logged out or the device is asleep before first unlock.
- **iCloud sync:** `kSecAttrSynchronizable = false`. Credentials never leave this Mac via iCloud Keychain.
- **Disk-side state:** the sync daemon also writes a copy of its TLS cert + key into `~/Library/Application Support/dev.macsiem.tokenly.mac/syncd/` so it can start before Keychain unlocks at boot. The files are auto-restored from Keychain on every launch and have mode 0600.

## iOS (viewer)

- **Store:** Apple Keychain.
- **Service identifier:** `dev.macsiem.tokenly`.
- **Accessibility flag:** `kSecAttrAccessibleWhenUnlocked`.
- **iCloud sync:** `kSecAttrSynchronizable = false`.
- **What's stored:** the per-pair session token, the Mac's TLS certificate fingerprint, and the HMAC secret used for reauthentication — one set per paired Mac hostname.
- **UserDefaults (non-secret):** a JSON array of paired hostnames + the last-successful hostname. Never carries any credential.
- **Important:** no provider credential ever lives on iOS. The iPhone is a read-only viewer of the snapshot the Mac publishes.

## Android (viewer)

- **Store:** `EncryptedSharedPreferences` from the AndroidX Security library.
- **File name:** `usagedeck_sync.xml`, location `/data/data/dev.macsiem.tokenly/shared_prefs/usagedeck_sync.xml`.
- **Key wrap:** AES-256-GCM with the master key wrapped by Android Keystore (hardware-backed where available).
- **What's stored:** per-pair session token, Mac's TLS certificate fingerprint.
- **Important:** no provider credential lives on Android. The phone is a read-only viewer.

## Windows (viewer / publisher)

- **Store:** Windows Credential Manager.
- **Resource name:** `dev.macsiem.tokenly.windows`.
- **Disk-side state:** when run in publisher mode, the sync daemon writes TLS cert + key to `%APPDATA%/dev.macsiem.tokenly.windows/syncd/` with restrictive ACLs.

## What this means in practice

- An attacker who pulls the disk out of any of these devices, but doesn't have the user's login password or device passcode, can't read any Tokenly credential or pairing token.
- Backup tools that don't include the secure store (most cloud backups by default) will skip these credentials, so a phone restored from such a backup will need to be re-paired with the Mac — by design.
- An attacker with malware running as the user can read the secure store on iOS/Android (the OS allows the owning app to read its own entries). At that point the attacker has the user's session already, so this is not a Tokenly-specific weakness.

## Erase paths

- macOS: `Settings → Privacy → Delete all Tokenly data` (v0.7+) walks the keychain service and removes every entry above.
- iOS: `Settings → Connections → Forget this Mac` removes the pairing entries for one hostname; deleting the app removes all of them along with the local app data.
- Android: standard "Clear data" in system Settings → Apps → Tokenly wipes the EncryptedSharedPreferences file.
- Windows: removing the credential from Credential Manager + deleting `%APPDATA%/dev.macsiem.tokenly.windows/`.
