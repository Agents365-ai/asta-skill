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

参见 [USAGE_CN.md](USAGE_CN.md) —— 包含自然语言查询示例,以及 `asta-skill` + `paper-fetch` 联用的完整流程演示(含截图)。

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
