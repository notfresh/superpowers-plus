# brainstorming skill (2026-06-13)

> 一句话总结: 把任意"想法"通过一问一答 + 设计稿,转成 `docs/superpowers/specs/` 下的设计文档;在用户书面批准前,HARD-GATE 禁止任何实现动作。

## 关键文件
- `skills/brainstorming/SKILL.md:12-14` — HARD-GATE:任何实现动作前必须有书面批准,无论项目多"简单"
- `skills/brainstorming/SKILL.md:16-18` — 反模式警示:"This Is Too Simple To Need A Design"
- `skills/brainstorming/SKILL.md:22-32` — 9 步 checklist,要求为每步建 task 并顺序完成
- `skills/brainstorming/SKILL.md:36-64` — Process Flow 图(dot),画清分支与回路
- `skills/brainstorming/SKILL.md:66` — Terminal state:只能调 `writing-plans`,禁止 `frontend-design`/`mcp-builder` 等
- `skills/brainstorming/SKILL.md:73-74` — Scope 过大时强制打断并先拆分,不要先问细节
- `skills/brainstorming/SKILL.md:117-131` — Spec 自审(占位/一致/范围/歧义) + 用户审 spec 双层 gate
- `skills/brainstorming/SKILL.md:148-164` — Visual Companion 邀请必须独立成一条消息
- `skills/brainstorming/visual-companion.md` — 浏览器可视化伴侣完整指南(290 行)
- `skills/brainstorming/spec-document-reviewer-prompt.md` — spec 评审 subagent 派发模板,非 skill
- `skills/brainstorming/scripts/server.cjs:1` — 354 行 Node.js HTTP+WebSocket 服务,零外部依赖
- `skills/brainstorming/scripts/start-server.sh:1` — 多平台启动脚本(mac/Linux/Windows/Codex/Gemini)
- `skills/brainstorming/scripts/frame-template.html` — 内容片段的包装模板(214 行,内含 CSS 主题)
- `skills/brainstorming/scripts/helper.js` — 客户端交互脚本(88 行)
- `tests/brainstorm-server/` — visual companion 服务端测试

## 流程
1. **探索项目上下文** — 看文件、文档、最近提交
2. **预判视觉问题** — 若涉及 UI/图表,先单独一条消息邀请 Visual Companion(必须独立成条)
3. **逐个问澄清问题** — 一次一个,优先多选题;聚焦目的/约束/成功标准
4. **提议 2-3 种方案** — 亮出推荐并解释理由
5. **分节呈现设计** — 每节后等用户确认;覆盖架构、组件、数据流、错误处理、测试
6. **写 spec 文档** — `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`,并 commit
7. **spec 自审** — 4 项 inline 检查;有问题直接修,不另起评审
8. **用户审 spec** — 等用户书面同意;要求改动则改后重跑自审
9. **转入 `writing-plans`** — 唯一允许的下一个 skill

## 坑
- **"这太简单了不用设计"是反模式**:`SKILL.md:16-18` 写明连 todo list、单一函数、config 改动也都走流程;设计可以很短,但不能跳过
- **Visual Companion 邀请必须独占一条消息**:`SKILL.md:151-153` 写"message should contain ONLY the offer and nothing else",不能与澄清问题、上下文合并
- **接受 companion ≠ 每题都用**:`SKILL.md:155-157` 要求每题重判 —— "would the user understand this better by seeing it than reading it?",UI 主题的问题不一定是视觉问题
- **scope 太大要先拆分**:`SKILL.md:73-74` 出现"多独立子系统"(平台 + 聊天 + 文件 + 计费 + 分析)立即打断,先帮用户拆成子项目,每个子项目走独立的 spec → plan → 实现循环
- **不能跳到 writing-plans 之外的 skill**:`SKILL.md:66` 明示"terminal state is invoking writing-plans";若用户已批准,直接调 writing-plans,不要再调 frontend-design / mcp-builder 等
- **`spec-document-reviewer-prompt.md` 是模板不是 skill**:`spec-document-reviewer-prompt.md:3-4` 写"Use this template when dispatching a spec document reviewer subagent",通过 Task 工具派 subagent 使用
- **script 跨平台差异需注意**:Windows 自动切前景模式并阻塞工具调用,必须 `run_in_background: true`;Codex 收割后台进程,自动切前景跑;Gemini 用 `--foreground` + 后台执行;远端/容器环境可能需要 `--host 0.0.0.0`(`visual-companion.md:53-90`)
- **mockup 文件名不要复用**:`visual-companion.md:99` 写"each screen gets a fresh file",服务器按修改时间排序,改名才会被推到浏览器
- **回到终端时推 `waiting.html` 清屏**:`visual-companion.md:117-122` 避免用户盯着已选完的画面等下一步

## 关联
- 紧随其后(唯一):`writing-plans` —— 生成实现计划
- 优先触发原因:见 `skills/using-superpowers/SKILL.md` 的优先级说明
- 相关探索候选:`visual-companion.md` 的服务端协议、CSS 主题、跨平台启动策略可单独抽个 `visual-companion.md`
- 待探索:`docs/superpowers/specs/2026-02-19-visual-brainstorming-refactor-design.md` —— 当前架构的设计决策
- 待探索:`docs/superpowers/specs/2026-03-11-zero-dep-brainstorm-server-design.md` —— server 零依赖取舍的来龙去脉