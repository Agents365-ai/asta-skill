# MCP 服务器注册

[English](INSTALL_MCP.md)

**先**注册 Asta MCP server,再安装技能本体。

## Claude Code

```bash
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

然后重启 Claude Code,MCP 工具会在会话启动时加载。

## Codex CLI

编辑 `~/.codex/config.toml`:

```toml
[mcp_servers.asta]
type = "http"
url = "https://asta-tools.allen.ai/mcp/v1"
headers = { "x-api-key" = "${ASTA_API_KEY}" }
```

## Cursor / Windsurf / Hermes / 其他 MCP 客户端

```json
{
  "mcpServers": {
    "asta": {
      "serverUrl": "https://asta-tools.allen.ai/mcp/v1",
      "headers": { "x-api-key": "<YOUR_API_KEY>" }
    }
  }
}
```

## LM Studio(手动模式)

LM Studio(0.3.17+)已支持 MCP,但不会自动加载 Agent Skills。两步即可使用:

1. **注册 MCP server** —— App Settings → Program → Integrations → 编辑 `mcp.json`:

    ```json
    {
      "mcpServers": {
        "asta": {
          "url": "https://asta-tools.allen.ai/mcp/v1",
          "headers": { "x-api-key": "YOUR_ASTA_API_KEY" }
        }
      }
    }
    ```

2. **手动注入技能指令** —— 把 [`skills/asta-skill/SKILL.md`](skills/asta-skill/SKILL.md) 的正文复制到聊天的 System Prompt,让模型按意图路由表和安全默认值调用工具。

需使用**支持 function calling 的本地模型**(如 Qwen2.5-Instruct、Llama 3.1 Instruct、Mistral Nemo、GPT-OSS),纯 chat 模型无法调用 MCP 工具。
