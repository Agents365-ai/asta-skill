# MCP Server Registration

[中文](INSTALL_MCP_CN.md)

Register the Asta MCP server with your host **before** installing the skill.

## Claude Code

```bash
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

Then restart Claude Code so the MCP tools load at session start.

## Codex CLI

Edit `~/.codex/config.toml`:

```toml
[mcp_servers.asta]
type = "http"
url = "https://asta-tools.allen.ai/mcp/v1"
headers = { "x-api-key" = "${ASTA_API_KEY}" }
```

## Cursor / Windsurf / Hermes / other MCP clients

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

## LM Studio (manual mode)

LM Studio (0.3.17+) speaks MCP but does not auto-discover Agent Skills. Use it in two steps:

1. **Register the MCP server** — App Settings → Program → Integrations → edit `mcp.json`:

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

2. **Paste the skill instructions** — copy the body of [`skills/asta-skill/SKILL.md`](skills/asta-skill/SKILL.md) into the chat's System Prompt so the model follows the intent routing and safe defaults.

Use a **tool-calling-capable** local model (e.g. Qwen2.5-Instruct, Llama 3.1 Instruct, Mistral Nemo, GPT-OSS). Plain chat models cannot invoke MCP tools.
