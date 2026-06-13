# Project Documents Registry

> 最后更新: 2026-06-13 · 已识别 12 份文档

## 必读 (🟢)
- `skills/brainstorming/SKILL.md` — 主入口,定义 9 步 checklist、HARD-GATE、Visual Companion 邀请规则;每次涉及 brainstorming 都先扫
- `skills/brainstorming/visual-companion.md` — 浏览器伴侣完整使用指南,涉及视觉题(UI mockup、架构图、对比)时必读
- `skills/brainstorming/spec-document-reviewer-prompt.md` — spec 评审 subagent 派发模板,写完 spec 后准备派 reviewer 时必读
- `skills/using-superpowers/SKILL.md` — **项目纪律中枢**,定义 1% rule、Red Flags 反合理化表、优先级链条;理解 superpowers 如何控制 agent 必读

## 参考 (🟡)
- `skills/brainstorming/scripts/server.cjs` — 354 行 Node.js 服务端源码,只在排查协议或修改 server 行为时读
- `skills/using-superpowers/references/` — 三份平台工具映射表(Codex/Copilot/Gemini),写跨 harness 集成时查
- `hooks/session-start` — SessionStart 钩子主逻辑,排查"为什么 agent 没拿到 bootstrap"时读
- `hooks/run-hook.cmd` — polyglot Windows/Unix 包装器,排查跨平台启动问题时读
- `hooks/hooks.json` / `hooks/hooks-cursor.json` — Claude Code 与 Cursor 的钩子配置,改钩子触发时机时读
- `.claude-plugin/plugin.json` / `.claude-plugin/marketplace.json` — Claude Code 插件清单,改 Claude Code 集成时读
- `.codex-plugin/plugin.json` / `.cursor-plugin/plugin.json` — Codex 与 Cursor 插件清单,改对应 harness 集成时读
- `.opencode/plugins/superpowers.js` — OpenCode 插件源码(JS),改 OpenCode 集成时读
- `GEMINI.md` / `AGENTS.md` — Gemini CLI 的文件注入入口与通用 agent 指令入口,改对应入口时读
- `commands/explore.md` — 斜杠命令样板,新增斜杠命令时参照格式
- `docs/superpowers/explorations/INDEX.md` — 已积累的探索索引,新探索开始前先扫一眼找相关主题
- `docs/superpowers/explorations/DOCUMENTS.md` — 本文件,新探索开始前先扫一眼找相关文档
- `docs/superpowers/done-sink/` — 历次开发反思记录,理解项目历史与设计决策时翻

## 跳过 (🔴)
- `skills/brainstorming/scripts/helper.js` — 客户端交互脚本,探索 brainstorming 流程时无需读
- `skills/brainstorming/scripts/frame-template.html` — CSS 主题模板,探索 brainstorming 流程时无需读
- `.opencode/INSTALL.md` — OpenCode 安装指南,理解控制机制本身不需要读
- `package.json` — 依赖清单(已记住:零运行时依赖)

---
*描述越准,后续越能判断"是否与当前主题相关"。新增文档时给一句"什么时候该读"。*