# cargo-fmt-toml

[![Crates.io](https://img.shields.io/crates/v/cargo-fmt-toml.svg)](https://crates.io/crates/cargo-fmt-toml)
[![Documentation](https://docs.rs/cargo-fmt-toml/badge.svg)](https://docs.rs/cargo-fmt-toml)
[![CI](https://github.com/dataroadinc/cargo-fmt-toml/workflows/CI%2FCD/badge.svg)](https://github.com/dataroadinc/cargo-fmt-toml/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/dataroadinc/cargo-fmt-toml/blob/main/LICENSE)
[![Downloads](https://img.shields.io/crates/d/cargo-fmt-toml.svg)](https://crates.io/crates/cargo-fmt-toml)

Cargo subcommand to format and normalize `Cargo.toml` files according
to workspace standards.

Only manifests for **workspace members** are considered, and only if they lie
under the **canonical workspace root** you pass (default: current directory).
If that directory is inside a **git** work tree, manifests must also stay
under `git rev-parse --show-toplevel` (so crates.io checkouts under
`~/.cargo/registry` are never touched).

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

1. **Workspace Dependencies**: Ensures all dependency versions are
   managed at workspace level
2. **Internal Dependencies**: All workspace crates use
   `{ workspace = true }` for consistency
3. **Sorted Dependencies**: All dependency sections are sorted
   alphabetically by name
4. **Package Section Format**: Enforces a consistent `[package]`
   section format

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

## Package Section Format

The tool enforces this exact format for the `[package]` section:

```toml
[package]
name = "crate-name"
description = "Brief description"
version = { workspace = true }
edition = { workspace = true }
license-file = { workspace = true }
authors = { workspace = true }
rust-version = { workspace = true }
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
