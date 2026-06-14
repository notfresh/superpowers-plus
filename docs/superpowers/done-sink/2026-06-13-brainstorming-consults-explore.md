# Brainstorming Consults Explore (2026-06-13)

## 发现 (What we discovered)
- 用户主项目已有 2 项真实探索(`agent-control-mechanism.md`、`brainstorming.md`),正好覆盖了"如何控制 agent"和"brainstorming skill 本身"两个主题——这正是新行为该主动引用的素材
- `skills/brainstorming/SKILL.md` 用了"软结构"模式:`## The Process` 下面用 `**bold**:` 而不是 `###` 来分小节——这意味着加新 `##` section 会变成顶层 sibling,跟其他小节不对称
- Worktree 隔离做得很彻底:`feature/explore-skill` 分支上 commit 完整、状态干净,可以独立 merge;spec / plan / 实现 / done-sink 都在同一个 worktree 链上

## 学到 (What we learned)
- **"K 是参考不是限制"** 是用户对 agent 行为约束的口味:面对"读多少 exploration 文件"这种判断题,用户更愿意给 agent 拿主意而不是锁死条数
- **brainstorming 自己的"一次只做一件事"和"agent 判断优先"原则,被新功能完整继承**——空文件静默跳过、agent 拿主意读多少、不主动推 `/explore`,都是 brainstorming 自身精神的延续
- **3 处 markdown 改动 = 3 个 commit + 3 轮 spec/code review + 1 轮总 review**:每个 edit 都是"读 → 改 → verify → commit"的微 TDD 循环,跟代码 TDD 同构
- **plan 的"line 数估计"几乎一定会错**:11 估 14、~15 估 18——pinned `new_string` 是权威,estimate 是参考;实施者当场数对才避免了一次 spec 误读
- **12 场景 spec walkthrough 是 markdown 改动的"测试"**:跟 explore 技能验证用同一种方法(读 spec 走查),这是文档 / skill 类改动能用的最严验证
- **per-task 2 阶段 review (spec + code) + 1 轮总 review 模式跑得通**:3 个 commit 跨任务的一致性("Read exploration context" 这个名字 3 处一致、checklist 1a 是 section 的浓缩、dot 图节点名跟 section 名一致)能被 final review 一次扫完

## 完成 (What we accomplished)
- 完整 spec → plan → 实现 → 验证 闭环:
  - spec: `ee0a6a3` (189 行,12 场景验证表 + 失败模式表)
  - plan: `f6062a6` (433 行,4 任务,每任务 2-5 分钟 step)
- 3 个 commit 落到 `feature/explore-skill`:
  - `22fb8fa` feat(brainstorming): 加 1a 子项到 `## Checklist`
  - `22134ce` docs(brainstorming): 加 `## Reading Exploration Context` section
  - `129fdc3` docs(brainstorming): dot 图加 "Read exploration context" 子节点
- 净改动:`skills/brainstorming/SKILL.md` +18/-1 行,1 个文件,无新文件
- 12 场景 spec walkthrough 全过(`brainstorming-consults-explore-impl` tag 已打)
- Final review 批准:跨 commit 一致性 OK,无 Critical/Important
- 5 个 review Minor notes(全为 cosmetic,"no fix needed"),记录给后续可能的 follow-up
- CLAUDE.md contributor 规则零冲突:无新依赖、无 domain-specific、纯 fork-internal 改动
- 探索-沉淀模式首次闭环:`brainstorming.md` 的探索内容(用户主项目里)直接喂给了 brainstorming skill 自身的设计,印证 explore 技能"未来 agent 可查"的价值主张
