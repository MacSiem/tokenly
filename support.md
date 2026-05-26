---
layout: default
title: Support
permalink: /support/
---

# Tokenly — Support

**Contact:** maciek.sieminski@gmail.com
**Repository:** [github.com/MacSiem/usagedeck](https://github.com/MacSiem/usagedeck)

## Quick answers

**Tokenly is showing zero usage / "no key in Keychain" — what now?**
Open the Providers tab on your Mac, pick the provider you want, and either choose Connect (browser login for Claude.ai / ChatGPT) or Paste key (for API providers). Provider credentials always live on the Mac that holds them; the iPhone / Android / Windows viewer only mirrors the snapshot the Mac produces.

**My iPhone says "Sign in again on paired Mac" — why?**
The session cookie or token Tokenly is using has expired on the provider's side. Open the Mac, go to Providers, pick the affected provider and choose Reconnect.

**Does Tokenly see my prompts?**
No. Tokenly only reads each provider's usage / billing endpoint (rate-limit windows, monthly spend, plan tier). It does not call any chat / completion / message endpoint, and there is no Tokenly server to send anything to. See the [Privacy Policy](../privacy/) for the full data table.

**Can I use Tokenly without iCloud?**
Yes. iCloud is only used for the optional off-LAN snapshot fallback between your Mac and your iPhone. If you stay on the same Wi-Fi (or don't pair an iPhone at all), iCloud is never touched.

**Where do my settings live? Will they survive an app update?**
On iOS in UserDefaults + Keychain, on Android in EncryptedSharedPreferences, on macOS in Apple Keychain + `~/Library/Application Support/dev.macsiem.tokenly.mac/`, on Windows in Credential Manager + `%APPDATA%/dev.macsiem.tokenly.windows/`. All four locations are preserved across normal app updates. They are wiped only if you manually clear the app's data or uninstall + reinstall on a device with no backup.

**Does Tokenly support Claude Code / OpenAI Codex / GitHub Copilot session usage?**
Subscription gauges for Claude (Pro / Max 5x / Max 20x) and ChatGPT (Plus / Pro / Pro Lite) read the official utilization endpoints and update every 30 minutes by default. Other consumer subscriptions (Grok, DeepSeek, Mistral, Perplexity, Cursor, Copilot, etc.) ship as **BETA** — their card explains exactly what we still need from the provider to make the gauge accurate.

## Reporting a bug or BETA gap

Email **maciek.sieminski@gmail.com** with:

- the platform (iOS / macOS / Android / Windows) and Tokenly version (Settings → About);
- the provider involved, if any;
- what happened versus what you expected.

In-app, every BETA provider card has a Report button that opens an email pre-filled with the slug and an anonymized note about the missing field — please use it; it cuts diagnostic time considerably.

## Privacy questions

Privacy questions go to the same email above; see also the full [Privacy Policy](../privacy/) and [Terms of Service](../terms/).
