# Cryptography

Algorithms, parameters, and rotation policy for every cryptographic primitive Tokenly uses. Implementation details (exact Rust crates, code paths) live in the private repo; the parameters here are the contract those implementations honor.

## Off-LAN snapshot envelope (iCloud fallback)

- **Algorithm:** AES-256-GCM.
- **Key derivation:** HKDF-SHA256 from a 32-byte pairing secret that is generated on first pair and shared between the macOS publisher and the iOS viewer over the LAN+TLS pairing handshake. The pairing secret never leaves the Keychain on either device.
- **HKDF info string:** `tokenly/icloud-envelope/v2` (`v2` is the current envelope version; older v1 envelopes carried metadata in cleartext and are rejected at decode time).
- **HKDF salt:** rotated per write (24 random bytes prepended to the ciphertext).
- **Nonce:** 12 random bytes per write, prepended after the salt.
- **Authenticated additional data (AAD):** a single version byte (`0x02` today).
- **Authenticated plaintext:** the snapshot writer's hostname, TLS fingerprint, and write timestamp are concatenated with the snapshot JSON and encrypted together. iCloud never sees them in cleartext.

## LAN sync pairing handshake

- **Transport:** HTTPS to a self-signed certificate generated on first publisher launch.
- **Trust model:** TOFU (trust-on-first-use) fingerprint pinning. The viewer captures the certificate fingerprint during pairing and rejects any subsequent connection that doesn't match.
- **Mutual auth:** HMAC-SHA256 over `(method, path, request_body_sha256, timestamp)` using the per-pair shared secret. The publisher rejects requests with a timestamp drift over 5 minutes (replay defense).
- **Session token:** issued by the publisher on successful pair, scoped to that one viewer, rotated on every TOFU certificate refresh (e.g. when the user moves the Mac to a new network and regenerates the cert).

## Trial anti-abuse signed token (v0.8+)

- **Algorithm:** HMAC-SHA256.
- **Signing key:** 32-byte random value held only inside the Cloudflare Worker. Rotated quarterly. Old keys are kept until all live trials issued under them expire.
- **Signed payload:** `account_id | device_id | ends_at_utc` where `account_id` is the random UUID the app minted, `device_id` is the hardware-anchored OS identifier, and `ends_at_utc` is the trial end instant.
- **Token format:** `payload.signature_hex` (the same shape as a JWS compact serialization without the alg header — we don't need JWS algorithm negotiation here because there's exactly one key holder).
- **Verification:** the app recomputes the HMAC over the payload it received and compares constant-time. A token whose `ends_at_utc` has passed is also rejected even if the signature verifies, so the local clock alone cannot extend a trial.

## What's NOT used

We deliberately avoid:
- Custom or "rolled" crypto primitives.
- Key escrow with any third party.
- Hardware tokens or attestation beyond what the OS provides at no cost (Keystore on Android, Secure Enclave on Apple).
- Plain-RSA or ECDSA signing schemes (no asymmetric key exchange is needed; the pairing handshake is over TLS, the trial token is single-issuer HMAC).
