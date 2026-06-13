# Agent 控制机制 (2026-06-13)

> 一句话总结: 通过 SessionStart 钩子在每个会话开头把 `using-superpowers` 技能全文塞进 agent 的上下文,再用 `<EXTREMELY_IMPORTANT>` 包裹 + Red Flags 反合理化表,把"先调 skill 再行动"打成不可绕过的纪律。零代码强制 —— 全靠文本。

## 关键文件
- `hooks/session-start:1` — **核心控制脚本**:读 using-superpowers 全文,包 `<EXTREMELY_IMPORTANT>` 标签,按平台 emit 不同 JSON
- `hooks/session-start:46-55` — **三路 JSON 发射器**:Cursor (`additional_context`)、Claude Code (`hookSpecificOutput.additionalContext`)、其他 (`additionalContext` SDK 标准)
- `hooks/session-start:42-44` — **去重注释**:Claude Code 同时读 `additional_context` 和 `hookSpecificOutput` 不会去重,所以只发当前平台用的那个字段
- `hooks/run-hook.cmd:1-46` — **polyglot Windows/Unix 包装器**:同文件前半是 cmd.exe 语法,后半是 bash(被 `: << 'CMDBLOCK'...CMDBLOCK` heredoc 隔离),由 OS 决定执行哪半
- `hooks/run-hook.cmd:13-39` — Windows 自动找 Git Bash、MSYS2、Cygwin,找不到 bash 就静默 exit 0(plugin 仍可用,只是没有 SessionStart 注入)
- `hooks/hooks.json:1-16` — Claude Code 钩子配置:匹配 `startup|clear|compact`,非 async,同步等结果
- `hooks/hooks-cursor.json:1-10` — Cursor 钩子配置:`sessionStart`(驼峰),`./hooks/run-hook.cmd session-start`
- `GEMINI.md:1-2` — Gemini CLI 的"钩子替代":用 `@./skills/using-superpowers/SKILL.md` 文件引用直接嵌入上下文
- `AGENTS.md:1` — 内容就是字符串 `CLAUDE.md`,让支持 AGENTS.md 的 agent 读到贡献者指南(但不注入技能)
- `.claude-plugin/plugin.json:21-23` — Cursor/通用结构(也带 commands/ 路径);Claude Code 用同一 `skills/` 目录
- `.claude-plugin/marketplace.json:1-21` — Claude Code 市场清单,把整个项目作为插件分发
- `.codex-plugin/plugin.json:1-47` — Codex 插件清单,带 `interface` 段描述 UI
- `.opencode/plugins/superpowers.js:1-66` — OpenCode 插件 JS,自实现系统提示词转换(不走 SessionStart 钩子)
- `.opencode/INSTALL.md:9-19` — OpenCode 用 `opencode.json` 的 `plugin: ["superpowers@git+..."]` 数组声明,不写文件系统
- `commands/explore.md:1-7` — 斜杠命令格式:`Use the explore skill to investigate: ${ARGUMENTS}`,由 harness 自动注册
- `skills/using-superpowers/SKILL.md:6-16` — `<EXTREMELY-IMPORTANT>` 标签 + "1% rule"(即使 1% 可能性也要调 skill)
- `skills/using-superpowers/SKILL.md:80-96` — Red Flags 反合理化表,12 条预判 agent 会说的借口("太简单"、"我记得"、"先看一下")
- `skills/using-superpowers/SKILL.md:19-26` — **优先级链条**:用户指令 > skill > 默认行为,明文告诉 agent 听用户的

## 控制机制分层

```
┌─────────────────────────────────────────────────────────┐
│ 第 1 层: 安装/发现                                        │
│ - 4 种 harness 各有插件清单(JSON),声明 skills/commands/hooks │
│ - Claude Code: marketplace.json                          │
│ - Codex / Cursor: 各自的 plugin.json                     │
│ - OpenCode: opencode.json 的 plugin[] 数组(动态拉取)        │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│ 第 2 层: 会话注入                                         │
│ - Claude Code / Cursor: SessionStart 钩子                │
│   → run-hook.cmd (polyglot) → session-start (bash)        │
│   → 读 using-superpowers/SKILL.md,emit JSON              │
│ - Gemini CLI: GEMINI.md 的 @file 引用(无钩子)            │
│ - OpenCode: superpowers.js 改 system prompt              │
│ - Codex: 未见显式 session-start,推测由 Codex harness 处理 │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│ 第 3 层: 上下文内容                                       │
│ <EXTREMELY_IMPORTANT>                                    │
│   You have superpowers.                                  │
│   <full using-superpowers skill body>                   │
│   <legacy migration warning, if applicable>              │
│ </EXTREMELY_IMPORTANT>                                   │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│ 第 4 层: 行为约束(纯文本)                                │
│ - "1% rule":任何相关可能性都先调 skill                    │
│ - Red Flags:12 条反合理化话术,预判 agent 的挣扎            │
│ - 优先级: process skill 先于 implementation skill         │
│ - 强制声明:"不调 skill 就没选择"                          │
└─────────────────────────────────────────────────────────┘
```

## 流程
1. 用户在 harness 中安装 superpowers(4 种方式之一)
2. Harness 读插件清单,注册 `skills/`、`commands/`、`hooks/`
3. 用户开新会话(或 `/clear`、`/compact`)→ SessionStart 钩子触发
4. `run-hook.cmd session-start` 跨平台分发(Windows 走 Git Bash,Unix 直接 bash)
5. `session-start` 检测环境变量(`CURSOR_PLUGIN_ROOT` / `CLAUDE_PLUGIN_ROOT`),决定 JSON 字段格式
6. JSON 输出到 stdout,harness 解析后注入 agent 上下文
7. Agent 看到 `<EXTREMELY-IMPORTANT>` 包裹的 bootstrap 内容
8. 用户给任务 → agent 必须先调 Skill 工具检查相关 skill,即使 1% 相关
9. 调出的 skill 提供完整流程;agent 跟着走

## 坑
- **纯文本强制,无代码校验**:`session-start` 注入后,agent 实际会不会遵守完全靠它自己 —— 没有 post-hook 检查"你有没有真的调 skill"
- **平台协议差必须分支**:`session-start:42-44` 注释明示 Claude Code 同时读 `additional_context` 和 `hookSpecificOutput` 不去重,所以必须按平台只发一个字段 —— 否则会重复注入浪费上下文
- **Windows bash 找不到就静默退**:`run-hook.cmd:37-39` 写"No bash found - exit silently rather than error(plugin still works, just without SessionStart context injection)" —— 没有 Git Bash 的 Windows 用户等于没装 SessionStart
- **`run-hook.cmd` 是 polyglot 大坑**:Windows 用 `.cmd` 扩展名绕过 Claude Code 的"含 .sh 就前置 bash"自动检测(`run-hook.cmd:7-9` 注释);但 Unix 直接执行 `.cmd` 文件需要 shell 容忍 —— `: << 'CMDBLOCK' ... CMDBLOCK` heredoc 在 cmd.exe 里是无效语法,只是 bash 才认
- **bash 5.3+ heredoc 会 hang**:`session-start:44-46` 注释引用 #571 —— 必须用 `printf` 而不是 heredoc,否则 hang
- **JSON 转义有性能坑**:之前的字符循环替换方案慢,现在用 `${s//old/new}` 一次性 bash 内置替换(`session-start:21-31` 注释)
- **AGENTS.md ≠ 技能注入**:内容只是 `CLAUDE.md` 字面量,让 agent 读到贡献者指南;agent 不会因此获得 skill bootstrap,必须单独走 GEMINI.md 或钩子路径
- **斜杠命令 vs skill 是两套**:`commands/explore.md` 是 harness 自动注册的斜杠命令,只起"转发到 explore skill"的作用;真正的纪律在 skill 里
- **legacy 警告会被注入上下文**:`session-start:14` 检测 `~/.config/superpowers/skills` 目录,若存在则在 bootstrap 里塞 `<important-reminder>` 让 agent 立刻提醒用户迁移
- **优先级链条明文写在 skill 里**:`using-superpowers/SKILL.md:19-26` 第一优先级是用户指令,所以"用户说别用 TDD"高于 skill 的"永远用 TDD" —— 这不是双重保险,是单向豁免

## 关联
- 相关:[brainstorming skill](brainstorming.md) —— 控制机制下最受约束的对象之一,HARD-GATE + 9 步流程就是"纪律成型"的范例
- 待探索:`tests/claude-code/` 和 `tests/skill-triggering/` —— 应该测的就是"agent 真的会被强制调 skill 吗"
- 待探索:`docs/superpowers/specs/2026-03-23-codex-app-compatibility-design.md` —— Codex 平台的设计取舍
- 待探索:`docs/superpowers/specs/2026-05-21-project-knowledge-sync-design.md` —— 沉淀物同步机制
- 外部参考:Anthropic 的 skills 文档与本项目"compliance"哲学差异(`CLAUDE.md:48-53` 明文写"comply with Anthropic's skills documentation will not be accepted")