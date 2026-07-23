# MCP Shell Server

A Model Context Protocol (MCP) server that provides remote shell execution and file operations.

## Features

- **Shell Commands** - Execute shell commands on the remote server
- **Interactive Sessions** - Start, interact with, and manage long-running PTY sessions
- **File Operations** - Read and write files directly, bypassing shell encoding issues
- **Dual Transport** - Supports both SSE (Cline) and Streamable HTTP (Cursor) on the same port

## Tools

### Shell

| Tool | Description |
|------|-------------|
| run_command | Execute a shell command and return stdout/stderr |
| start_session | Start an interactive PTY session |
| send_input | Send input to a running session |
| read_output | Read output from a running session |
| list_sessions | List all active sessions |
| close_session | Close a session |
| start_background | Start a long-running command in background |

### File Operations

| Tool | Description |
|------|-------------|
| write_file | Write content directly to a file (no shell encoding issues) |
| read_file | Read content from a file |

## Why write_file and read_file?

The original run_command tool passes content through shell heredocs, which can break with special characters like backticks, dollar signs, quotes, and JSON. The write_file tool bypasses the shell entirely using Python built-in open().write(), making it safe for any content.

## Usage

### Claude Desktop / Cline

Add to your MCP configuration:

```json
{
  "mcpServers": {
    "shell-server": {
      "url": "http://your-server:6942/sse",
      "type": "sse"
    }
  }
}
```

### Cursor

```json
{
  "mcpServers": {
    "shell-server": {
      "url": "http://your-server:6942/mcp",
      "type": "streamable-http"
    }
  }
}
```

## Requirements

- Python 3.10+
- mcp, uvicorn, starlette

Install: pip install mcp uvicorn starlette

## Running

python shell_mcp.py

The server listens on 127.0.0.1:6942 by default.

## License

MIT
