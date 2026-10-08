# ClinicOS Export Upload — controlled pilot

This entry requires the `migration-uploader:0.1.0` image to be published after
review and the cloud rollout gates to pass. Do not advertise it before then.
The Umbrel app proxy keeps its default login requirement; there is no host port
or alternate unauthenticated listener.

Before installation, the operator creates `${APP_DATA_DIR}/data` owned by UID/GID
1000 with mode 0700, and a readable `${APP_DATA_DIR}/export` directory. Alternatively
set `APP_MIGRATION_EXPORT_DIR` to an already mounted, narrowly scoped export
directory. The mount is read-only and must exist: Compose must not silently create
an empty directory when the external disk is missing. Never mount the host root,
Docker socket, home directory, or an entire shared patient-data volume.

Place one finished, closed export file in that directory. Do not copy 100 GB into
app storage; point the read-only mount at the existing file's directory. UID 1000
must be able to traverse the directory and read the file. Source symlinks and
special files are rejected. Check the app proxy's forwarded Host/Origin on the
pilot hardware; add only its exact LAN hostname/IP to the allowed host/origin
settings if the appliance is accessed differently.

In Backoffice → Migrations → Umbrel file intake, choose the intended practice and
create an authorization. In the Umbrel app, enter the code, check the verified
practice name, then choose the file. Claim codes expire after 24 hours; transfers
expire after 29 days and can be cleaned up after 48 hours without verified progress.
The default upload ceiling is 10 MiB/s, adjustable downwards in the app. Pause and
cancel never delete the original. Keep the original until separately agreed.

Persist `/data` across upgrades and reboots. It contains the private capability
and SQLite journal: do not copy it into support tickets, logs, backups with broad
access, or browser storage. Changing this directory loses the resumable identity.
The app does not need AWS credentials or ClinicOS admin credentials.

Use the pilot and rollback checklist in `clinicos-edge/apps/migration-uploader/README.md`.
