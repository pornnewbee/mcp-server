# shell-mcp-server

MCP Shell Server v2 — an [MCP](https://modelcontextprotocol.io) (Model Context Protocol) server that exposes shell capabilities over HTTP.

Built with the MCP Python SDK v2 (`MCPServer`). Listens on `127.0.0.1:6942`.

## Features

- `run_command` — execute non-interactive shell commands and return stdout/stderr/exit code
- `start_session` / `send_input` / `read_output` / `close_session` — interactive PTY sessions
- `start_background` — background PTY processes
- `write_file` / `read_file` — direct file access (bypasses shell encoding issues)

## Endpoints

| Path       | Purpose                          |
|------------|----------------------------------|
| `/mcp`     | Streamable HTTP (modern MCP)     |
| `/sse`     | SSE (legacy MCP clients)         |
| `/messages`| SSE message endpoint            |
| `/health`  | Health check                    |

## Run

```bash
python3 shell_mcp.py
```

The provided systemd unit (`shell-mcp.service`) runs it as user `runner`:

```ini
[Service]
Type=simple
User=runner
WorkingDirectory=/home/runner
ExecStart=/home/runner/mcp-venv/bin/python /home/runner/shell_mcp.py
Environment=PYTHONUNBUFFERED=1
```

## Notable fix: `TIOCSWINSZ`

The PTY child in `_create_pty_session()` originally passed a hardcoded `0x5410`
to `fcntl.ioctl()`. On Linux `0x5410` is **`TIOCSPGRP`** (set foreground process
group), *not* `TIOCSWINSZ` (`0x5414`) as intended. The mismatched ioctl returned
`ENOTTY`, the exception was swallowed by `except Exception: os._exit(127)`, and
every interactive session died immediately (child became a zombie, sessions were
never visible to `list_sessions`). Fixed by using the `termios.TIOCSWINSZ`
constant.

## Logs

`/tmp/mcp_shell.log` (file logging) and journald (`journalctl -u shell-mcp`).
