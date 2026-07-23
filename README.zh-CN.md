# MCP Shell Server

一个提供远程 Shell 执行和文件操作的 MCP 服务器。

## 功能

- **Shell 命令** - 在远程服务器上执行 Shell 命令
- **交互式会话** - 启动、交互和管理长时间运行的 PTY 会话
- **文件操作** - 直接读写文件，绕过 Shell 编码问题
- **双传输模式** - 同一端口同时支持 SSE（Cline）和 Streamable HTTP（Cursor）

## 工具列表

### Shell 工具

| 工具 | 说明 |
|------|------|
| run_command | 执行 Shell 命令并返回 stdout/stderr |
| start_session | 启动交互式 PTY 会话 |
| send_input | 向运行中的会话发送输入 |
| read_output | 读取运行中会话的输出 |
| list_sessions | 列出所有活跃会话 |
| close_session | 关闭会话 |
| start_background | 后台启动长时间运行的命令 |

### 文件操作工具

| 工具 | 说明 |
|------|------|
| write_file | 直接写入文件内容（无 Shell 编码问题） |
| read_file | 读取文件内容 |

## 为什么需要 write_file 和 read_file？

原有的 run_command 工具通过 Shell heredoc 传递内容，特殊字符（反引号、美元符号、引号、JSON 等）容易导致解析失败。write_file 工具使用 Python 内置的 open().write() 直接写入，完全绕过 Shell，适用于任何内容。

## 使用方法

### Claude Desktop / Cline

在 MCP 配置中添加：

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

## 环境要求

- Python 3.10+
- mcp, uvicorn, starlette

安装：pip install mcp uvicorn starlette

## 运行

python shell_mcp.py

默认监听 127.0.0.1:6942。

## 许可证

MIT
