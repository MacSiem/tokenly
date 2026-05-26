# Tokenly — transparency repo

Tokenly is a local-first LLM usage and cost tracker for Apple (iOS + macOS), Android, and Windows. This repository publishes its **privacy policy, terms, security model, and architecture overview** so anyone can verify the claims Tokenly makes in its in-app onboarding and on the App Store / Play / Microsoft Store listings.

The full application source code lives in a separate private repository. This one carries only the material that lets you audit how Tokenly handles your data, your credentials, and the network surface it opens. That's a deliberate split — privacy claims must be checkable; UX and connector strategy stay private.

## What Tokenly does

Tokenly reads usage and cost for the LLM providers you actually use — Claude, ChatGPT, the OpenAI / Anthropic / OpenRouter APIs, and more — **using your own credentials, on the device that holds them**. There is no Tokenly server. Your prompts, your completions, and your chat content are never seen, stored, or proxied by us.

## What Tokenly does NOT do

- It does not relay or proxy any prompt, completion, or chat content.
- It does not store your prompts, replies, files, or anything else you type into a chat.
- It does not show ads. It will not show ads in any future version.
- It does not collect analytics or telemetry of any kind.
- It does not ship your API keys, session cookies, or bearer tokens off the device that holds them.

## What's in this repository

- [Privacy Policy](privacy.md) — the same policy linked from inside the app and from every store listing.
- [Terms of Service](terms.md)
- [Support](support.md) — how to report a bug or BETA gap.
- [Threat model](security/threat-model.md) — what we model against, what's out of scope.
- [Secure storage](security/secure-storage.md) — exactly where credentials live, per platform.
- [Network surface](security/network-surface.md) — every host Tokenly opens a connection to, and why.
- [Cryptography](security/cryptography.md) — algorithms and parameters for the off-LAN snapshot envelope, pairing handshake, and trial anti-abuse token.
- [Architecture overview](architecture/overview.md) — macOS publisher + iOS/Android/Windows viewers + LAN/iCloud sync.
- [Data flow](architecture/data-flow.md) — what data crosses each platform boundary.

## What stays in the private repo

The private repository carries the implementation details that don't change the privacy story:

- The exact endpoints, cookie names, and response shapes used to read each provider's usage.
- The Rust FFI surface between core and the platform clients.
- The build pipeline, signing scripts, and provisioning profiles.

If anything in this repository's privacy or security claims contradicts what the app actually does, that's a bug — please report it via the support contact in [support.md](support.md).

## License

This repository is MIT-licensed (see [LICENSE](LICENSE)). The app binaries it documents are not open-source.

## Contact

Maciej Siemiński · maciek.sieminski@gmail.com
