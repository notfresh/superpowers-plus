# Brainstorming Consults Explore — Design Spec

## Overview

When the `brainstorming` skill runs, it consults the `explore` skill's sediment files (`INDEX.md` and `DOCUMENTS.md` at `docs/superpowers/explorations/`) as part of its project context exploration. Empty, missing, or off-topic files are skipped silently. Relevant entries are cited actively in subsequent steps of the brainstorm.

The integration is purely additive: **zero new files, zero new skills, zero new hooks**. Three edits to a single file (`skills/brainstorming/SKILL.md`), totaling ~15 lines added.

## Goals

- Make `brainstorming` **context-aware** — it should know what the user has already explored about the project before asking its first question
- Enable **active citation** — when asking clarifying questions or proposing approaches, surface relevant prior explorations by name so the user can verify
- Preserve **"one thing at a time" discipline** — no /explore suggestion from inside brainstorming, no continuous consultation, just a one-shot read at the start of a session
- Stay **minimal** — one file modified, no architectural change to the skills system

## Non-Goals (YAGNI)

- Modifying any skill other than `brainstorming` (`writing-plans`, `debugging`, etc. are out of scope for this PR)
- Auto-invoking `/explore` from within brainstorming
- A separate `consult-explore` skill or wrapper around brainstorming
- A hook-based injection mechanism
- Continuous consultation (re-Reading exploration files throughout the brainstorm)
- Modifying `explore/SKILL.md` to add a "how to be consulted" section (the explore skill already documents its pull-based integration in its own spec)
- Eval evidence / before-after measurements (this is the user's fork, not core)

## File Changes

```
modified: skills/brainstorming/SKILL.md
新增:    docs/superpowers/specs/2026-06-13-brainstorming-consults-explore-design.md  (this file)
```

Three discrete edits to `skills/brainstorming/SKILL.md`:

1. `## Checklist` — Step 1 expanded with sub-item 1a
2. `## The Process` — new `## Reading Exploration Context` section added under `Understanding the idea`
3. `## Process Flow` — dot graph gains a "Read exploration context" sub-node

No edits to any other file. No edits to `skills/explore/SKILL.md`. No edits to any harness plugin config.

## The Design

### Edit 1 — `## Checklist` (currently lines 22–32)

Step 1 becomes two-line sub-item:

```markdown
1. **Explore project context** — check files, docs, recent commits
   1a. **Read exploration context** — read `docs/superpowers/explorations/INDEX.md` (and `DOCUMENTS.md` if helpful). Empty, missing, or no relevant entries → proceed normally. Relevant entries → Read those files; cite explicitly in subsequent steps.
2. **Offer visual companion** (if topic will involve visual questions) — this is its own message, not combined with a clarifying question. See the Visual Companion section below.
```

The numbering keeps the existing 9-step checklist intact; 1a is a child of Step 1, not a new top-level step.

### Edit 2 — New `## Reading Exploration Context` section under `Understanding the idea`

Inserted after the "Check out the current project state first" paragraph, before the "Exploring approaches" sub-heading:

```markdown
## Reading Exploration Context

Before asking clarifying questions, read the project's exploration sediment:

- Read `docs/superpowers/explorations/INDEX.md`. If it has entries that look relevant to the current brainstorm topic, Read those exploration files.
- Optionally read `DOCUMENTS.md` to know which existing project docs are off-limits or high-signal.
- **Empty, missing, or no relevant entries: proceed normally.** Do not prompt the user to run `/explore` from here.
- **If a relevant exploration exists: cite it explicitly** in later steps, e.g. "I see INDEX.md has an auth-flow exploration — does this need to integrate with that?" or "Your existing data-model exploration covers user tables — should this new feature extend it or stand alone?"
- **🟠 documents in DOCUMENTS.md: never read.** Follow the explore skill's "Read a 🟠 document without first asking the user" red flag.
- **🟢/🟡 documents whose description is clearly off-topic: don't read.** Follow the explore skill's same rule.
- **Never invent citations.** Only cite what was actually in the read files.
- **The read is one-shot per session.** When the topic shifts within a brainstorm, re-read only if the new topic doesn't overlap with the previous one's relevant explorations; otherwise reuse what was read. Agent's call.
- **If the user contradicts a cited exploration** (e.g., "X 探索说走 JWT,其实我们走 cookie"), the user's latest statement wins. If a design is committed, optionally add a `## 代码 vs 文档` section to record the conflict (mirrors the explore skill's pattern).
```

### Edit 3 — `## Process Flow` dot graph (currently lines 36–64)

Add two edges to insert "Read exploration context" between "Explore project context" and "Visual questions ahead?":

```dot
"Explore project context" -> "Read exploration context";
"Read exploration context" -> "Visual questions ahead?";
```

The rest of the graph (Visual Companion offer, clarifying questions, etc.) is unchanged.

## Failure Modes / Edge Cases

| # | 情况 | 行为 | Spec 依据 |
|---|---|---|---|
| 1 | `INDEX.md` 不存在 | 静默跳过 | "Empty, missing, or no relevant entries" |
| 2 | `INDEX.md` 存在但 0 项 | 静默跳过 | 同上 |
| 3 | `INDEX.md` 有 N 项,0 项相关 | 静默跳过;不在澄清问题里提 | spec 明文"proceed normally" |
| 4 | `INDEX.md` 有 N 项,部分沾边 | 默认只读明显相关的;**K 是参考不是限制**,agent 拿主意 | spec 明文 "agent's call" |
| 5 | `INDEX.md` 条目指向不存在文件(链接坏) | 跳过该 entry,继续 | Read 错误降级为 "no content for this entry" |
| 6 | `DOCUMENTS.md` 含 🟠 | **不读** | spec 明文 |
| 7 | `DOCUMENTS.md` 含 🟢/🟡 但 off-topic | 不读 | spec 明文 |
| 8 | 单个 exploration 文件 > 200 行 | 读关键 section(关键文件/流程/坑),具体多深 agent 拿主意 | spec 软化 |
| 9 | 同一 brainstorm 切到子主题 | 主题明显变 → 重读;否则沿用上次读到的 | spec "Agent's call" |
| 10 | mid-brainsorm 显式 `/explore` | 尊重 /explore 触发,该 explore 完成后回到 brainstorming 时,新写入的 exploration 自然可见 | spec 明文 |
| 11 | 用户反驳 cited exploration(如"X 探索说走 JWT,其实走 cookie") | 用户最新陈述为准;必要时在最终 spec 加 `## 代码 vs 文档` 一节 | spec 明文 |
| 12 | brainstorming 主题跟项目无关(如"设计一个 logo") | INDEX.md 有项目特定内容,无相关 → 静默 | spec "no relevant entries" |

## Red Flags (Discipline)

**Never:**

- Suggest running `/explore` from within brainstorming
- Read a 🟠 document in DOCUMENTS.md
- Cite an exploration that wasn't actually read (no fabrication)
- Cite an exploration that has nothing to do with the current topic (no padding)
- Re-read exploration files continuously during a brainstorm (one-shot per session)

**Always:**

- Treat "empty / missing / no relevant" as the same case — no error, no special message
- Cite the file by its name when referencing an exploration (so the user can verify)
- Let the user's latest statement override any cited exploration

## Integration with Existing Skills

This skill change is **purely about `brainstorming`**. The `explore` skill is unchanged. The integration is pull-based:

- `brainstorming` reads `explore`'s outputs (the new Step 1a)
- `explore` does not need to know `brainstorming` exists
- Other skills (writing-plans, debugging, etc.) are unchanged; they can opt into reading exploration files later on their own

This matches the integration philosophy already documented in `explore`'s spec:
> "When any skill needs project context, it can: 1. Read INDEX.md, 2. Read DOCUMENTS.md, 3. Read specific files only if relevant. Skills are NOT required to use this; it's an opt-in resource."

## Post-Implementation Note

After this change is merged, the existing `docs/superpowers/explorations/brainstorming.md` (which lists the 9-step checklist under "## 关键文件" and "## 流程") will be **out of date** — Step 1 has gained sub-item 1a. That exploration should be re-run or appended to in a separate, follow-up change. Not part of this PR — flagged here so the drift is visible.

## Verification

### Diff verification (immediate, post-implementation)

`git diff skills/brainstorming/SKILL.md` should show:

- 1 added line in `## Checklist` (the 1a sub-item)
- 1 added section under `## Understanding the idea` (~10 lines)
- 2 added edges in the dot graph (1 new node, 2 new edges, 0 modified edges)
- 0 deletions

Total: ~15 lines added, 0 removed.

### Spec walkthrough (12 scenarios)

| # | 场景 | Pass criteria | Spec line that handles it |
|---|---|---|---|
| 1 | `INDEX.md` 缺失 | 静默,无错误外溢 | "Empty, missing, or no relevant entries" |
| 2 | `INDEX.md` 0 项(当前 worktree) | 静默,继续 | 同上 |
| 3 | `INDEX.md` 有 N 项,无相关 | 静默,澄清问题里不出现"exploration" | "proceed normally" |
| 4 | `INDEX.md` 有 N 项,部分沾边 | agent 拿主意,不强求 K 项 | "K 是参考不是限制" |
| 5 | `INDEX.md` 链接坏 | 跳过该 entry,继续 | Read 错误降级 |
| 6 | `DOCUMENTS.md` 含 🟠 | 不读,且不推荐用户读 | spec 明文 |
| 7 | `DOCUMENTS.md` 含 🟢/🟡 off-topic | 不读 | spec 明文 |
| 8 | 单 exploration 文件 > 200 行 | 读关键 section,不全读 | spec 软化 |
| 9 | 切子主题 | 重读 or 沿用,agent 拿主意 | spec "agent's call" |
| 10 | mid-brainsorm `/explore` | 尊重触发,新内容下个切换自然可见 | spec 明文 |
| 11 | 用户反驳 cited exploration | 用户胜;spec 可加 `## 代码 vs 文档` | spec 明文 |
| 12 | brainstorming 主题跟项目无关 | 静默 | "no relevant entries" |

If any scenario is FAIL, fix the SKILL.md before this PR is merged.

### Real agent smoke test (recommended, post-merge)

The user's main project has 2 real exploration entries (`agent-control-mechanism.md`, `brainstorming.md`) — perfect fixture. After merging to main:

1. Open a clean session in the main project
2. Trigger brainstorming with a topic that should cite `agent-control-mechanism.md` (e.g., "改 SessionStart 钩子")
3. Observe whether the agent cites the exploration in its first clarifying question or design proposal
4. Trigger brainstorming with a clearly off-topic topic (e.g., "设计一个新 logo")
5. Observe whether the agent stays silent about explorations

Pass = on-topic cites by name, off-topic is silent.

## Open Questions

None at design time. All resolved during brainstorming:

- Q: 集成方式? → A: 直接改 brainstorming/SKILL.md
- Q: 空文件怎么办? → A: 静默跳过
- Q: 怎么消费内容? → A: 主动引用
- Q: 范围? → A: 只动 brainstorming
- Q: 补丁形态? → A: 结构化子步骤(Approach 2)
- Q: 读多少? → A: K 是参考,agent 拿主意
- Q: 用户反驳 exploration 怎么办? → A: 用户胜;spec 可加冲突记录
- Q: 何时重读? → A: 主题明显变才重读,agent 拿主意
