# Security policy

## Supported versions

Tokenly is in TestFlight Internal as of 2026-05-26. There is not yet a public store release. Once a public release exists, the latest **two** App Store / Play Store / Microsoft Store versions will receive security fixes.

| Channel | Version | Supported |
|---|---|---|
| TestFlight Internal (Apple) | 0.7.0 (build 12) | ✅ |
| Public stores | — | n/a yet |

## Reporting a vulnerability

If you find a security issue in Tokenly, **please email** `mail@macsiem.dev` with:

1. A short description of the issue.
2. The platform and Tokenly version (visible in Settings → About).
3. Steps that reliably reproduce it, or a sample payload.
4. Your assessment of impact (data exposure, code execution, account-level compromise, etc.).

If the issue is sensitive enough that even the subject line risks disclosure, use the subject `Tokenly security report` and send only the minimum context needed for me to set up a more secure channel.

## What I treat as in-scope

- The four Tokenly clients: iOS, macOS, Android, Windows.
- The pairing handshake between the macOS publisher and any paired viewer.
- The off-LAN snapshot envelope (AES-GCM v2 in `iCloud.dev.macsiem.tokenly`).
- The encrypted snapshot relay and push-notification delivery path.
- Anything claimed in [privacy](privacy.md) that the app does not actually honor.

## Legacy (removed in 0.7.2 b86)

Desktop builds through 0.7.2 b85 could contact `trial.tokenly.macsiem.dev` for the former trial and licence checks. Reports concerning that endpoint remain in scope while it is temporarily available for those older builds; it is scheduled for decommission.

## What is out of scope

- LLM provider account compromise (Anthropic / OpenAI / etc. account hygiene is the user's responsibility).
- The user's own OS being compromised.
- The GitHub Pages site hosting these docs (it serves static content only; no Tokenly state lives there).
- Issues that require physical access to an unlocked device.

## Response time

I am the sole maintainer of this project. I aim to acknowledge a credible report within **3 working days** and to ship a fix or a documented mitigation within **30 days** for critical issues. Severe/public-exploit-risk issues will take precedence over everything else.

## Disclosure

By default I prefer **coordinated disclosure**: I confirm the report, work on a fix, ship the fix, and publish a short post-mortem in the repository release notes once users have had a reasonable window to update. If you have a specific timeline (academic publication, conference talk, etc.) let me know and I will try to align.

## Recognition

I am happy to thank reporters by name (or pseudonym) in the release notes. If you prefer not to be credited, say so in the report.

## Out-of-band channel

If email is not reachable, leave a public issue in this repository asking for a secure channel, with no detail about the vulnerability. I will reply with a way to escalate privately.
