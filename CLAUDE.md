# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

HomeProxy is a LuCI (OpenWrt web UI) application that provides a graphical interface for managing the [sing-box](https://sing-box.sagernet.org/) proxy client and server on OpenWrt routers.

## Architecture

- **Build System**: OpenWrt package Makefile (`TOPDIR/rules.mk`)
- **Service Manager**: procd with init script at `/etc/init.d/homeproxy`
- **Configuration**: UCI (`/etc/config/homeproxy`) with multiple sections: `infra`, `config`, `control`, `routing`, `dns`, `subscription`, `server`
- **Core Logic**: ucode scripts in `/etc/homeproxy/scripts/`
- **Frontend**: LuCI JavaScript views in `htdocs/luci-static/resources/view/homeproxy/`
- **RPC Layer**: ucode at `root/usr/share/rpcd/ucode/luci.homeproxy`

## Key Components

| Path | Purpose |
|------|---------|
| `root/etc/init.d/homeproxy` | Service init/stop/reload, process spawning |
| `root/etc/homeproxy/scripts/generate_client.uc` | Generates sing-box client config |
| `root/etc/homeproxy/scripts/generate_server.uc` | Generates sing-box server config |
| `root/etc/homeproxy/scripts/firewall_pre.uc` | Firewall pre-processing |
| `root/etc/homeproxy/scripts/firewall_post.ut` | nftables rule generation |
| `htdocs/luci-static/resources/view/homeproxy/*.js` | LuCI UI views |

## Supported Routing / Proxy Mode

This slim build only supports:

- Routing mode: `bypass_mainland_china` - proxy only non-China traffic
- Routing ports: all ports
- Proxy mode: `redirect_tproxy` - Redirect TCP + TProxy UDP

## Dependencies

- sing-box
- firewall4 (nftables)
- kmod-nft-tproxy
- ucode-mod-digest
- dnsmasq (for DNS hijacking)

## Common Tasks

This is an OpenWrt package - it builds within the OpenWrt buildroot. There's no local dev server or test suite. Development typically involves:
- Editing ucode scripts or JavaScript views
- Building an IPK via `.github/build-ipk.sh` or GitHub Actions
- Deploying to a test OpenWrt device
- Checking `/var/run/homeproxy/homeproxy.log` for errors

## Build Commands

```bash
# Via GitHub Actions workflow - see .github/workflows/build-ipk.yml
# Or locally within an OpenWrt buildroot:
make package/luci-app-homeproxy/compile
```
