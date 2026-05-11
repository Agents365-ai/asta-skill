# 技能安装

[English](INSTALL_SKILL.md)

技能正文位于仓库内的 `skills/asta-skill/SKILL.md`。最简单的安装方式是通过插件市场。

## 插件市场(推荐)

```bash
# 任意 agent(Claude Code、Cursor、Copilot 等)
npx skills add Agents365-ai/365-skills -g

# 仅 Claude Code
/plugin marketplace add Agents365-ai/365-skills
/plugin install asta
```

同时收录于 [SkillsMP](https://skillsmp.com/) 与 [ClawHub](https://clawhub.ai/) —— 各自通过自己的市场处理更新。

## 手动克隆(任意 host)

```bash
git clone https://github.com/Agents365-ai/asta-skill.git /tmp/asta-skill
cp -r /tmp/asta-skill/skills/asta-skill <你的-host-的-skills-目录>/asta-skill
```
