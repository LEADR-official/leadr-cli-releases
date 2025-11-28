# leadr

A Rust-based CLI tool for testing and performing admin actions against the LEADR API. Designed for internal team use across different platforms and operating systems.

## Features

- **Admin Authentication**: Securely configure API keys for admin operations
- **Account Management**: Create, list, view, and delete accounts
- **Game Management**: Create, list, view, and delete games
- **Board Management**: Create, list, view, and delete leaderboards
- **User Management**: Create, list, view, and delete users
- **API Key Management**: Create, list, view, and revoke API keys for accounts
- **Client Authentication**: Start sessions and refresh tokens for testing
- Automatic token and credential management
- Support for both local development and production APIs
- Cross-platform support (macOS, Linux, Windows)

## Installation

### From source

#### Public Build (Admin-only)
```bash
cargo install --path .
```

#### Internal Build (Full features for dev use)
```bash
cargo install --path . --features internal
```

This will install the `leadr` binary to your cargo bin directory (typically `~/.cargo/bin/`).

### Build Variants

The CLI supports two build variants controlled by the `internal` feature flag:

**Public Build** (default) - Admin-only operations:
- Excludes client authentication commands (`auth client`)
- Excludes superadmin-only commands (`account create`, `account list`)
- Dual-auth commands (`board list`, `score create`, `score list`) require explicit `--account-id` parameter
- Defaults to production API (`https://api.leadr.gg`)

**Internal Build** (`--features internal`) - Full functionality:
- Includes all client authentication features
- Includes superadmin account management commands
- Dual-auth commands support both admin and client authentication modes
- Defaults to local API (`http://0.0.0.0:8000`)

## Usage

### Admin Authentication

Before performing admin operations, configure your API key (one-time setup):

```bash
leadr auth admin configure
```

You'll be prompted to enter your API key (input is hidden). The key is validated against the API and saved securely.

Check API key status:
```bash
leadr auth admin status
```

Clear saved API key:
```bash
leadr auth admin clear
```

### Account Management

Create a new account:
```bash
leadr account create --name "My Studio" --slug my-studio
```

List all accounts:
```bash
leadr account list
```

Get account details:
```bash
leadr account get --account-id <uuid>
```

Delete an account (soft delete):
```bash
leadr account delete --account-id <uuid>
```

### Game Management

Create a new game:
```bash
leadr game create --account-id <uuid> --name "My Game"
```

With optional parameters:
```bash
leadr game create --account-id <uuid> --name "My Game" --steam-app-id 123456 --anti-cheat-enabled true
```

List games for an account:
```bash
leadr game list --account-id <uuid>
```

Get game details:
```bash
leadr game get --game-id <uuid>
```

Delete a game (soft delete):
```bash
leadr game delete --game-id <uuid>
```

### Board Management

Create a new board (leaderboard):
```bash
leadr board create \
  --account-id <uuid> \
  --game-id <uuid> \
  --name "High Scores" \
  --icon "🏆" \
  --short-code "highscores" \
  --unit "points" \
  --sort-direction desc \
  --keep-strategy best
```

List boards for a game:
```bash
leadr board list --game-id <uuid>
```

Get board details by ID:
```bash
leadr board get --board-id <uuid>
```

Get board details by short code:
```bash
leadr board get-by-code --short-code "highscores"
```

Delete a board (soft delete):
```bash
leadr board delete --board-id <uuid>
```

### User Management

Create a new user:
```bash
leadr user create --account-id <uuid> --email "user@example.com" --display-name "John Doe"
```

List users for an account:
```bash
leadr user list --account-id <uuid>
```

Get user details:
```bash
leadr user get --user-id <uuid>
```

Delete a user (soft delete):
```bash
leadr user delete --user-id <uuid>
```

### API Key Management

Create a new API key:
```bash
leadr api-key create --account-id <uuid> --name "Production Key"
```

**Important**: The API key value is only shown once during creation. Save it immediately!

List API keys for an account:
```bash
leadr api-key list --account-id <uuid>
```

Get API key details:
```bash
leadr api-key get --key-id <uuid>
```

Revoke an API key (soft delete):
```bash
leadr api-key revoke --key-id <uuid>
```

### Client Authentication

Start a new client session:
```bash
leadr auth client start --game-id <uuid>
```

With optional parameters:
```bash
leadr auth client start --game-id <uuid> --device-id my-device --platform ios
```

Refresh access token:
```bash
leadr auth client refresh
```

### API Endpoints

The CLI uses different default API endpoints depending on the build variant:

**Public Build** (default):
- Defaults to production: `https://api.leadr.gg`
- Override with `--host` flag to use a different endpoint

**Internal Build** (`--features internal`):
- Defaults to local: `http://0.0.0.0:8000`
- Override with `--host` flag to use a different endpoint

```bash
# Public build examples
leadr account list                                    # Uses production (https://api.leadr.gg)
leadr --host http://0.0.0.0:8000 account list         # Uses local
leadr --host http://localhost:3000 account list       # Uses custom local port

# Internal build examples
leadr account list                                    # Uses local (http://0.0.0.0:8000)
leadr --host https://api.leadr.gg account list        # Uses production
leadr --host http://0.0.0.0:3000 account list         # Uses custom local port
```

### Configuration

Admin API keys, client tokens, and device credentials are stored in `~/.leadr/config.toml` with restrictive permissions (0600 on Unix systems).

## Development

### Building

Public build (admin-only):
```bash
cargo build
```

Internal build (full features):
```bash
cargo build --features internal
```

### Running

Public build:
```bash
cargo run -- account list
```

Internal build:
```bash
cargo run --features internal -- auth client start --game-id <uuid>
```

### Linting

Public build:
```bash
cargo clippy -- -W clippy::pedantic
```

Internal build:
```bash
cargo clippy --features internal -- -W clippy::pedantic
```

### Project Structure

```
src/
├── main.rs              # Entry point and CLI routing
├── cli.rs               # Command definitions (clap)
├── config.rs            # Config file management (~/.leadr/config.toml)
├── error.rs             # Error types
├── api/
│   ├── mod.rs
│   ├── client.rs        # HTTP client
│   ├── auth.rs          # Client auth API endpoints
│   ├── admin.rs         # Admin API endpoints (accounts, games, boards, users, API keys)
│   └── models.rs        # Request/response models
└── commands/
    ├── mod.rs
    ├── auth.rs          # Client auth command handlers
    ├── admin_auth.rs    # Admin auth command handlers
    ├── account.rs       # Account command handlers
    ├── game.rs          # Game command handlers
    ├── board.rs         # Board command handlers
    ├── user.rs          # User command handlers
    └── api_key.rs       # API key command handlers
```
