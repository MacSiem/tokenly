# Contributing

This repository is the **transparency mirror** for the Tokenly app: privacy policy, terms of service, support FAQ, security model, and architecture overview. The app's own source code lives in a separate **private** repository.

## What I welcome via PR

- **Translations** of `privacy.md`, `terms.md`, and `support.md` (especially Polish, but any locale is fine).
- **Fact corrections** in the security / architecture documents — if something I claim about how Tokenly works does not match what the app actually does, that's a bug worth fixing here.
- **Clarifications** that make the privacy story easier to read for a non-technical user.

For these, open a regular pull request against `main`. I will review within ~3 working days.

## What I will not merge here

- Feature code or bugfixes for the Tokenly app itself — this repository has no app code.
- Marketing copy that softens or removes specific privacy claims.
- Anything that adds tracking, analytics, third-party SDKs, or ad-related material — Tokenly has none of these by policy.
- Files containing real secrets / tokens / credentials of any kind. A CI lint blocks accidental leaks.

## How to suggest a feature for the app

The app code is closed-source, but I read every email at `maciek.sieminski@gmail.com`. Send:

1. The use case you have in mind.
2. Which platform you'd use it on (iOS / macOS / Android / Windows).
3. Anything you've already tried that didn't fit.

If a feature request matches the project's privacy charter, it usually shows up in a future release.

## Coding standards (for docs PRs only)

- Plain Markdown, no HTML, no embedded JS.
- Wrap long lines at ~120 chars in narrative paragraphs; tables can be wider.
- Avoid jargon where a normal user can read the text — this repo is meant to be auditable by non-engineers too.

## Code of conduct

Be kind, on-topic, and assume the other person is acting in good faith.

## Contact

Maciej Siemiński · maciek.sieminski@gmail.com
