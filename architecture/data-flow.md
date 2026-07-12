# Data flow

What data crosses each boundary, in both directions, with exactly what shape.

## Boundary 1 — Provider → Mac (publisher)

| Provider | Endpoint | Mac sends | Mac receives |
|---|---|---|---|
| Claude.ai | `GET /api/organizations/<org>/usage` | `sessionKey` cookie | `{five_hour, seven_day, extra_usage, …}` with utilization percentages and reset timestamps |
| ChatGPT | `GET /backend-api/wham/usage` | WHAM bearer + optional account_id header | `{rate_limits: [...], credits: {balance, currency}}` |
| Anthropic Admin | `GET /v1/organizations/cost_report` | `x-api-key: sk-ant-admin-…` | Daily cost rollups |
| OpenAI | `GET /v1/organization/costs` | Bearer admin key | Daily cost rollups |
| OpenRouter | `GET /api/v1/credits` | Bearer | MTD credit usage |

Nothing about your prompts crosses this boundary. The endpoints above don't have prompt content to return.

## Boundary 2 — Mac (publisher) → LAN viewer (iPhone / Android / Windows)

The Mac publishes a single signed JSON snapshot every 30 minutes (user-configurable) or on demand. It contains aggregated cost / window-utilization numbers and reset timestamps. What's intentionally NOT in this snapshot:

- Session cookies, bearer tokens, or API keys.
- The raw provider response bodies.
- Prompt content or completion content of any kind.
- The user's name, email, IP, or any identifier other than the publisher's hostname.

The viewer pulls this over HTTPS to the publisher's pinned self-signed cert, authenticates via per-pair HMAC, and stores nothing beyond the latest snapshot in memory plus the pairing material in OS secure store.

## Boundary 3 — Mac → iCloud Drive → iPhone (off-LAN fallback)

When the iPhone is off the home Wi-Fi:

1. The Mac writes the snapshot JSON, AES-GCM-encrypted with a key derived from the per-pair secret, into the `iCloud.dev.macsiem.tokenly` container.
2. Apple iCloud sees only ciphertext + a version byte + a salt + a nonce. The hostname, fingerprint, and write timestamp are inside the authenticated plaintext.
3. The iPhone pulls the envelope from its own iCloud container view, decrypts locally, and renders.

Apple's iCloud servers cannot decrypt the snapshot. The decryption key never leaves the user's Keychain.

## Boundary 4 — Publisher → Encrypted relay → Viewer (optional fallback)

When enabled, the publisher uploads an opaque account identifier and an end-to-end-encrypted snapshot blob to `relay.tokenly.macsiem.dev`. The relay stores and returns only ciphertext; decryption happens on the paired viewer using key material that never leaves the user's secure stores.

## Boundary 5 — App → Provider status pages

Tokenly may fetch public incident state from `status.anthropic.com` and `status.openai.com`. The request is anonymous and carries no provider credential, Tokenly identifier, or usage data.

## Boundary 6 — App ↔ Platform push service

When notifications are enabled, the viewer registers an opaque account identifier and platform push token with `relay.tokenly.macsiem.dev`. The service uses Apple Push Notification service or Firebase Cloud Messaging to deliver a minimal alert. This path never receives a provider credential, prompt, completion, or raw provider response.

## Boundary 7 — Mobile app ↔ App Store / Google Play

Standard in-app-purchase entitlement flow. The store handles monthly or yearly subscription transactions and the seven-day introductory access period; Tokenly receives a Premium yes/no entitlement flag. The macOS and Windows apps are free and unlimited and do not cross a licensing boundary.

## Legacy (removed in 0.7.2 b86): Viewer → Trial Worker

Desktop builds through 0.7.2 b85 could cross this boundary for the former trial and licence checks:

| Direction | Payload |
|---|---|
| Desktop app → Worker | Opaque device/account identifier and, for licence validation, the licence key |
| Worker → Desktop app | Trial/licence status response |

Current builds make no calls to `trial.tokenly.macsiem.dev`. The endpoint remains temporarily available for older builds and is scheduled for decommission.

## What never crosses any boundary

- The content of any conversation you have with an LLM.
- The list of providers you have connected, to anyone besides the device that holds them.
- Any usage / cost number, to anyone besides your own paired devices (and Apple iCloud as ciphertext in transit).
