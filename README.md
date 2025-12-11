# LEADR CLI

Command-line tool for managing your LEADR account and leaderboards.

> [!TIP]
> Need some help? Give us a shout on Discord: https://discord.gg/RMUukcAxSZ

## Installation

### Quick Install (macOS/Windows)

```bash
curl -sSL https://raw.githubusercontent.com/LEADR-official/leadr-cli-releases/refs/tags/v0.3.0/install.sh | bash
```

### Manual Download

Download binaries from the [Releases page](https://github.com/LEADR-official/leadr-cli-releases/releases).

## Getting Started

### 1. Create Your Account

```bash
leadr register
```

### 2. Create a Game

```bash
leadr game create --name "My Game"
```

### 3. Create a Leaderboard

```bash
leadr board create --game-id <uuid> --name "High Scores" --short-code highscores
```

## Common Commands

| Command                           | Description                 |
| --------------------------------- | --------------------------- |
| `leadr help`                      | Show all available commands |
| `leadr <command> --help`          | Show help for a command     |
| `leadr game create`               | Create a new game           |
| `leadr game list`                 | List your games             |
| `leadr board create`              | Create a leaderboard        |
| `leadr board list --game-id <id>` | List boards for a game      |

## Documentation

For full documentation, visit [docs.leadr.gg](https://docs.leadr.gg)
