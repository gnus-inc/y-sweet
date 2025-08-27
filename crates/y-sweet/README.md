<img src="https://raw.githubusercontent.com/drifting-in-space/y-sweet/main/logo.svg" />

# y-sweet: a Yjs server with persistence and auth

**y-sweet** is an open-source server for building realtime applications on top of the [Yjs](https://github.com/yjs/yjs) CRDT library.

## Features

- Persists document data to a network filesystem or S3-compatible storage, [inspired by Figma’s infrastructure](https://digest.browsertech.com/archive/browsertech-digest-figma-is-a-file-editor/).
- Scales horizontally with a [session backend](https://driftingin.space/posts/session-lived-application-backends) model.
- Deploys as a native Linux process, or as a WebAssembly module on Cloudflare's edge.
- Provides document-level access control via client tokens.
- Written in Rust with a focus on stability and performance, building on the excellent [y-crdt](https://github.com/y-crdt/y-crdt/) library.

## `y-sweet` crate

The y-sweet crate is primarily intended to be used as a binary, but can also be used as a library. See `main.rs` for usage examples.

## Configuration

### Environment Variables

The y-sweet server supports the following environment variables:

#### Basic Configuration

- `Y_SWEET_STORE`: Storage path or S3 URL (e.g., `/path/to/storage` or `s3://bucket-name/prefix`)
- `PORT`: Port number for the server to listen on (default: 8080)
- `Y_SWEET_HOST`: Host address for the server to bind to (default: 127.0.0.1)
- `Y_SWEET_CHECKPOINT_FREQ_SECONDS`: Checkpoint frequency in seconds (default: 1)
- `Y_SWEET_AUTH`: Secret key for authentication
- `Y_SWEET_URL_PREFIX`: URL prefix for the server
- `Y_SWEET_LOG`: Log level configuration (e.g., `info`, `debug`, `trace` or full filter specification)

#### S3 Configuration

- `AWS_ACCESS_KEY_ID`: S3 access key ID
- `AWS_SECRET_ACCESS_KEY`: S3 secret access key
- `AWS_SESSION_TOKEN`: S3 session token (for temporary credentials)
- `AWS_REGION`: S3 region (default: us-east-1)
- `AWS_ENDPOINT_URL_S3`: S3 endpoint URL
- `AWS_S3_USE_PATH_STYLE`: Whether to use path-style URLs (`true`/`false`)

#### OpenTelemetry APM Configuration

- `DD_TRACE_ENABLED`: Whether to enable OpenTelemetry APM (`true`/`false`)
- `DD_SERVICE`: Service name (default: y-sweet)
- `DD_ENV`: Environment name (default: development)
- `DD_VERSION`: Version
- `DD_TRACE_AGENT_URL`: Trace agent URL (default: http://localhost:14268/api/traces)

#### ServeDoc Command Specific

- `SESSION_BACKEND_KEY`: Session backend key
- `STORAGE_BUCKET`: Storage bucket name
- `STORAGE_PREFIX`: Storage prefix

### Command Line Arguments

#### `serve` Command

Starts the server.

```bash
y-sweet serve [OPTIONS]
```

**Options:**

- `--store <STORE>`: Storage path or S3 URL
- `--port <PORT>`: Port number (default: 8080)
- `--host <HOST>`: Host address
- `--checkpoint-freq-seconds <SECONDS>`: Checkpoint frequency in seconds
- `--auth <AUTH>`: Secret key for authentication
- `--url-prefix <URL>`: URL prefix
- `--prod`: Production mode (disables connection string logging)

#### `gen-auth` Command

Generates an authentication key pair.

```bash
y-sweet gen-auth [OPTIONS]
```

**Options:**

- `--json`: Output in JSON format

#### `convert-from-update` Command

Converts from YDoc v1 update format to a .ysweet file.

```bash
y-sweet convert-from-update <STORE> <DOC_ID>
```

**Arguments:**

- `STORE`: Target storage for writing
- `DOC_ID`: Document ID

#### `version` Command

Displays version information.

```bash
y-sweet version
```

#### `serve-doc` Command

Starts a server that serves only a specific document.

```bash
y-sweet serve-doc [OPTIONS]
```

**Options:**

- `--port <PORT>`: Port number (default: 8080)
- `--host <HOST>`: Host address
- `--checkpoint-freq-seconds <SECONDS>`: Checkpoint frequency in seconds (default: 10)

### Usage Examples

#### Basic Server Startup

```bash
# Using filesystem storage
Y_SWEET_STORE=/path/to/storage y-sweet serve

# Using S3 storage
Y_SWEET_STORE=s3://my-bucket/prefix AWS_ACCESS_KEY_ID=xxx AWS_SECRET_ACCESS_KEY=xxx y-sweet serve

# Enabling authentication
y-sweet gen-auth | grep "private_key" | cut -d' ' -f2 | xargs -I {} y-sweet serve --auth {}
```

#### Production Environment Startup

```bash
y-sweet serve --prod --auth YOUR_AUTH_KEY --store s3://your-bucket/prefix
```

#### Log Level Configuration

```bash
# Enable debug logging
Y_SWEET_LOG=debug y-sweet serve

# Set specific module log levels
Y_SWEET_LOG=y_sweet=info,y_sweet_core=info,hyper=warn y-sweet serve
```
