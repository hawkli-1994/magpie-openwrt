# magpie-openwrt

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![OpenWrt](https://img.shields.io/badge/OpenWrt-24.10%2B-00B5E2?logo=openwrt&logoColor=white)](https://openwrt.org/)
[![Arch](https://img.shields.io/badge/arch-x86__64-informational)](#hardware)
[![Magpie](https://img.shields.io/badge/upstream-yetone%2Fmagpie-6f42c1)](https://github.com/yetone/magpie)
[![Status](https://img.shields.io/badge/status-design%20approved-success)](#roadmap)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**A stable, trustworthy home AI gateway on OpenWrt.**

Pack [yetone/magpie](https://github.com/yetone/magpie) (`magpie-cli`) for soft routers so every LAN device—laptops, phones, IDEs, Claude Code / Codex-style clients—shares one local entrypoint (`http://router:3425` or `http://magpie.lan`).

> **What this is not:** an anti-ban / anti-detection / platform-evasion toolkit.  
> **What this is:** key centralization, one LAN entry, clean egress hygiene.

---

## Why

Running Magpie on a PC works—until every machine needs its own keys, ports, and proxy path. Putting the gateway on the router gives you:

| Goal | How |
|------|-----|
| One entry for the whole home | `magpie serve` behind LAN firewall |
| Keys stay on the router | Provider keys + gateway tokens, mode `0600` |
| Predictable egress | Optional OpenClash / passwall; no random exit hop |
| Leak hygiene | DNS leak guard + IPv6 bypass prevention |
| Remote without WAN expose | Optional Tailscale (reuse system daemon) or WireGuard |

---

## Status

| Area | State |
|------|--------|
| Upstream CLI smoke (static `linux-amd64` + musl chroot) | Done |
| Design (UCI / procd / packaging) | **Approved** |
| Installable `ipk` + procd | Next |
| LuCI app | Deferred (CLI + UCI first) |
| aarch64 | Later (x86_64 first) |

Pinned Magpie CLI target: **v0.1.1614** (SHA256-verified from upstream releases at build time).

---

## Architecture (planned)

```text
LAN / optional Tailscale
        │
        ▼
  magpie-openwrt (procd)
  ├─ magpie serve   ← MAGPIE_ADDR=0.0.0.0:3425
  ├─ UCI → Magpie JSON (XDG config)
  ├─ firewall: LAN (+ optional tailnet) only; WAN closed
  └─ optional: OpenClash/passwall egress, dnsmasq magpie.lan
        │
        ▼
   model providers
```

---

## Hardware

| Class | Guidance |
|-------|----------|
| **Supported (v1)** | x86_64 soft router, **≥1 GB RAM**, room for ~52 MB binary + ~50 MB+ RSS |
| Not recommended | Tiny flash / low-RAM home APs |
| Alternative | Run Magpie on a NAS / Pi beside the router; manage remotely later via LuCI |

---

## Security defaults

- Gateway **not** exposed on WAN
- Cross-host access requires a Magpie **gateway key** (`sk-magpie-key-…`)
- Optional `MAGPIE_ALLOW_IPS` / source limits
- Secrets **not** stored in UCI plaintext exports by default
- Remote: Tailscale MagicDNS and/or WireGuard—never “open 3425 to the internet”

---

## Roadmap

1. **Package** — OpenWrt Makefile, pin + verify upstream CLI, procd init  
2. **Config** — UCI `/etc/config/magpie` → Magpie JSON under `XDG_CONFIG_HOME`  
3. **Network hygiene** — LAN-only firewall zone, DNS/IPv6 guards, optional Clash egress  
4. **Remote (optional)** — Tailscale (reuse OpenWrt `tailscale` package) / WireGuard access faces  
5. **LuCI** — enable/port/keys (masked)/status/logs  

---

## Upstream

- Magpie: [yetone/magpie](https://github.com/yetone/magpie) (MIT)  
- Releases / binaries: [yetone/magpie](https://github.com/yetone/magpie/releases) / [yetone/magpie-releases](https://github.com/yetone/magpie-releases)  
- Docs: [usemagpie.ai](https://usemagpie.ai)

This repo packages and operates Magpie on OpenWrt; it does not reimplement the gateway.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Design-first: open an issue or design note before large packaging changes.

## License

[MIT](LICENSE) — packaging and scripts in this repository.  
Magpie itself remains under its upstream MIT license and copyright.

## Disclaimer

Use responsibly and within provider terms of service. This project focuses on **home-lab reliability and egress hygiene**, not circumventing account or abuse detection.
