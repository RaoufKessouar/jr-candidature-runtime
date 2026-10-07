# Routine

Minimal GitHub Actions launcher. The application code and runtime data are private.

Manual tasks:

- `preflight`: verify installation, offline tests and readonly access to the input repository.
- `sync`: restore private state, synchronize notifications and save an encrypted checkpoint.

The current foundation does not contain a browser worker and cannot submit applications.
Synchronization is disabled unless `SYNC_ENABLED` is set to `true` and private storage credentials
are configured. Scheduling will be added after manual verification; there is no active cron.

Required repository configuration:

- Variables: `CODE_REPOSITORY`, `CODE_REF` (approved full commit SHA), `JR_REPOSITORY`,
  `STATE_REPOSITORY`, `SYNC_ENABLED` (initially `false`). Optional `JR_REF` selects an
  operator-approved input revision; it defaults to `main` and is not a dispatch input.
- Secrets: `CODE_READ_SSH_KEY`, `JR_READ_SSH_KEY`, and, for synchronization,
  `STATE_TOKEN`, `STATE_KEY`.

Checkout deploy keys are readonly. `STATE_TOKEN` must be dedicated to the private storage
repository with Contents read/write permission. Do not use a personal administrator token.
The storage repository must be private and explicitly initialized before synchronization.

Public logs contain generic success/failure messages. Detailed process output is captured
temporarily on the runner and is not uploaded as a public artifact. No profile, CV, session,
capture, private source code or personal report belongs in this repository.
