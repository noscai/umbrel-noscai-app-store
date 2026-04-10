# ClinicOS Doctolib Sync

Bidirectional appointment sync between Doctolib and ClinicOS, running on your Umbrel device.

## First-time setup

1. Install **ClinicOS Doctolib Sync** from the Umbrel App Store
2. Open the app — you'll see a settings page with status "Waiting for configuration"
3. In **ClinicOS Admin**, go to Integrations > Doctolib > copy the Sync Token
4. Paste the token in the settings page and click **Save**
5. The app connects to ClinicOS, fetches credentials, and starts syncing automatically

```text
ClinicOS Admin          Umbrel App              Doctolib
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Generate     │     │ Paste token  │     │ Appointments │
│ Sync Token  ─┼──>  │ Click Save  ─┼──>  │ synced both  │
│              │     │ Auto-config  │     │ directions   │
└──────────────┘     └──────────────┘     └──────────────┘
```

## Updating

When a new version is available, Umbrel shows an **Update** badge on the app icon.

1. Open your Umbrel dashboard
2. Click the app
3. Click **Update**
4. The app restarts with the new version — no manual steps needed

Your Sync Token is preserved across updates.

## What it does

| Direction | How | What happens |
|-----------|-----|-------------|
| Doctolib > ClinicOS | Polls every 5 min | New/changed/cancelled bookings appear in ClinicOS |
| ClinicOS > Doctolib | On appointment create | Appointment is created in Doctolib, ID synced back |

## Troubleshooting

| Status | Meaning | Action |
|--------|---------|--------|
| Waiting for configuration | No Sync Token set | Open the app and paste your token |
| Running | Everything is working | Nothing to do |
| Error with "Connection refused" | ClinicOS server unreachable | Check that your ClinicOS instance is running |
| Error with "401" or "403" | Token expired or invalid | Generate a new token in ClinicOS Admin |
| Error with "2FA failed" | IMAP credentials incorrect | Update Doctolib IMAP settings in ClinicOS Admin |

## Architecture

The app runs entirely on your Umbrel device. Doctolib credentials and session data never leave the clinic network. Communication with ClinicOS happens via Google Cloud Pub/Sub.

For developer documentation, see the [doctolib-twin repository](https://github.com/noscai/doctolib-twin).
