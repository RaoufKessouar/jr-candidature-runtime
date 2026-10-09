# Routine

Minimal GitHub Actions launcher. The application code and runtime data are private.

Manual tasks:

- `preflight`: verify installation, offline tests and readonly access to the input repository.
- `sync`: restore private state, synchronize notifications and save an encrypted checkpoint.
- `transfer`: promote an encrypted staged handoff under the active run's lock, preserving the
  existing remote campaign control and feed history. Only an idle paused campaign is supported.
- `state-check`: restore and verify that idle paused state on a fresh runner, then checkpoint it.
- `update`: apply an encrypted, versioned human profile/answer update under the live run's lock.
- `mail-check`: verify the live OAuth client, exact mailbox and readonly scope, without reading
  message content or loading campaign state.
- `account-update`: save an encrypted account preparation or observed status under the active
  run's lock. This task does not create accounts on external websites.

These tasks do not run a browser or call a model and cannot submit applications.
Synchronization is disabled unless `SYNC_ENABLED` is set to `true` and private storage credentials
are configured. Scheduling will be added after manual verification; there is no active cron.

Required repository configuration:

- Variables: `CODE_REPOSITORY`, `CODE_REF` (approved full commit SHA), `JR_REPOSITORY`,
  `STATE_REPOSITORY`, `SYNC_ENABLED` (initially `false`). Optional `JR_REF` selects an
  operator-approved input revision; it defaults to `main` and is not a dispatch input.
  `HANDOFF_ASSET` identifies an opaque encrypted asset in private storage for `transfer`;
  it contains no candidate data and is not a dispatch input.
  `UPDATE_ASSET` similarly identifies an encrypted private human update for `update`.
  `ACCOUNT_ASSET` identifies an encrypted private account record for `account-update`.
- Secrets: `CODE_READ_SSH_KEY`, `JR_READ_SSH_KEY`, and, for synchronization,
  `STATE_TOKEN`, `STATE_KEY`.

Checkout deploy keys are readonly. `STATE_TOKEN` must be dedicated to the private storage
repository with Contents read/write permission. Do not use a personal administrator token.
The storage repository must be private and explicitly initialized before synchronization.
State maintenance requires approved private code supporting schema 4. The handoff freezes
the local ledger first; downloaded copies remain readonly, and only the live GitHub lock
owner can reserve budget or change runtime data. Never bootstrap an existing state again.
Maintenance does not read the JR repository and receives no model API key.
Updates require approved private code supporting `ProfileUpdate`; they do not resume the
campaign. Requests carry the exact ledger/handoff/runtime identity and expected profile version.
Replay receipts and profile changes commit together before the remote checkpoint is published.
`mail-check` requires private code supporting this task and the dedicated `GMAIL_ACCESS` secret.
It receives this credential only in its verification step, as `JRC_GMAIL_ACCESS`. The other
tasks receive no Gmail credential. Mail credentials are not included in campaign snapshots.
`account-update` requires approved private code supporting `AccountUpdate`. The record is bound
to the existing private account plan, candidate profile, ledger and account revision. Passwords
cannot be changed through this task; login credentials are released only for verified accounts.
It receives no model or mail credentials and does not change campaign mode or site policies.

Public logs contain generic success/failure messages. Detailed process output is captured
temporarily on the runner and is not uploaded as a public artifact. No profile, CV, session,
capture, private source code or personal report belongs in this repository.
