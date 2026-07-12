# Network surface

Every connection Tokenly opens. This is the entire list. Anything not here, Tokenly does not call.

| Host | When opened | What goes out | What comes back |
|---|---|---|---|
| `claude.ai` | macOS, only if Claude.ai is connected | Your session cookie | The same usage utilization JSON the Settings → Usage page in claude.ai already serves you |
| `chatgpt.com` | macOS, only if ChatGPT is connected | Your WHAM bearer token | The WHAM usage + credit-balance JSON the app already serves you |
| `api.anthropic.com` | macOS, only if you added an Admin API key (`sk-ant-admin-…`) | The admin key as a header | Daily cost reports (one row per provider per day, in USD) |
| `api.openai.com` | macOS, only if you added an Admin API key | Bearer token | Daily organization cost rollups |
| `openrouter.ai` | macOS, only if you added an OpenRouter API key | Bearer token | Month-to-date credit usage |
| LAN multicast | iOS / Android / Windows viewer, when looking for the paired Mac | `_usagedeck._tcp` mDNS query | The Mac's hostname + port |
| Paired Mac LAN address, port 7642 | iOS / Android / Windows viewer, after pairing | HMAC proof + the pinned TLS fingerprint | A snapshot JSON over TLS |
| Apple iCloud Drive (`iCloud.dev.macsiem.tokenly` container) | macOS publisher writes; iOS viewer reads — only if iCloud fallback is enabled | An AES-GCM ciphertext blob | The same blob |
| `relay.tokenly.macsiem.dev` | Only when encrypted relay fallback is enabled | Opaque account identifier + end-to-end-encrypted snapshot blob | The same ciphertext blob |
| `status.anthropic.com` / `status.openai.com` | When checking provider service health | Anonymous HTTPS request; no credentials or identifiers | Public incident status |
| `relay.tokenly.macsiem.dev` → Apple Push Notification service / Firebase Cloud Messaging | Only when push notifications are enabled | Opaque account identifier + platform push token + minimal alert payload; no provider credential, prompt, or completion content | Delivery acknowledgement / notification delivery |

That's the entire current list. Tokenly operates no inference proxy and its supporting relay cannot decrypt snapshots. Connections happen only when the corresponding provider is connected or the related optional feature is enabled.

## Legacy (removed in 0.7.2 b86)

Desktop builds through 0.7.2 b85 could contact `trial.tokenly.macsiem.dev` for the former trial and licence checks. They sent an opaque device/account identifier and, for licence validation, a licence key. The endpoint remains temporarily available for those builds and is scheduled for decommission.

## What does NOT travel over the wire

- Your prompts, your completions, any chat content.
- Provider session cookies or API keys to anything other than that provider's own endpoint.
- The hostname of any other paired device.
- The TLS fingerprint of any other paired device.
- The contents of the LLM provider's response, beyond the usage / cost / window-reset fields the app needs to render gauges.

## What about logs

- macOS publisher logs go to the system unified log under subsystem `dev.macsiem.tokenly`. Credentials are tagged `private` so they're redacted in shared sysdiagnose bundles unless the user explicitly captures with the private flag.
- iOS / Android / Windows viewers log under their own subsystems; same redaction discipline.

## Refresh cadence

By default the macOS publisher polls each connected provider every 30 minutes. The user can change this in Settings → Refresh. There is no "always-on" socket; every fetch is a one-shot HTTPS request, response, close.
