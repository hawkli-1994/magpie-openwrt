# magpie-openwrt

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![OpenWrt](https://img.shields.io/badge/OpenWrt-24.10%2B-00B5E2?logo=openwrt&logoColor=white)](https://openwrt.org/)
[![iStoreOS](https://img.shields.io/badge/iStoreOS-app%20planned-orange)](#distribution)
[![Docker](https://img.shields.io/badge/Docker-compose%20planned-2496ED?logo=docker&logoColor=white)](#distribution)
[![Arch](https://img.shields.io/badge/arch-x86__64-informational)](#hardware)
[![Magpie](https://img.shields.io/badge/upstream-yetone%2Fmagpie-6f42c1)](https://github.com/yetone/magpie)
[![Status](https://img.shields.io/badge/status-pivoting%20to%20governance-yellow)](#why-the-router)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**Home / small-team AI usage governance on the network edge.**

OpenWrt (and soon **iStoreOS / Docker**) packaging for [yetone/magpie](https://github.com/yetone/magpie): one always-on LAN entrypoint, **per-device gateway keys**, usage & spend by key, budgets, and one-click revoke—without scattering provider secrets across laptops.

> **Not** an anti-ban / anti-detection / evasion toolkit.  
> **Not** “automatic billing by MAC” (Magpie’s usage ledger keys off **gateway-key**, not client IP/MAC).  
> **Yes:** key-centric governance + always-on LAN placement + easy install paths.

---

## Why the router?

“Just run Magpie somewhere” works on NAS, Docker, or a PC. The router only wins when we use what those hosts lack:

| Router-native leverage | How we use it |
|------------------------|---------------|
| Always on, on-path | Single `http://router:3425` / `magpie.lan` for the whole LAN |
| Central revoke | Issue one Magpie **gateway key** per device or member; rotate/remove when a laptop leaves |
| Usage & budget | Upstream `usage.jsonl` + `/v1/magpie/usage` (tokens, cost, model, callerKey); optional alerts on the edge |
| Device roster (assist) | Map DHCP hostnames → key names in LuCI; **no** claim of MAC-based metering |

If Magpie had no per-key usage or revoke, this project would not be worth a dedicated OpenWrt effort.

---

## Distribution

| Channel | Role |
|---------|------|
| **iStoreOS app** (planned) | Lowest friction for CN soft-router users |
| **Docker Compose** (planned) | Same governance story beside the router / on NAS |
| **OpenWrt `ipk`** | Native procd + UCI on x86_64 soft routers |

Packaging alone is not the product; **key-level governance UX** is. Install paths exist so people can actually try it.

---

## Status

| Area | State |
|------|--------|
| Upstream CLI smoke (static + musl chroot) | Done |
| Usage/source feasibility (per gateway-key) | **Confirmed** |
| OpenWrt `ipk` + procd | Next |
| iStoreOS / Docker one-click | Planned (priority for reach) |
| Governance UI (keys, usage, budgets) | Planned |
| Full LuCI config surface | Deferred |
| aarch64 | Later |

Pinned Magpie CLI (packaging): **v0.1.1614** (SHA256-verified at build time).

---

## Architecture (planned)

```text
Devices (each with its own gateway key)
        │
        ▼
  magpie-openwrt / iStoreOS app / Docker
  ├─ magpie serve          ← LAN-only (optional Tailscale/WG)
  ├─ usage.jsonl + limits  ← per callerKey tokens/cost
  ├─ key issue / revoke    ← provider secrets stay on the box
  └─ optional: Clash egress, DNS/IPv6 hygiene, DHCP→key roster
        │
        ▼
   local and/or cloud model providers
```

Local-first / cloud-fallback routing is supported upstream via Magpie routing groups; edge wizards come after packaging PoC.

---

## Hardware

| Class | Guidance |
|-------|----------|
| **v1 target** | x86_64 soft router or mini PC, **≥1 GB RAM** (~52 MB binary + ~50 MB+ RSS) |
| Skip | Tiny flash / low-RAM APs |
| Fine alternative | Docker/iStoreOS on a NAS next to the router—same key governance story |

---

## Security defaults

- Not exposed on WAN
- Clients use Magpie **gateway keys**; provider API keys stay on the gateway
- Optional IP allowlists (`RemoteAddr` only; Magpie does not trust `X-Forwarded-For` for allow)
- Secrets mode `0600`; backups exclude keys by default
- Remote: Tailscale (reuse system daemon) and/or WireGuard

---

## Roadmap

1. **Ship installables** — `ipk` + Docker Compose; then iStoreOS app  
2. **Governance loop** — issue/list/revoke keys; surface usage & limits; budget alerts  
3. **Network hygiene** — LAN-only firewall, DNS/IPv6 guards, optional Clash egress  
4. **Roster assist** — DHCP hostname → key label mapping  
5. **Stop-loss** — if no stranger runs an alpha within ~30 days, shrink to packaging scripts only  

---

## Upstream

- Magpie: [yetone/magpie](https://github.com/yetone/magpie) (MIT)  
- Docs: [usemagpie.ai](https://usemagpie.ai)

This repo packages and operates Magpie at the edge; it does not replace Magpie.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Design-first. Please keep discussions on reliability and governance—not account circumvention.

## License

[MIT](LICENSE) — packaging and scripts here. Magpie remains under upstream MIT.

## Disclaimer

Use within provider terms of service. Focus is **edge governance and installability**, not circumventing abuse detection.
