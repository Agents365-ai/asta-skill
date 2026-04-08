# asta-skill 🔭

通过 **Ai2 Asta MCP Server** 让任意支持 MCP 的 agent(Claude Code、Codex、Hermes、Cursor、Windsurf、OpenClaw 等)使用 Semantic Scholar 学术语料库。

- **MCP 端点:** `https://asta-tools.allen.ai/mcp/v1`
- **传输:** streamable HTTP
- **认证:** `x-api-key` header([申请 API key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm))

## 它是什么

`asta-skill` 是一个**零代码的 agent 技能包** —— 它本身不发请求,而是告诉 agent:

1. Asta MCP 提供了哪些工具
2. 不同用户意图该调用哪个工具
3. 如何把工具组合成 topic discovery / 引用扩展 / 作者深挖 / 证据检索等工作流
4. 如何在各个 host 里注册 Asta MCP server

## Asta 提供的工具

| 工具 | 用途 |
|---|---|
| `get_paper` | 按 DOI / arXiv / PMID / PMCID / CorpusId / MAG / ACL / SHA / URL 查论文 |
| `search_papers_by_relevance` | 关键词宽泛搜索(支持 venue + 日期过滤) |
| `search_paper_by_title` | 按标题查找 |
| `get_citations` | 谁引用了某篇论文 |
| `search_authors_by_name` | 按姓名找作者 |
| `get_author_papers` | 查作者的所有论文 |
| `snippet_search` | 从论文正文中检索 ~500 词的相关段落 |

## 安装

先申请 API key 并设置环境变量:

```bash
export ASTA_API_KEY="..."
```

### Claude Code

```bash
claude mcp add asta \
  --transport http \
  --url https://asta-tools.allen.ai/mcp/v1 \
  --header "x-api-key: $ASTA_API_KEY"
```

### Codex CLI

编辑 `~/.codex/config.toml`:

```toml
[mcp_servers.asta]
type = "http"
url = "https://asta-tools.allen.ai/mcp/v1"
headers = { "x-api-key" = "${ASTA_API_KEY}" }
```

### Cursor / Windsurf / Hermes / 其他 MCP 客户端

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

### 安装 skill 本体

| 平台 | 路径 |
|---|---|
| Claude Code(全局) | `~/.claude/skills/asta-skill/` |
| Claude Code(项目) | `.claude/skills/asta-skill/` |
| OpenClaw(全局) | `~/.openclaw/skills/asta-skill/` |
| OpenClaw(项目) | `skills/asta-skill/` |

把本仓库克隆或复制到上述任一路径即可。

## 验证

让 agent 执行:

> "用 Asta 查一下 DOI `10.48550/arXiv.1706.03762` 这篇论文。"

应返回 *Attention Is All You Need* 的元数据。

## 与 `semanticscholar-skill` 的区别

| | semanticscholar-skill | asta-skill |
|---|---|---|
| 传输 | Python + REST | MCP(streamable HTTP) |
| 运行依赖 | Python + `S2_API_KEY` | Host 支持 MCP |
| 适用场景 | 脚本化批处理、复杂过滤 | 零代码 agent 集成 |

两者共享 Semantic Scholar 语料库,使用哪个取决于 host 是否支持 MCP。

## License

MIT
