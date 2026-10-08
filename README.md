# Routine

Minimal GitHub Actions launcher. The application code and runtime data are private.

Manual tasks:

- `preflight`: verify installation, offline tests and readonly access to the input repository.
- `sync`: restore private state, synchronize notifications and save an encrypted checkpoint.
- `transfer`: promote an encrypted staged handoff under the active run's lock, preserving the
  existing remote campaign control and feed history. Only an idle paused campaign is supported.
- `state-check`: restore and verify that idle paused state on a fresh runner, then checkpoint it.

These tasks do not run a browser or call a model and cannot submit applications.
Synchronization is disabled unless `SYNC_ENABLED` is set to `true` and private storage credentials
are configured. Scheduling will be added after manual verification; there is no active cron.

Required repository configuration:

- Variables: `CODE_REPOSITORY`, `CODE_REF` (approved full commit SHA), `JR_REPOSITORY`,
  `STATE_REPOSITORY`, `SYNC_ENABLED` (initially `false`). Optional `JR_REF` selects an
  operator-approved input revision; it defaults to `main` and is not a dispatch input.
  `HANDOFF_ASSET` identifies an opaque encrypted asset in private storage for `transfer`;
  it contains no candidate data and is not a dispatch input.
- Secrets: `CODE_READ_SSH_KEY`, `JR_READ_SSH_KEY`, and, for synchronization,
  `STATE_TOKEN`, `STATE_KEY`.

Checkout deploy keys are readonly. `STATE_TOKEN` must be dedicated to the private storage
repository with Contents read/write permission. Do not use a personal administrator token.
The storage repository must be private and explicitly initialized before synchronization.
State maintenance requires approved private code supporting schema 4. The handoff freezes
the local ledger first; downloaded copies remain readonly, and only the live GitHub lock
owner can reserve budget or change runtime data. Never bootstrap an existing state again.
Maintenance does not read the JR repository and receives no model API key.

Public logs contain generic success/failure messages. Detailed process output is captured
temporarily on the runner and is not uploaded as a public artifact. No profile, CV, session,
capture, private source code or personal report belongs in this repository.
