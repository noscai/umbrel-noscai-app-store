# ClinicOS KIM Clientmodul

Arvato Systems' **kim+ KIM-Clientmodul** on the Umbrel device. easyTI sends and receives the practice's KIM mail
through it, so KIM no longer depends on a Windows or Mac PC being switched on. Image and details:
[`clinicos-edge/apps/kim-clientmodul`](https://github.com/noscai/clinicos-edge/tree/main/apps/kim-clientmodul).

kim+ only works with Arvato's KIM service (`arv.kim` addresses). A practice with another KIM provider needs that
provider's Clientmodul.

## Ports

| Port | What |
|------|------|
| 3083 | the kim+ desktop in the browser (Umbrel login in front) |
| 10110 | POP3 proxy (TLS), for easyTI and mail clients |
| 10025 | SMTP proxy (TLS), for easyTI and mail clients |

## First-time setup

1. Install **ClinicOS KIM Clientmodul**. The first start downloads kim+ from Arvato (about 250 MB) and takes a
   few minutes.
2. Open the app. The kim+ desktop shows a tray icon at the bottom right.
3. Tray icon → **Einstellungen → Konnektoren → Neuen Konnektor anlegen**: the practice Konnektor's host,
   Mandant, Clientsystem and Arbeitsplatz, and its client authentication.
4. **Einstellungen → TLS**: switch on TLS for POP3 and SMTP (off out of the box; easyTI connects with TLS).
5. Tray icon → **Adminmodul** opens Firefox inside the desktop. Accept the certificate warning once (kim+ uses
   its own certificate), then add the practice's KIM accounts.
6. In easyTI, point the KIM clientmodule at this device's IP with ports 10110 / 10025, and upload this
   Clientmodul's TLS certificate (Einstellungen → TLS).

## Updating

Umbrel shows an **Update** badge when a new version is out. A new version can bring a new kim+, which is
downloaded on the next start; the configuration and the KIM accounts stay.

## Data

`${APP_DATA_DIR}/data` holds kim+, its configuration and the keyring with its passwords. The keyring is
locked with the app's `APP_SEED`, so a backup of this folder is useless without the same Umbrel install.
