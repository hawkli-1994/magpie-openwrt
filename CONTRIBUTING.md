# Contributing

Thanks for helping make **magpie-openwrt** a solid OpenWrt packaging project for Magpie.

## Scope

In scope: packaging, procd, UCI, firewall/DNS hygiene, optional Tailscale/WireGuard integration docs, LuCI (later).

Out of scope: anti-ban / anti-detection / platform-evasion features or messaging.

## Process

1. Open an issue describing the change (or reference the approved design).
2. Prefer small, reviewable PRs.
3. Do not commit real API keys, gateway tokens, or production host details.
4. Pin upstream Magpie CLI versions with SHA256 verification—no unpinned `latest` in release packages.

## Development notes

- Primary arch for v1: **x86_64**
- Magpie CLI is started with `magpie serve` (not systemd); OpenWrt uses **procd**
- Plugins / Bun are deferred for the first release

## Code of conduct

Be respectful. Assume good intent. Keep discussions about reliability and packaging, not account circumvention.
