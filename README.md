<p align="center">
  <img src="assets/screenshot.png" alt="Free CC" width="720" />
</p>

<h1 align="center">Free CC</h1>

<p align="center">
  <strong>Unleash your full power with Claude Code.</strong><br>
  Multi-provider support. Remote web access. OpenAI integration. All experimental features unlocked.<br>
  One binary, unlimited possibilities.
</p>

<p align="center">
  <a href="#quick-install"><img src="https://img.shields.io/badge/install-one--liner-blue?style=flat-square" alt="Install" /></a>
  <a href="https://github.com/chat812/freecc/stargazers"><img src="https://img.shields.io/github/stars/chat812/freecc?style=flat-square" alt="Stars" /></a>
  <a href="https://github.com/chat812/freecc/issues"><img src="https://img.shields.io/github/issues/chat812/freecc?style=flat-square" alt="Issues" /></a>
  <a href="https://github.com/chat812/freecc/blob/main/FEATURES.md"><img src="https://img.shields.io/badge/features-88%20flags-orange?style=flat-square" alt="Feature Flags" /></a>
</p>

---

## What's New in Free CC

Free CC extends Claude Code with features that aren't available in the official release:

- **/provider** — Switch between Anthropic, OpenAI, Bedrock, Vertex, and Foundry with a single command
- **/remote-connect** — Control your CLI session from any web browser via a self-hosted relay server
- **OpenAI Codex OAuth + API key** — Use ChatGPT Plus/Pro or your own OpenAI API key with custom base URL
- **Provider persistence** — Your provider choice survives restarts
- **Client pairing** — New clients request access from the server admin, no key sharing needed
- **Separate config** — Uses `~/.freecc/` instead of `~/.claude/`, runs side-by-side with Claude Code

---

## Quick Install

```bash
git clone https://github.com/chat812/freecc.git
cd freecc
bun install
bun run build
./freecc
```

Then use `/login` to authenticate with your preferred provider, or `/provider` to switch.

---

## Table of Contents

- [What's New in Free CC](#whats-new-in-free-cc)
- [Provider Switching](#provider-switching)
- [Remote Web Access](#remote-web-access)
- [Model Providers](#model-providers)
- [Build](#build)
- [Experimental Features](#experimental-features)
- [Project Structure](#project-structure)
- [License](#license)

---

## Provider Switching

Use the `/provider` command to switch API providers interactively:

```
/provider
```

Select from:
- **Anthropic (First-Party)** — Direct Anthropic API
- **AWS Bedrock** — Amazon Bedrock
- **Google Vertex AI** — Google Cloud Vertex AI
- **Microsoft Foundry** — Azure Foundry
- **OpenAI** — OpenAI Codex OAuth or API key

Your choice is persisted to `~/.freecc.json` and restored on next startup.

### OpenAI Provider

When selecting OpenAI, you get three options:
1. **Use existing account** — If already configured
2. **Login with OpenAI OAuth** — Sign in with ChatGPT Plus/Pro subscription
3. **Use API key** — Enter an OpenAI API key and optional base URL (for compatible APIs)

---

## Remote Web Access

Control your Free CC session from any web browser using a self-hosted relay server.

### Architecture

```
Free CC CLI ←──WebSocket──→ Relay Server ←──WebSocket──→ Web Browser
                                 │
                              /admin
                          (dashboard)
```

### Setup the Server

**Quick install (binary):**

```bash
curl -fsSL https://raw.githubusercontent.com/chat812/freecc-relay/main/install.sh | sudo bash
```

**Or download the binary directly** from [Releases](https://github.com/chat812/freecc-relay/releases):

```bash
# Linux amd64
wget https://github.com/chat812/freecc-relay/releases/latest/download/freecc-relay-linux-amd64
chmod +x freecc-relay-linux-amd64
./freecc-relay-linux-amd64 --port 8081
```

**Or build from source (Rust):**

```bash
git clone https://github.com/chat812/freecc-relay.git
cd freecc-relay
cargo build --release
./target/release/freecc-relay --port 8081
```

The server prints a client key and admin password on startup.

### Connect from Free CC

```
/remote-connect
```

1. Enter the server URL (e.g. `http://your-server:8081`)
2. Choose **Request access** (sends pairing request to admin) or **Enter client key**
3. If pairing: wait for admin approval at the server's `/admin` dashboard
4. Connected — share the session URL to control from any browser

### Web UI Features

- Markdown rendering (headings, tables, code blocks, lists)
- Collapsible tool output (shows tool name + args, click to expand)
- Real-time message forwarding (CLI responses appear on web)
- Auto-reconnect on disconnect

### Server Features

- Admin dashboard with session management (`/admin`)
- Password-protected admin (regenerated each restart)
- Kill individual/bulk/all sessions
- Cleanup old sessions by age
- Client pairing with approve/reject flow
- Session persistence across server restarts
- HTTPS/WSS support (`--tls-cert` and `--tls-key`)
- Systemd service for auto-start on boot

### Server Admin

```bash
# View logs
journalctl -u freecc-relay -f

# Get admin password
journalctl -u freecc-relay | grep "Admin password"

# Generate additional client keys
freecc-relay --generate-key alice
```

---

## Model Providers

### Anthropic (Direct API) — Default

| Model | ID |
|---|---|
| Claude Opus 4.6 | `claude-opus-4-6` |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` |
| Claude Haiku 4.5 | `claude-haiku-4-5` |

### OpenAI Codex

| Model | ID |
|---|---|
| GPT-5.2 Codex | `gpt-5.2-codex` |
| GPT-5.1 Codex | `gpt-5.1-codex` |
| GPT-5.4 | `gpt-5.4` |

### AWS Bedrock

```bash
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION="us-east-1"
./freecc
```

### Google Cloud Vertex AI

```bash
export CLAUDE_CODE_USE_VERTEX=1
./freecc
```

### Anthropic Foundry

```bash
export CLAUDE_CODE_USE_FOUNDRY=1
export ANTHROPIC_FOUNDRY_API_KEY="..."
./freecc
```

### Provider Summary

| Provider | Env Variable | Auth Method |
|---|---|---|
| Anthropic (default) | — | `ANTHROPIC_API_KEY` or OAuth |
| OpenAI | `CLAUDE_CODE_USE_OPENAI=1` | OAuth or API key via `/provider` |
| AWS Bedrock | `CLAUDE_CODE_USE_BEDROCK=1` | AWS credentials |
| Google Vertex AI | `CLAUDE_CODE_USE_VERTEX=1` | `gcloud` ADC |
| Anthropic Foundry | `CLAUDE_CODE_USE_FOUNDRY=1` | `ANTHROPIC_FOUNDRY_API_KEY` |

> Tip: Use `/provider` instead of env vars — it persists your choice automatically.

---

## Requirements

- **Runtime**: [Bun](https://bun.sh) >= 1.3.11
- **OS**: macOS, Linux, or Windows
- **Auth**: An API key or OAuth login for your chosen provider

---

## Build

```bash
git clone https://github.com/chat812/freecc.git
cd freecc
bun install
bun run build
./freecc
```

### Build Variants

| Command | Output | Description |
|---|---|---|
| `bun run build` | `./freecc` | Production build |
| `bun run build:dev` | `./freecc-dev` | Dev build |
| `bun run build:dev:full` | `./freecc-dev` | All 54 experimental flags |
| `bun run compile` | `./dist/freecc` | Alternative output path |
| `bun run dev` | (source) | Run from source |

---

## Experimental Features

The `bun run build:dev:full` build enables all 54 working feature flags. Highlights:

| Flag | Description |
|---|---|
| `ULTRAPLAN` | Remote multi-agent planning |
| `VOICE_MODE` | Push-to-talk voice input |
| `BRIDGE_MODE` | IDE remote-control bridge |
| `AGENT_TRIGGERS` | Local cron/trigger tools |
| `EXTRACT_MEMORIES` | Automatic memory extraction |
| `TEAMMEM` | Team-memory files |

See [FEATURES.md](FEATURES.md) for the complete audit of all 88 flags.

---

## Project Structure

```
scripts/
  build.ts                    # Build script with feature flags

src/
  entrypoints/cli.tsx          # CLI entrypoint
  commands.ts                  # Command registry
  screens/REPL.tsx             # Main interactive UI

  commands/
    provider/                  # /provider command
    remote-connect/            # /remote-connect command
  remote-server/
    client.ts                  # WebSocket relay client
    relay.ts                   # Message forwarding singleton
  plugins/
    bundled/remote-relay/      # Remote relay builtin plugin
  services/api/
    codex-fetch-adapter.ts     # OpenAI Codex + API key adapter
```

---

## Configuration

Free CC uses `~/.freecc/` for all configuration, separate from Claude Code's `~/.claude/`.

| File | Purpose |
|---|---|
| `~/.freecc.json` | Global config (provider, theme, keys) |
| `~/.freecc/settings.json` | User settings |
| `~/.freecc/projects/` | Session transcripts |
| `~/.freecc/plugins/` | Installed plugins |

---

## Related

- [freecc-relay](https://github.com/chat812/freecc-relay) — Self-hosted relay server for remote web access

---

## License

MIT. The original Claude Code source is the property of Anthropic.
