# Coding Style & Naming Conventions

## General Style
- **Edition**: Rust 2021
- **Indentation**: 4 spaces (Rust default)
- **Line Length**: Follow rustfmt defaults
- **Formatting Tool**: `cargo fmt` (rustfmt) - run before committing
- **Linting**: `cargo clippy --all-targets --all-features` - catch lint issues

## Naming Conventions
- **Modules & Functions**: `snake_case`
- **Types (Structs, Enums, Traits)**: `PascalCase`
- **Constants**: `SCREAMING_SNAKE_CASE`
- **Examples from codebase**:
  - Constants: `DEFAULT_S3_REGION`, `VERSION`, `S3_ACCESS_KEY_ID`
  - Structs: `Opts`, `ServSubcommand`
  - Functions: `parse_s3_config_from_env_and_args`, `get_store_from_opts`, `init_tracing`

## Documentation
- Use `///` comments for public API documentation, especially for cross-crate consumption
- Document behavior changes in commit messages and PR descriptions
- Keep module boundaries clear (e.g., authentication in auth.rs)

## Code Organization
- Mirror existing module boundaries
- Keep public API surface minimal and well-documented
- Prefer colocating tests with code (tests.rs or tests modules)
- Use `#[tokio::test]` for async test scenarios

## Dependencies
- Favor async/await patterns with Tokio
- Use tracing for structured logging
- Leverage serde for serialization needs
- Follow established patterns for error handling (anyhow, thiserror)
