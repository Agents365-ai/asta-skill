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

兼容所有支持 MCP 并能加载 [Agent Skills](https://agentskills.io) 的 host —— 已在 **Claude Code、Codex、Cursor、Windsurf、Hermes、opencode、OpenClaw/ClawHub、[pi-mono](https://github.com/badlogic/pi-mono)** 上验证,并收录于 **SkillsMP**。**LM Studio**(0.3.17+)支持 MCP 但不自动加载 skills,需手动将 `SKILL.md` 粘贴到 system prompt(见安装指南中的 [LM Studio(手动模式)](docs/INSTALL_MCP_CN.md#lm-studio手动模式))。

## 前置条件

- 任意支持 MCP 的 agent host(Claude Code、Codex、Cursor、Windsurf、opencode、OpenClaw/ClawHub、pi-mono 等)
- Asta API key —— [点此申请](https://share.hsforms.com/1L4hUh20oT3mu8iXJQMV77w3ioxm)

  ```bash
  export ASTA_API_KEY=xxxxxxxxxxxxxxxx
  ```

## 安装

两步 —— 先注册 MCP server,再把技能加载到 host:

1. **[注册 Asta MCP server](docs/INSTALL_MCP_CN.md)** —— Claude Code、Codex、Cursor / Windsurf / Hermes、LM Studio 各 host 的具体配方。
2. **[安装技能](docs/INSTALL_SKILL_CN.md)** —— 插件市场(推荐)或手动克隆。

## 使用方式

参见 [USAGE_CN.md](docs/USAGE_CN.md) —— 包含自然语言查询示例,以及 `asta-skill` + `paper-fetch` 联用的完整流程演示(含截图)。

## 🔗 相关技能

属于 [Agents365-ai 科研技能家族](https://github.com/Agents365-ai) —— 按场景挑选合适工具:

| 技能 | 定位 | 何时使用 |
|---|---|---|
| [semanticscholar-skill](https://github.com/Agents365-ai/semanticscholar-skill) | 直连 Semantic Scholar API(Python) | 无法使用 MCP,或更想脚本化访问时 |
| [paper-fetch](https://github.com/Agents365-ai/paper-fetch) | DOI → PDF,7 源回退 | 找到引用后需要全文时 |
| [scholar-deep-research](https://github.com/Agents365-ai/scholar-deep-research) | 8 阶段文献综述流水线 | 用户需要结构化、带引用的综述报告时 |
| [zotero-research-assistant](https://github.com/Agents365-ai/zotero-research-assistant) | Zotero 文献库工作流 | 文献需要进入 Zotero 时 |

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
