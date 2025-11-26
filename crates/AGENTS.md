# Repository Guidelines

## Project Structure & Module Organization
The workspace `Cargo.toml` groups the `y-sweet` service crate and the reusable `y-sweet-core` library. `y-sweet/src` holds the CLI entrypoints (`main.rs`, `cli.rs`, `server.rs`) plus storage adapters under `stores/` and top-level tests in `tests.rs`. Shared persistence, sync, and auth logic lives in `y-sweet-core/src/{doc_sync,store,sync}`. Sample fixtures in `data/` back local development, while `localstack/` spins up the S3 emulator consumed by the Docker setup.

## Build, Test, and Development Commands
- `cargo build` (or `make build`) compiles the full workspace.
- `cargo test` runs unit tests; target a crate with `cargo test -p y-sweet-core`.
- `cargo fmt` (or `make format`) enforces Rust style before committing.
- `make http.dev` launches the dev server with `cargo watch`, auto-reloading on file changes.
- `make http.dev.s3` or `docker-compose up --build` boot the HTTP server against the bundled LocalStack S3.

## Coding Style & Naming Conventions
Rust 2021 defaults apply: four-space indentation, `snake_case` for modules and functions, `PascalCase` for types, and `SCREAMING_SNAKE_CASE` for constants. Run `cargo fmt` prior to commits, and favor `cargo clippy --all-targets --all-features` to catch lint issues. Mirror existing module boundaries (e.g., authentication in `auth.rs`) and keep public API surface documented with `///` comments when it is consumed cross-crate.

## Testing Guidelines
Prefer small, deterministic tests colocated with code (`y-sweet/src/tests.rs`) or inside each crate’s `tests` modules. Use `#[tokio::test]` for async scenarios and assert on both content and side effects, especially around S3 stores and document sync flows. When adding new functionality, extend fixture data under `data/` if runtime artifacts are required, and ensure CI-critical commands like `cargo test --all-features` continue to pass.

## Commit & Pull Request Guidelines
Follow the repository’s conventional commits practice (`feat:`, `fix:`, etc.) and keep scopes meaningful (e.g., `feat: sync kv`), referencing issues or PR numbers when available. PRs should summarize behavior changes, list manual verification steps (CLI run, docker-compose, tests), and attach screenshots or logs for user-facing tweaks. Call out migrations or configuration changes so reviewers can coordinate deploy scripts.

## Local S3 & Configuration Tips
Use the provided `docker-compose.yml` to mirror production: it mounts `./data` and provisions LocalStack with `AWS_ENDPOINT_URL_S3`. Export matching credentials (`AWS_ACCESS_KEY_ID=test`, `AWS_SECRET_ACCESS_KEY=test`) when hitting the dev server outside Docker. Keep secrets out of the repo; rely on `.env` or local shell exports during development.
