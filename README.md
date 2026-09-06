# apnctl

**APN control for the Nokia AOD311NK** (Airtel AirFiber ODU) — a small compiled CLI that logs into the router's web interface and changes the cellular APN without opening the browser UI.

Distributed as a prebuilt binary only.

- Supported device: Nokia AOD311NK (5G32-A), FW AOD311NK_R_1.0 / web build 2402.20.06.A17
- macOS builds: `apnctl` is a universal binary (x86_64 + arm64 — runs on both
  Intel and Apple Silicon Macs). `apnctl-arm64` and `apnctl-x86_64` are the
  single-architecture builds if you only want one.
- No installation required — download, `chmod +x`, run

> For use on hardware you own. The tool performs only the same APN read/write
> operations available in the device's own web UI.

## Quick start

```sh
chmod +x apnctl

# 1. Check what's configured now
APNCTL_PASSWORD=<router password> ./apnctl apn get

# 2. Change the APN (example: BSNL)
APNCTL_PASSWORD=<router password> ./apnctl apn set bsnlnet

# 3. Check the router connection state
APNCTL_PASSWORD=<router password> ./apnctl status
```

The router ships with the web password set to the device serial number
(printed on the label), unless it was changed.

## Commands

| Command | Effect |
|---|---|
| `login` | authenticate and show session info |
| `apn get` | list configured APN instances and their state |
| `apn set <name>` | switch the APN (instance 1) to `<name>` and verify |
| `apn set <name> -i <N>` | switch APN instance `N` |
| `status` | dump the current connection status |

## Options

```
-H <url>        router base url (default https://192.168.0.1)
-u <user>       web account (default admin)
-p <password>   web password (else $APNCTL_PASSWORD, else prompt)
-k              enable TLS verification (off by default: self-signed cert)
-t <seconds>    per-request timeout (default 15)
-v              verbose output
```

## Notes

- The APN change survives a reboot. A factory reset on the device restores
  the original APN and password.
- After an APN change it can take a minute for the modem to re-register and
  for data to flow; `status` shows when the connection is back up.
- Bug reports and feature requests: open an issue.

## Integrity

SHA-256 checksums:

```
ae0a0b145eff5926156339a379e0437362f046afe4b1d3dd41a2c2396d569ba4  apnctl          (universal, x86_64 + arm64)
c87c2435b008a4e34a2785a3e20b0ef1dedf8e6c1878ca601468d070fca38f50  apnctl-arm64
ece92fc0109962475deaf99a04138c2f8f4c918d10298772b25b751c3d6b4830  apnctl-x86_64
```

Sessions are cached in `~/.config/apnctl/` (0600) so consecutive commands don't
re-authenticate; the device refuses rapid back-to-back logins.
