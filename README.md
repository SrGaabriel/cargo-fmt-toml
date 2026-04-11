# cargo-fmt-toml

[![Crates.io](https://img.shields.io/crates/v/cargo-fmt-toml.svg)](https://crates.io/crates/cargo-fmt-toml)
[![Documentation](https://docs.rs/cargo-fmt-toml/badge.svg)](https://docs.rs/cargo-fmt-toml)
[![CI](https://github.com/dataroadinc/cargo-fmt-toml/workflows/CI%2FCD/badge.svg)](https://github.com/dataroadinc/cargo-fmt-toml/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/dataroadinc/cargo-fmt-toml/blob/main/LICENSE)
[![Downloads](https://img.shields.io/crates/d/cargo-fmt-toml.svg)](https://crates.io/crates/cargo-fmt-toml)

Cargo subcommand that **opinionatedly** formats and normalizes `Cargo.toml`
files: one canonical layout for top-level tables, `[package]` fields, and
dependency tables, plus workspace policy rules.

Only manifests for **workspace members** are considered, and only if they lie
under the **canonical workspace root** you pass (default: current directory).
If that directory is inside a **git** work tree, manifests must also stay
under `git rev-parse --show-toplevel` (so crates.io checkouts under
`~/.cargo/registry` are never touched).

## Opinionated layout

The formatter is meant to be a **policy enforcer**, not a minimal pretty-printer.

- **Top-level tables** — A fixed order for standard Cargo roots (`package`,
  target definitions, dependency tables, `features`, `target`, `workspace`,
  `patch`, `replace`, `profile`, `lints`, `badges`, `cargo-features`). Any
  **other** top-level key (extensions, tooling) is sorted **lexicographically**
  after that list so output is deterministic.
- **`[package]` fields** — A fixed order for common keys (`name`, `version`,
  `description`, … through `metadata`). Remaining keys sort **lexicographically**
  at the end of `[package]`.
- **Dependencies** — Keys in `dependencies`, `dev-dependencies`,
  `build-dependencies`, and target-specific dependency tables are sorted
  alphabetically.
- **Workspace rules** — Centralized versions and `workspace = true` for internal
  crates (see Features below).

The exact lists live in `src/main.rs` as `TOP_LEVEL_SECTION_ORDER` and
`PACKAGE_FIELD_ORDER`; change them there when the policy evolves.

## Installation

### Using cargo-binstall (recommended)

Pre-built binaries from GitHub Releases (see `[package.metadata.binstall]` in
`Cargo.toml`):

```bash
cargo install cargo-binstall
cargo binstall cargo-fmt-toml
```

### Using cargo install (crates.io)

```bash
cargo install cargo-fmt-toml
```

If the crate is not yet on crates.io, install from git:

```bash
cargo install --git https://github.com/dataroadinc/cargo-fmt-toml
```

### Network / offline Cargo config

If the project you run `cargo install` from sets `net.offline = true` in
`.cargo/config.toml`, either run the install from a directory without that
setting or override for one invocation, for example:

```bash
CARGO_NET_OFFLINE=false cargo install cargo-fmt-toml
```

## Features

1. **Canonical layout** — Opinionated top-level and `[package]` ordering;
   unknown keys sorted for stable diffs
2. **Workspace dependencies** — Versions belong in the workspace; member crates
   use `workspace = true`
3. **Internal path deps** — Workspace crates reference each other with
   `{ workspace = true }`
4. **Sorted dependency tables** — Alphabetical keys in dependency sections
5. **`[package]` field normalization** — Consistent key order and inline table
   style where the tool applies transforms

## Usage

```bash
# Format all Cargo.toml files in the workspace
cargo fmt-toml

# Preview changes without modifying files
cargo fmt-toml --dry-run

# Check if files need formatting (returns non-zero if changes
# needed)
cargo fmt-toml --check

# Explicit workspace root
cargo fmt-toml --workspace-path /path/to/repo
```

```bash
cargo fmt-toml --help
```

## Package section example

`[package]` keys are ordered per `PACKAGE_FIELD_ORDER` in `src/main.rs`
(identity and metadata first, then `metadata` and any other keys sorted at the
end). Workspace members often look like:

```toml
[package]
name = "crate-name"
version = { workspace = true }
description = "Brief description"
authors = { workspace = true }
edition = { workspace = true }
rust-version = { workspace = true }
license-file = { workspace = true }
readme = { workspace = true }
```

## Dependency Sorting

All dependency sections are sorted alphabetically:

- `[dependencies]`
- `[dev-dependencies]`
- `[build-dependencies]`
- `[target.'cfg(...)'.dependencies]`

## Integration

After `cargo install cargo-fmt-toml` (or `cargo binstall`):

```makefile
.PHONY: fmt-toml
fmt-toml:
	cargo fmt-toml

.PHONY: check-fmt-toml
check-fmt-toml:
	cargo fmt-toml --check
```
