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

兼容所有支持 MCP 并能加载 [Agent Skills](https://agentskills.io) 的 host —— 已在 **Claude Code、Codex、Cursor、Windsurf、Hermes、opencode、OpenClaw/ClawHub、[pi-mono](https://github.com/badlogic/pi-mono)** 上验证,并收录于 **SkillsMP**。**LM Studio**(0.3.17+)支持 MCP 但不自动加载 skills,需手动将 `SKILL.md` 粘贴到 system prompt(见安装指南中的 [LM Studio(手动模式)](INSTALL_MCP_CN.md#lm-studio手动模式))。

## 前置条件

- 任意支持 MCP 的 agent host(Claude Code、Codex、Cursor、Windsurf、opencode、OpenClaw/ClawHub、pi-mono 等)
- Asta API key —— [点此申请](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## 安装

两步 —— 先注册 MCP server,再把技能加载到 host:

1. **[注册 Asta MCP server](INSTALL_MCP_CN.md)** —— Claude Code、Codex、Cursor / Windsurf / Hermes、LM Studio 各 host 的具体配方。
2. **[安装技能](INSTALL_SKILL_CN.md)** —— 插件市场(推荐)或手动克隆。

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

`asta-skill` 只负责**检索和元数据获取**，不下载 PDF。如果要从搜索结果直接拿到本地 PDF，可与 `paper-fetch` 技能（或任意基于 DOI 的下载工具）串联使用：

```
> 用 Asta 检索 2022 年以来 "single-cell ATAC-seq batch correction" 引用最高的 5 篇论文，
  然后把 DOI 交给 paper-fetch 批量下载到 ./papers/ 目录
```

底层流程：

1. **asta-skill** → 调用 `search_papers_by_relevance`，参数 `publication_date_range="2022:"`，`fields=title,year,authors,venue,tldr,externalIds`（注意带上 `externalIds` 才能拿到 DOI）
2. Agent 从结果中提取 `externalIds.DOI`；没有 DOI 时回退到 `externalIds.ArXiv`
3. **paper-fetch** → 批量按 Unpaywall → arXiv → bioRxiv/medRxiv → PMC → SS → Sci-Hub 顺序解析每个 DOI/arXiv ID
4. PDF 落到 `./papers/`，每篇一个文件

`paper-fetch` 是独立技能，需要下载能力时单独安装。`asta-skill` 本身职责仅限 Semantic Scholar 语料。

**Step 1 — Asta 返回 Top 5 论文（含 DOI）：**

![Asta 检索结果](docs/images/asta-search.png)

**Step 2 — paper-fetch 把 5 篇 PDF 全部下载到 `./papers/`：**

![paper-fetch 批量下载](docs/images/asta-paper-fetch.png)

## 验证

注册好 MCP server 并重启 host 后,向 agent 提问:

> "用 Asta 查论文 ARXIV:1706.03762,字段要 title,year,authors,venue,tldr"

成功调用应返回 *Attention Is All You Need*,NeurIPS 2017,Vaswani 等人,含 TLDR。

## License

MIT

## 社区

加入交流群获取帮助、提问和最新动态:

- **Discord:** https://discord.gg/79JF5Atuk
- **微信:** 扫描下方二维码

<p align="center">
  <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/qrcode/agents365ai_wechat_1.png" width="200" alt="微信交流群">
</p>

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
    <td align="center">
      <img src="https://raw.githubusercontent.com/Agents365-ai/images_payment/main/awarding/award.gif" width="180" alt="打赏">
      <br>
      <b>打赏</b>
    </td>
  </tr>
</table>

## 作者

**Agents365-ai**

- Bilibili: https://space.bilibili.com/441831884
- GitHub: https://github.com/Agents365-ai
