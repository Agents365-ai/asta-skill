# asta-skill — 通过 Ai2 Asta MCP 访问 Semantic Scholar 🔭

[English](README.md) | [Asta MCP 介绍](https://allenai.org/asta/resources/mcp) | [申请 API Key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

## 功能特性

- **搜索** Semantic Scholar 学术语料库,支持关键词、标题、作者、全文片段多种检索方式
- **查论文** —— 支持 DOI、arXiv、PMID、PMCID、CorpusId、MAG、ACL、SHA、URL 等任意 ID 格式
- **引用遍历** —— 查找某篇论文被谁引用,支持过滤与分页
- **批量查找** —— 通过 `get_paper_batch` 一次查询多篇论文
- **片段检索** —— 从论文正文中提取 ~500 词的相关段落,用于证据溯源
- **作者检索** —— 查找研究者并列出其发表论文
- **零代码集成** —— 本技能是纯指令包,所有 I/O 通过 Asta MCP server 完成
- 当用户提出论文、引用、学术搜索、文献发现相关需求且 Asta 工具已注册时自动触发

## 多平台支持

兼容所有支持 MCP 以及 [Agent Skills](https://agentskills.io) 格式的 AI 编程助手:

| 平台 | 状态 | 说明 |
|------|------|------|
| **Claude Code** | ✅ 完全支持 | 原生 SKILL.md + `claude mcp add` 注册 |
| **Codex** | ✅ 完全支持 | 在 `~/.codex/config.toml` 中配置 MCP |
| **Cursor / Windsurf / Hermes** | ✅ 完全支持 | 标准 `mcpServers` JSON 配置 |
| **OpenClaw** | ✅ 完全支持 | `metadata.openclaw` 命名空间 + MCP 配置 |
| **SkillsMP** | ✅ 已收录 | GitHub topics 已配置 |

## 对比

### vs. `semanticscholar-skill`(REST 版姊妹技能)

| 能力 | `semanticscholar-skill` | `asta-skill` |
|---|---|---|
| 传输方式 | Python + 直连 REST(`s2.py`) | MCP(streamable HTTP) |
| 运行依赖 | Python + `S2_API_KEY` | Host 支持 MCP 即可 |
| 认证变量 | `S2_API_KEY` | `ASTA_API_KEY`(`x-api-key` 头) |
| 最佳场景 | 脚本化批处理、复杂过滤 | 零代码 agent 集成 |
| Cursor / Windsurf 开箱即用 | ❌ | ✅ |

### vs. 原生 Agent(无技能)

| 能力 | 原生 Agent | 本技能 |
|---|---|---|
| 知道 Asta 端点和 `x-api-key` 头 | ❌ | ✅ |
| 意图 → 工具决策表 | ❌ | ✅ |
| 工作流模板(发现 / 种子扩展 / 作者 / 证据) | ❌ | ✅ |
| 警告避免 `fields=citations` 炸上下文 | ❌ | ✅ |
| 各 MCP host 安装配方 | ❌ | ✅ |

## 前置条件

- 任意支持 MCP 的 agent host(Claude Code、Codex、Cursor、Windsurf、OpenClaw 等)
- Asta API key —— [点此申请](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## MCP 服务器注册

**先**注册 Asta MCP server,再安装技能本体。

### Claude Code

```bash
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

然后重启 Claude Code,MCP 工具会在会话启动时加载。

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

## 技能安装

### Claude Code

```bash
# 全局安装(所有项目可用)
git clone https://github.com/Agents365-ai/asta-skill.git ~/.claude/skills/asta-skill

# 项目级安装
git clone https://github.com/Agents365-ai/asta-skill.git .claude/skills/asta-skill
```

### Codex

```bash
git clone https://github.com/Agents365-ai/asta-skill.git ~/.codex/skills/asta-skill
```

### OpenClaw

```bash
git clone https://github.com/Agents365-ai/asta-skill.git ~/.openclaw/skills/asta-skill

# 项目级
git clone https://github.com/Agents365-ai/asta-skill.git skills/asta-skill
```

### SkillsMP

```bash
skills install asta-skill
```

### 安装路径汇总

| 平台 | 全局路径 | 项目路径 |
|------|---------|---------|
| Claude Code | `~/.claude/skills/asta-skill/` | `.claude/skills/asta-skill/` |
| Codex | `~/.codex/skills/asta-skill/` | 暂无 |
| OpenClaw | `~/.openclaw/skills/asta-skill/` | `skills/asta-skill/` |
| SkillsMP | 暂无(CLI 安装) | 暂无 |

## 使用方式

直接用自然语言描述需求即可:

```
> 用 Asta 查一下 DOI 10.48550/arXiv.1706.03762 这篇论文

> 在 Asta 上搜索 2023 年以来 NeurIPS 的 mixture-of-experts 论文

> "Attention Is All You Need" 被哪些论文引用?按引用数排前 20

> 在 Asta 语料库中查找提到 "flash attention latency" 的段落

> 在 Asta 上找 Yann LeCun,列出他 2024 年的论文
```

技能会选对 Asta 工具、附上安全的 `fields` 参数,并遵循文档中的工作流模板。

## Asta 提供的工具

| 工具 | 用途 |
|---|---|
| `get_paper` | 按任意支持的 ID 查单篇论文 |
| `get_paper_batch` | 一次批量查询多篇论文 |
| `search_papers_by_relevance` | 关键词宽泛搜索,支持 venue + 日期过滤 |
| `search_paper_by_title` | 按标题查找 |
| `get_citations` | 分页式引用遍历 |
| `search_authors_by_name` | 作者资料搜索 |
| `get_author_papers` | 查作者的全部论文 |
| `snippet_search` | 从论文正文检索 ~500 词段落 |

## 文件

- `SKILL.md` —— **唯一必需文件**。所有 host 都以此作为技能指令加载
- `README.md` —— 英文文档(GitHub 主页显示)
- `README_CN.md` —— 本文件(中文文档)

## 验证

注册好 MCP server 并重启 host 后,向 agent 提问:

> "用 Asta 查论文 ARXIV:1706.03762,字段要 title,year,authors,venue,tldr"

成功调用应返回 *Attention Is All You Need*,NeurIPS 2017,Vaswani 等人,含 TLDR。

## 常见问题

### Asta 本身就是 MCP server，为什么还需要这个 skill？

MCP server 给 agent 提供的是原始**工具**（函数名 + 参数 schema）。Skill 给 agent 提供的是正确使用这些工具的**领域知识**。没有 skill，agent 每次会话都要从零开始摸索：

| 层级 | 提供的内容 |
|------|-----------|
| **MCP server** | 8 个可调用的工具及其输入输出 schema |
| **本技能** | 意图路由、安全默认值、工作流模板、陷阱警告 |

具体来说，skill 额外提供了：

1. **意图 → 工具映射** —— 用户说"搜某主题的论文"和"谁引用了这篇"该调哪个工具
2. **上下文溢出保护** —— 警告 agent 永远不要请求 `fields=citations`（一篇高被引论文返回 20 万+字符）
3. **多步工作流模板** —— 主题发现、种子论文扩展、作者深挖、证据检索
4. **并行批处理指导** —— 优先用 `get_paper_batch` 而非 N 次串行 `get_paper`
5. **安全的 `fields` 默认值** —— 精选字段列表，防止上下文爆炸
6. **统一的输出格式** —— 表格、计数、后续操作菜单

类比：API 文档 vs. API 本身 —— schema 告诉 agent **能做什么**，skill 告诉它**怎么做才聪明**。

## 已知限制

- **`fields=citations` / `fields=references` 会炸上下文** —— 一篇高被引论文的返回可达 20 万字符。请改用 `get_citations` 工具(分页)。SKILL.md 中已明确警告
- **生产使用必须配 API key** —— 未认证访问的速率限制非常严格
- **作者消歧** —— 常见姓名会重名,调用 `get_author_papers` 前务必先用 `search_authors_by_name` 核对单位
- **MCP 在会话启动时加载** —— 会话中途注册 server 后必须重启 host 才能看到工具
- **摘要不一定有** —— 并非所有论文都有完整摘要;可用 `snippet_search` 或 `tldr` 作为 fallback

## License

MIT

## 支持

如果这个技能对你有帮助,欢迎打赏支持作者:

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/wechat-pay.png" width="180" alt="微信支付">
      <br>
      <b>微信支付</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/alipay.png" width="180" alt="支付宝">
      <br>
      <b>支付宝</b>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/buymeacoffee.png" width="180" alt="Buy Me a Coffee">
      <br>
      <b>Buy Me a Coffee</b>
    </td>
  </tr>
</table>

## 作者

**Agents365-ai**

- Bilibili: https://space.bilibili.com/441831884
- GitHub: https://github.com/Agents365-ai
