# Threat model

This document lists the attacker models Tokenly defends against, and the ones we explicitly do not.

## In scope

### Curious attacker on the same Wi-Fi
Tries to sniff or replay the sync traffic between the paired Mac and the iPhone / Android / Windows viewer.

Mitigation: discovery via mDNS (`_usagedeck._tcp`), every snapshot fetch is HTTPS to a self-signed cert that the viewer pins on first pair (TOFU fingerprint stored in the viewer's secure store). A request without the matching session token + cert fingerprint is rejected.

### Lost or stolen iPhone / Android with screen lock on
Attacker has physical access to the device.

Mitigation: pairing tokens, certificate fingerprints, and the shared HMAC secret all live in OS secure store gated by `kSecAttrAccessibleWhenUnlocked` (iOS) / EncryptedSharedPreferences (Android) / Credential Manager (Windows). They are unreadable until the user unlocks the device.

### Tokenly developer wants to read user prompts
The maintainer of this codebase tries to extract user chat content.

Mitigation: the app never sees prompts or completions because it never calls a chat / completion / message endpoint. It only calls the providers' own usage / billing endpoints. Supporting encrypted-relay and push-delivery services do not receive prompt content or provider credentials.

### Compromised cloud provider reading the off-LAN snapshot
Apple is compromised (or coerced) and reads the contents of the `iCloud.dev.macsiem.tokenly` container.

Mitigation: the snapshot envelope is AES-GCM encrypted with a key derived via HKDF-SHA256 from a secret that lives only on the user's macOS Keychain and the paired iPhone's Keychain. iCloud servers see ciphertext + a version byte; metadata (hostname, fingerprint, write time) is inside the authenticated payload, not in cleartext headers.

## Legacy (removed in 0.7.2 b86)

### Legacy access-cycling attacker (removed in 0.7.2 b86)
A user tries to restart the 48-hour Premium trial forever by reinstalling the app.

Mitigation used by builds through 0.7.2 b85: the trial server (`trial.tokenly.macsiem.dev`) tracked one trial per `tokenly_account_id` and per hardware-anchored `device_id`. The signed HMAC token from the server was required to unlock trial-gated features. Current builds do not use this mechanism.

## Out of scope

These are not models Tokenly tries to defend against. They are the user's responsibility, the OS vendor's responsibility, or the LLM provider's responsibility.

- **Kernel-level malware on the user's Mac.** If an attacker has root, no userspace app can defend against them.
- **Apple / Google / Microsoft store compromise.** We trust the signed-app delivery channel; if the store ships a malicious update under our name, that's an app-store-level breach, not a Tokenly-level one.
- **Provider account compromise.** If your Claude or ChatGPT account itself is compromised, Tokenly will faithfully report whatever usage the attacker generated — that's an Anthropic / OpenAI account hygiene problem.
- **Provider-side data retention.** Anthropic and OpenAI both retain account usage records. Tokenly reads the same numbers the user sees in their account dashboard; we don't and can't change what the provider stores.
- **Targeted nation-state attacker.** Out of scope.
- **Side channels** (timing, power, electromagnetic) on a physically-present attacker. Out of scope.

## Cryptographic primitives in use

- AES-256-GCM for the off-LAN snapshot envelope.
- HKDF-SHA256 for the envelope key derivation.
- TLS 1.2+ with TOFU certificate fingerprint pinning for LAN sync.

Algorithm parameters and rotation policy are in [cryptography.md](cryptography.md).
