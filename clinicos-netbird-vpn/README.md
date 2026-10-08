# ClinicOS VPN Tunnel

Secure tunnel from this device to the ClinicOS cloud, powered by [NetBird](https://netbird.io) (WireGuard under the hood).

Required by all on-site ClinicOS apps that need a direct connection back to the cloud — Doctolib Sync, easyTI Sync, DICOM Integration, device bridges. Install this app first; everything else just assumes the tunnel is up.

## First-time setup

1. Install **ClinicOS VPN Tunnel** from the Umbrel App Store
2. Open the app — you'll see a settings page with status "Waiting for setup key"
3. In **ClinicOS Admin**, go to **Devices → VPN** and copy the setup token for this clinic
4. Paste the token and click **Save & connect**
5. Wait 5–10 seconds — the tunnel comes up and the page flips to the status dashboard

```text
ClinicOS Admin              This device                  vpn.nosc.ai
┌──────────────────┐     ┌──────────────────┐     ┌────────────────┐
│ Issue setup      │     │ Paste token      │     │ Peer registered│
│ token for clinic ┼──>  │ Click Save      ─┼──>  │ Tunnel up      │
│                  │     │                  │     │                │
└──────────────────┘     └──────────────────┘     └────────────────┘
```

## Architecture

Two containers run side-by-side:

- **`netbird`** — the official NetBird daemon, in `network_mode: host` with `/dev/net/tun` access. This is what actually moves packets.
- **`web`** — a small FastAPI control plane (the [`clinicos-vpn-agent`](https://github.com/noscai/clinicos-edge/tree/main/apps/vpn-agent)) that serves the setup wizard and status dashboard, and drives the netbird daemon via the host Docker socket.

The setup token is handed once to `netbird up` and never stored by the agent. NetBird caches its rotated session credentials in `/var/lib/netbird`, persisted in `${APP_DATA_DIR}/netbird`.

## What it does

| Direction | How |
|-----------|-----|
| This device → ClinicOS cloud | NetBird WireGuard tunnel to `vpn.nosc.ai` |
| ClinicOS cloud → this device | Cloud reaches this device at its assigned NetBird hostname (e.g. `tisch-umbrel-01.netbird.cloud`) |

## Updating

When a new version is available, Umbrel shows an **Update** badge.

1. Open your Umbrel dashboard
2. Click the app
3. Click **Update**

Your tunnel registration is preserved across updates — `/var/lib/netbird` survives.

## Troubleshooting

| Status | Meaning | Action |
|--------|---------|--------|
| Waiting for setup key | No registration yet | Open the app and paste the setup token |
| Management only | Reached the management server but no peer link | The firewall is probably blocking outbound UDP — ask the network admin to allow outbound UDP (STUN + WireGuard need it) |
| Tunnel down | Was connected, now unreachable | Check the device's internet connection; the agent retries automatically |
| Tunnel up | Everything is working | Nothing to do |

## Network requirements

- **Outbound TCP 443** to `vpn.nosc.ai` — management, signaling, and the relay fallback when direct peer-to-peer isn't possible
- **Outbound UDP** for NAT traversal (STUN, default `stun.wiretrustee.com:3478`) and for the direct WireGuard tunnel. Local listen port and remote peer ports are negotiated at runtime — not a single fixed port. Most clinic firewalls allow outbound UDP by default; restrictive firewalls need a generic outbound-UDP allow rule.
- **`/dev/net/tun`** available on the host (standard on Umbrel and other Linux devices)

## Reconfigure

The status dashboard has a **Reconfigure** link. Clicking it tears down the current tunnel and returns to the setup wizard so you can paste a new setup token (e.g., when re-keying after a security incident, or when assigning this device to a different clinic).
