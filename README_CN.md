# asta-skill — 通过 Ai2 Asta MCP 访问 Semantic Scholar

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/Agents365-ai/asta-skill?style=flat&logo=github)](https://github.com/Agents365-ai/asta-skill/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/Agents365-ai/asta-skill?style=flat&logo=github)](https://github.com/Agents365-ai/asta-skill/network/members)
[![Latest Release](https://img.shields.io/github/v/release/Agents365-ai/asta-skill?logo=github)](https://github.com/Agents365-ai/asta-skill/releases/latest)
[![Last Commit](https://img.shields.io/github/last-commit/Agents365-ai/asta-skill?logo=github)](https://github.com/Agents365-ai/asta-skill/commits/main)

[![SkillsMP](https://img.shields.io/badge/SkillsMP-listed-1f6feb)](https://skillsmp.com/skills/agents365-ai-asta-skill-skills-asta-skill-skill-md)
[![ClawHub](https://img.shields.io/badge/ClawHub-listed-ff6b35)](https://clawhub.ai/agents365-ai/asta-pro-skill)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-plugin-8a2be2)](https://github.com/Agents365-ai/365-skills)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-compatible-2ea44f)](https://agentskills.io)
[![Discord](https://img.shields.io/badge/Discord-Join-5865F2?logo=discord&logoColor=white)](https://discord.gg/79JF5Atuk)

[English](README.md) · **中文** · [Asta MCP 介绍](https://allenai.org/asta/resources/mcp) · [申请 API Key](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

纯指令包技能,将自然语言研究问题转换为对 Ai2 Asta MCP server(Semantic Scholar)的规范调用。负责意图路由 → 工具选择、安全默认值填充、工作流编排,并提示常见陷阱。兼容 **Claude Code、Codex、Cursor、Windsurf、Hermes、opencode、OpenClaw、pi-mono** 以及任何符合 [Agent Skills](https://agentskills.io) 规范的 agent。

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

兼容所有支持 MCP 并能加载 [Agent Skills](https://agentskills.io) 的 host —— 已在 **Claude Code、Codex、Cursor、Windsurf、Hermes、opencode、OpenClaw/ClawHub、[pi-mono](https://github.com/badlogic/pi-mono)** 上验证,并收录于 **SkillsMP**。**LM Studio**(0.3.17+)支持 MCP 但不自动加载 skills,需手动将 `SKILL.md` 粘贴到 system prompt(见下方 [LM Studio(手动模式)](#lm-studio手动模式))。

## 前置条件

- 任意支持 MCP 的 agent host(Claude Code、Codex、Cursor、Windsurf、opencode、OpenClaw/ClawHub、pi-mono 等)
- Asta API key —— [点此申请](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## 安装

两步 —— 先注册 MCP server,再把技能加载到 host。

### 第 1 步：注册 Asta MCP 服务器

**先**注册 Asta MCP server,再安装技能本体。

#### Claude Code

```bash
claude mcp add -t http -s user asta https://asta-tools.allen.ai/mcp/v1 \
  -H "x-api-key: $ASTA_API_KEY"
```

然后重启 Claude Code,MCP 工具会在会话启动时加载。

#### Codex CLI

编辑 `~/.codex/config.toml`:

```toml
[mcp_servers.asta]
type = "http"
url = "https://asta-tools.allen.ai/mcp/v1"
headers = { "x-api-key" = "${ASTA_API_KEY}" }
```

#### Cursor / Windsurf / Hermes / 其他 MCP 客户端

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

#### LM Studio(手动模式)

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

### 第 2 步：安装技能

技能正文位于仓库内的 `skills/asta-skill/SKILL.md`。最简单的安装方式是通过插件市场。

#### 插件市场(推荐)

```bash
# 任意 agent(Claude Code、Cursor、Copilot 等)
npx skills add Agents365-ai/365-skills -g

# 仅 Claude Code
/plugin marketplace add Agents365-ai/365-skills
/plugin install asta
```

同时收录于 [SkillsMP](https://skillsmp.com/) 与 [ClawHub](https://clawhub.ai/) —— 各自通过自己的市场处理更新。

#### 手动克隆(任意 host)

```bash
git clone https://github.com/Agents365-ai/asta-skill.git /tmp/asta-skill
cp -r /tmp/asta-skill/skills/asta-skill <你的-host-的-skills-目录>/asta-skill
```

### 验证

注册好 MCP server、安装好技能并重启 host 后,向 agent 提问:

> "用 Asta 查论文 ARXIV:1706.03762,字段要 title,year,authors,venue,tldr"

成功调用应返回 *Attention Is All You Need*,NeurIPS 2017,Vaswani 等人,含 TLDR。

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

### 示例：检索 + 批量下载（与 `paper-fetch` 联用）

`asta-skill` 只负责**检索和元数据获取**,不下载 PDF。如果要从搜索结果直接拿到本地 PDF,可与 `paper-fetch` 技能(或任意基于 DOI 的下载工具)串联使用:

```
> 用 Asta 检索 2022 年以来 "single-cell ATAC-seq batch correction" 引用最高的 5 篇论文,
  然后把 DOI 交给 paper-fetch 批量下载到 ./papers/ 目录
```

底层流程:

1. **asta-skill** → 调用 `search_papers_by_relevance`,参数 `publication_date_range="2022:"`,`fields=title,year,authors,venue,tldr,externalIds`(注意带上 `externalIds` 才能拿到 DOI)
2. Agent 从结果中提取 `externalIds.DOI`；没有 DOI 时回退到 `externalIds.ArXiv`
3. **paper-fetch** → 批量按 Unpaywall → arXiv → bioRxiv/medRxiv → PMC → SS → Sci-Hub 顺序解析每个 DOI/arXiv ID
4. PDF 落到 `./papers/`,每篇一个文件

`paper-fetch` 是独立技能,需要下载能力时单独安装。`asta-skill` 本身职责仅限 Semantic Scholar 语料。

**Step 1 — Asta 返回 Top 5 论文(含 DOI)：**

![Asta 检索结果](assets/asta-search.png)

**Step 2 — paper-fetch 把 5 篇 PDF 全部下载到 `./papers/`：**

![paper-fetch 批量下载](assets/asta-paper-fetch.png)

## 🔗 相关技能

属于 [Agents365-ai 科研技能家族](https://github.com/Agents365-ai) —— 按场景挑选合适工具:

| 技能 | 定位 | 何时使用 |
|---|---|---|
| [semanticscholar-skill](https://github.com/Agents365-ai/semanticscholar-skill) | 直连 Semantic Scholar API(Python) | 无法使用 MCP,或更想脚本化访问时 |
| [paper-fetch](https://github.com/Agents365-ai/paper-fetch) | DOI → PDF,7 源回退 | 找到引用后需要全文时 |
| [scholar-deep-research](https://github.com/Agents365-ai/scholar-deep-research) | 8 阶段文献综述流水线 | 用户需要结构化、带引用的综述报告时 |
## 💬 社区

加入交流群获取帮助、提问和最新动态:

- **Discord:** https://discord.gg/79JF5Atuk
- **微信:** 扫描下方二维码

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="微信交流群">
</p>

## ❤️ 支持

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
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/awarding/award.gif" width="180" alt="打赏">
      <br>
      <b>打赏</b>
    </td>
  </tr>
</table>

## 👤 作者

**Agents365-ai**

- GitHub: https://github.com/Agents365-ai
- Bilibili: https://space.bilibili.com/441831884

## 📄 License

[MIT](LICENSE)
