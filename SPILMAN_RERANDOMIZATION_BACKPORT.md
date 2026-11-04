# Spilman test-mint rerandomization backport

Base: CDK `v0.18.1` (`a056e0f0`). This branch carries the mint/Cashu,
SQLite/SQL-common database, and wallet struct-initializer changes from
cashubtc/cdk#2735 at `365959807dd93703abc65597fba73eac7d41ef57`.
It implements the draft cashubtc/nuts#463 transcript, including hashed original
outputs, not the earlier raw-point prototype.

The only conflict was subscription-test context: retain v0.18.1 subscription
behavior and add the new committed-randomness snapshot test, without importing
unrelated quote/subscription changes from development main.
The fork does not update dependency versions or introduce a general CDK upgrade.
PostgreSQL receives the common SQL migration/implementation but is not the
tested target; the Spilman test mint uses SQLite. FFI generation from the
development branch is outside this backport's supported/tested scope.

Validation on stable Rust (do not use `--all-features`, which enables Cashu's
nightly benchmark feature):

- `cargo test -p cashu --features mint,wallet --lib`
- `cargo test -p cdk --no-default-features --features mint,wallet --lib`
- `cargo test -p cdk-sqlite --features mint,wallet --lib`
- `cargo clippy -p cashu -p cdk -p cdk-sqlite --no-default-features --features mint,wallet --lib -- -D warnings`

All passed. Tests cover ordinary and opted-in signature domains, additive
output tweaks, deterministic infinity retry, staged saga failure and committed
recovery, capability advertisement, NUT-17 snapshots, and SQLite restart/migration.
Protocol support is experimental and opt-in per request. Ordinary requests
remain ordinary; channel-mode selection is separate Spilman work.
