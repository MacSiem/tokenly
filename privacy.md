---
layout: default
title: Privacy Policy
permalink: /privacy/
---

# Tokenly — Privacy Policy

**Effective date:** 2026-05-26
**Developer:** Maciej Siemiński
**Contact:** mail@macsiem.dev

Tokenly is a local-first LLM usage and cost tracker. This document describes every piece of data Tokenly touches, where it stays, and what we send anywhere off your device.

## Plain summary

- **Tokenly never relays your prompts or completions.** We do not operate any inference proxy. Your chats with Claude, ChatGPT, the OpenAI API, etc. happen between you and that provider exactly as before.
- **Tokenly never stores your prompts, replies, or chat content.** We do not see them.
- **Tokenly does not run any analytics or telemetry.** No third-party SDK is bundled for that purpose.
- **Tokenly never sends your API keys, session cookies, or bearer tokens anywhere off the device that holds them.** They live in that device's own secure store (Apple Keychain, Android EncryptedSharedPreferences, or Windows Credential Manager).
- **Tokenly does not show ads.** Free or future-paid, no version of Tokenly will ever serve ads.

## What data Tokenly handles, where it stays

| Data | Why Tokenly touches it | Stays on device | Goes anywhere |
|---|---|---|---|
| Claude.ai session cookie | To call your own `usage` endpoint at claude.ai and read your subscription utilization | Yes (Apple Keychain on Mac) | No |
| ChatGPT WHAM bearer token | To call your own `usage` endpoint at chatgpt.com and read your subscription utilization and credit balance | Yes (Apple Keychain on Mac) | No |
| Anthropic Admin API key (`sk-ant-admin-…`) | To call your own Cost Reports at api.anthropic.com | Yes (Apple Keychain on Mac) | No |
| OpenAI Admin API key | To call your own organization Costs at api.openai.com | Yes (Apple Keychain on Mac) | No |
| OpenRouter API key (`sk-or-v1-…`) | To call your own credits + auth endpoints at openrouter.ai | Yes (Apple Keychain on Mac) | No |
| Anthropic / OpenAI / OpenRouter responses (cost totals, percentages, reset times) | To display them in the app, widget and notifications | Yes | No |
| Your local user preferences (budget target, refresh interval, widget mode, notification toggles) | To remember how you configured the app | Yes (local app storage) | No |
| Optional encrypted snapshot for off-LAN pairing (Apple only) | To let your iPhone show desktop data over cellular when the LAN is unreachable | Encrypted at rest in **your** iCloud Drive container `iCloud.dev.macsiem.tokenly`; the encryption key is shared only with the device you paired | Yes — your iCloud Drive only |
| Pairing tokens + TLS fingerprints for the iPhone / Android / Windows viewer | To recognize the desktop publisher when you reconnect | Yes (Apple Keychain on iPhone, EncryptedSharedPreferences on Android, Windows Credential Manager on PC) | No |

## Tokenly does NOT

- relay, store, log, summarize, or otherwise process your prompts or completions on any server we control — **we do not operate any server**;
- collect your name, email, address, phone, biometric, health, or contact data;
- run third-party analytics SDKs (Sentry, Crashlytics, Mixpanel, Amplitude, Segment, Firebase Analytics, etc.) — none are bundled in any current build;
- track you across other apps or websites;
- show ads or pass any identifier to an advertising SDK;
- write any prompt or completion content to disk in any form.

## What about crash logs?

The macOS app ships an **opt-in, local-only** crash logger. When enabled and the app crashes, a stack trace is written to your Mac's `~/Library/Application Support/dev.macsiem.tokenly.mac/crashes/` for you to inspect. **Nothing is uploaded anywhere.** Crash reporting is disabled by default and the setting is visible in macOS → Settings → Privacy.

## Where credentials and pairing material live

| Platform | Secure store | Notes |
|---|---|---|
| macOS (publisher) | Apple Keychain (`kSecAttrAccessibleWhenUnlocked`, not iCloud-synced) | All provider credentials and the syncd shared secret/fingerprint live here. Cert files in `~/Library/Application Support/dev.macsiem.tokenly.mac/` are backed up to Keychain on first run and auto-restored on launch. |
| iOS (viewer) | Apple Keychain (`kSecAttrAccessibleWhenUnlocked`, `kSecAttrSynchronizable=false`) | Stores only the per-host pairing token, TLS fingerprint and shared secret. **No provider credentials live on iOS.** |
| Android (viewer) | EncryptedSharedPreferences (AES-256-GCM key wrap) | Stores only the per-host pairing token, TLS fingerprint and shared secret. **No provider credentials live on Android.** |
| Windows (viewer / publisher) | Windows Credential Manager (resource `dev.macsiem.tokenly.windows`) | Stores per-host pairing token + fingerprint + shared secret on the device that holds them. |

## Network traffic

Tokenly only opens connections to the providers you've configured. By default it talks to:

- `claude.ai` — to read your Claude subscription usage (only if you've connected Claude.ai)
- `chatgpt.com` — to read your ChatGPT subscription usage (only if you've connected ChatGPT)
- `api.anthropic.com` — to read your Anthropic API cost reports (only if you've added an Admin key)
- `api.openai.com` — to read your OpenAI organization costs (only if you've added an Admin key)
- `openrouter.ai` — to read your OpenRouter credits (only if you've added an OpenRouter key)
- Your local LAN, for paired iPhone / Android / Windows clients pulling the snapshot (mDNS + TLS, never the public internet)
- Apple's iCloud Drive servers, only for the optional encrypted off-LAN snapshot fallback (Apple devices only)

Tokenly does not call any other host. There is no opt-out flag because there is nothing to opt out of — the list above is the entire network surface.

## Third-party services

Tokenly currently bundles **no** third-party SDKs in any shipped build. Future Premium (one-time unlock) will route through Apple App Store / Google Play in-app purchases; in that case Apple / Google handle the transaction and Tokenly receives only a "Premium yes/no" entitlement flag.

If we ever add anything that changes the surface above (crash uploader, analytics, etc.) the new policy will be published here with a new effective date and the change will be called out in the in-app release notes **before** it ships.

## Permissions Tokenly requests

| Permission | Why | Required? |
|---|---|---|
| Local Network (iOS) | Discover the paired Mac publisher via mDNS (`_usagedeck._tcp`) | Yes for LAN sync; iCloud fallback works without it |
| Camera (iOS / Android) | Scan the pairing QR code shown on your Mac | No — you can also enter the host + PIN manually |
| iCloud account / Documents (iOS / macOS) | Read/write the encrypted snapshot envelope in your `iCloud.dev.macsiem.tokenly` container | No — required only if you want off-LAN sync |
| Notifications (iOS / macOS / Android / Windows) | Show desktop / lock-screen alerts when you hit a budget threshold | No — fully optional |
| Keychain / Credential Manager | Store provider credentials and pairing material | Yes — the app cannot run without writing to its own keychain bucket |

## Your rights

- **You can stop using Tokenly at any time.** Uninstalling removes all local app data. Provider credentials in the OS keychain stay until you choose Disconnect or wipe the keychain entry yourself.
- **You can revoke each provider in the app's Providers tab.** Disconnect removes the credential from the local keychain (and wipes the WKWebView cookies/local storage for that domain on macOS).
- **You can revoke iCloud sync** by toggling Tokenly off in iOS Settings → Apple ID → iCloud → iCloud Drive → Apps.
- **EU users (GDPR):** Maciej Siemiński is the data controller for Tokenly. Because Tokenly processes data only on your devices, the legal basis is your consent (granted by installing and configuring the app). You may withdraw consent at any time by uninstalling Tokenly or revoking permissions.
- **California users (CCPA):** Tokenly does not sell your personal information. It does not have any of your personal information to sell.
- **Children:** Tokenly is not directed at children under 13. We do not knowingly process data from children under 13.

## Changes to this policy

If anything material changes (we add an SDK, change a default, ship a new platform) the new policy will be published at the same URL with a new effective date, and the change will be noted in the in-app release notes.

## Contact

For privacy questions, write to mail@macsiem.dev.
