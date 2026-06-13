# Explore Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a new `explore` Superpowers skill that lets an agent explore a project's code through dialogue and sediment findings as Markdown files, plus a Claude Code `/explore` slash command.

**Architecture:** Purely additive. Five new files, zero edits to existing files. The skill is documentation-only (a SKILL.md with a workflow) and uses only built-in Read/Write/Edit tools. Two empty seed files (INDEX.md, DOCUMENTS.md) under `docs/superpowers/explorations/` provide a predictable starting state.

**Tech Stack:** Markdown, YAML frontmatter, Claude Code slash command format.

**Spec:** `docs/superpowers/specs/2026-06-13-explore-skill-design.md`

**Working directory:** `c:\Users\zhengxu\projects\my-superpower`

---

## File Structure (locked in by this plan)

| File | Responsibility |
|---|---|
| `docs/superpowers/explorations/INDEX.md` | Empty template — dictionary of explored topics |
| `docs/superpowers/explorations/DOCUMENTS.md` | Empty template — registry of discovered documents |
| `commands/explore.md` | Claude Code slash command, forwards to the skill |
| `skills/explore/SKILL.md` | The skill workflow itself |

No file is modified. The `docs/superpowers/explorations/` directory is created on demand.

---

## Task 1: Create sediment template files

**Files:**
- Create: `docs/superpowers/explorations/INDEX.md`
- Create: `docs/superpowers/explorations/DOCUMENTS.md`

- [ ] **Step 1: Create the `explorations/` directory**

Run:
```bash
mkdir -p docs/superpowers/explorations
```
Expected: no output, exit code 0.

- [ ] **Step 2: Write `INDEX.md` (empty template)**

Write to `docs/superpowers/explorations/INDEX.md`:

```markdown
# Project Knowledge Index

> 最后更新: YYYY-MM-DD · 共 0 项探索

(尚无探索。运行 `/explore <主题>` 启动第一次探索。)

---
*新增探索时,挑已有分类追加,或新建一个分类 section。保持分类少而稳。*
```

- [ ] **Step 3: Write `DOCUMENTS.md` (empty template)**

Write to `docs/superpowers/explorations/DOCUMENTS.md`:

```markdown
# Project Documents Registry

> 最后更新: YYYY-MM-DD · 已识别 0 份文档

(尚无登记。`explore` 技能首次读取项目文档时,会自动登记到此。)

---
*描述越准,后续越能判断"是否与当前主题相关"。新增文档时给一句"什么时候该读"。*
```

- [ ] **Step 4: Verify the two files exist with the expected content**

Run:
```bash
ls -la docs/superpowers/explorations/
```
Expected: both `INDEX.md` and `DOCUMENTS.md` listed.

Then read each file and confirm:
- `INDEX.md` has the H1 `# Project Knowledge Index` and the footer line.
- `DOCUMENTS.md` has the H1 `# Project Documents Registry` and the footer line.

- [ ] **Step 5: Commit**

Run:
```bash
git add docs/superpowers/explorations/INDEX.md docs/superpowers/explorations/DOCUMENTS.md
git commit -m "feat(explore): add empty INDEX.md and DOCUMENTS.md seed templates"
```

---

## Task 2: Create the Claude Code slash command

**Files:**
- Create: `commands/explore.md`

- [ ] **Step 1: Re-read the spec's Trigger section**

Read `docs/superpowers/specs/2026-06-13-explore-skill-design.md` under "Triggers" to confirm the slash command's role. The slash command is one of three trigger surfaces; the body should be a thin forwarder.

- [ ] **Step 2: Create the `commands/` directory**

Run:
```bash
mkdir -p commands
```
Expected: no output, exit code 0.

- [ ] **Step 3: Write `commands/explore.md`**

Write to `commands/explore.md`:

```markdown
---
description: Explore the project's code architecture, conventions, and design. Sediment findings to docs/superpowers/explorations/. With no topic, the skill enters casual mode and proposes areas to explore.
---

Use the explore skill to investigate: ${ARGUMENTS}

(If no argument was provided, the explore skill will run in casual mode and propose a topic.)
```

- [ ] **Step 4: Verify the file**

Read `commands/explore.md` and confirm:
- Has YAML frontmatter with `description` field.
- Body contains the literal string `${ARGUMENTS}` and the casual-mode hint.
- No trailing whitespace, file ends with a newline.

- [ ] **Step 5: Commit**

Run:
```bash
git add commands/explore.md
git commit -m "feat(explore): add /explore Claude Code slash command"
```

---

## Task 3: Create the explore SKILL.md

**Files:**
- Create: `skills/explore/SKILL.md`

This is the largest task. The skill body is built section by section, exactly matching the spec.

- [ ] **Step 1: Create the `skills/explore/` directory**

Run:
```bash
mkdir -p skills/explore
```
Expected: no output, exit code 0.

- [ ] **Step 2: Write the complete `skills/explore/SKILL.md`**

This is the final file. Writing it in a single Write call is recommended (avoids Edit-append ordering bugs). The complete file is:

```markdown
---
name: explore
description: Use when you want to understand an unfamiliar codebase or project area — explores code architecture, conventions, and design through dialogue, and sediments findings to docs/superpowers/explorations/ as a growing project knowledge base.
---

# Explore — Project Codebase Investigation

## Overview

This skill lets an agent explore a project's code architecture, conventions, and design through natural dialogue, and sediment the findings as Markdown files that grow into a reusable knowledge base over time.

Two sediment files are produced and maintained:

- `INDEX.md` — dictionary of **explored topics** (agent-generated knowledge)
- `DOCUMENTS.md` — registry of **discovered project documents** (existing files) with value ratings

The skill is intentionally minimal: it uses only built-in Read/Write/Edit tools, no scripts, no hooks, no harness config changes.

## Goals

- Make codebase exploration **fast and conversational** — `/explore auth` returns a usable explanation in one turn.
- Build a **growing knowledge base** that survives sessions via the repository's own `docs/` directory.
- Be a **resource** that other skills can opt into (`Read INDEX.md` / `Read DOCUMENTS.md`) without code changes.
- Keep change scope **small** — five new files, zero edits to existing files.

## Non-Goals (YAGNI)

- Cross-harness support beyond Claude Code in this skill.
- Slash command auto-registration in any harness manifest.
- A script/tool for managing the sediment files (Read/Write/Edit are enough).
- Schema validation, linting, or CI checks for `INDEX.md` / `DOCUMENTS.md`.
- Automatic archival, rename, or deletion of 🟠 "needs organizing" documents.
- Modifying any other skill to use these resources (we document the integration; we don't enforce it).

## Triggers

The skill activates on any of:

- **Explicit slash command:** `/explore` or `/explore <topic>`
- **Natural language:** "探索一下 X"、"调研一下"、"看看这个项目怎么做的"、"explore X"
- **Implicit proposal:** The agent recognizes the user is unclear about a part of the project that `INDEX.md` does not cover, and proposes "要不要我探索一下 X?"

The agent must announce the invocation: `"Using the explore skill to ..."`.

## Two Modes

### Mode 1 — Directed

The user supplied a topic (via slash argument, natural language, or in response to a proposal).

Steps:

1. Restate the topic: "好,我要探索 <topic>。"
2. **Read DOCUMENTS.md first.** For each entry, decide relevance by comparing the document's one-line description against the topic. Read only relevant documents. Skip 🟢/🟡 entries whose description is clearly off-topic. Pause on 🟠 entries and ask the user whether to read them.
3. Read the relevant code: `Glob` to locate files, then `Read` 1–3 rounds, targeted by the topic.
4. Form a structured mental model: architecture, key files, flow, gotchas.
5. **Cross-check code vs documents**, but only for documents whose content is being actively used to inform this topic. If a mismatch is found, record it under `## 代码 vs 文档` in the exploration file. Do not chase mismatches that are not relevant to the current topic.
6. Generate `docs/superpowers/explorations/<slug>.md` with the format defined below.
7. Update `docs/superpowers/explorations/INDEX.md` (append one entry under the appropriate category).
8. Update `docs/superpowers/explorations/DOCUMENTS.md` (append one entry per **newly** read document, with a one-line impression and a tier).
9. Report: "已写入 `<path>`,INDEX.md 和 DOCUMENTS.md 已更新。"

### Mode 2 — Casual

The user invoked `/explore` or "探索一下" with no topic.

Steps:

1. Read `INDEX.md`.
2. Branch:
   - **a) INDEX.md missing or empty:** Ask the user: "你最想了解这个项目的哪块?"
   - **b) INDEX.md has content:** Identify categories that exist but are sparse (≤1 entry) and any obviously missing categories. Propose 1–2 candidates: "INDEX.md 里 `<X>` 还是空的,要探索这块吗?或者你心里有别的方向?"
3. Wait for the user's selection.
4. Hand off to Mode 1 with the chosen topic.

The casual mode never picks a topic fully on its own — the user always confirms. This is "agent proposes, user disposes."

## Sediment File Formats

### `INDEX.md` — Explored Topics Dictionary

```markdown
# Project Knowledge Index

> 最后更新: 2026-06-13 · 共 N 项探索

## 架构 (Architecture)
- [认证流](auth-flow.md) — 登录、会话、token 校验的端到端流程
- [构建管线](build-pipeline.md) — 从源码到可部署产物的步骤

## 数据模型 (Data Model)
- [用户表结构](user-schema.md) — 用户核心字段及关联关系

## 约定 (Conventions)
- [错误处理](error-handling.md) — 错误如何传递与暴露
- [测试模式](testing-patterns.md) — 单元、集成、E2E 测试的写法

## 杂项 (Misc)
- [日志实践](logging.md) — 日志分级、格式、采集点

---
*新增探索时,挑已有分类追加,或新建一个分类 section。保持分类少而稳。*
```

- **Categories are human-semantic** (架构 / 数据模型 / 约定 / 杂项), not technical tags.
- **Each entry: title (linking to a single exploration file) + one-line description** — enough to decide whether to open the file.
- The header line (`最后更新 ... 共 N 项`) is rewritten on every update.
- The footer is the only static content; the rest is regenerated.

### `DOCUMENTS.md` — Discovered Documents Registry

```markdown
# Project Documents Registry

> 最后更新: 2026-06-13 · 已识别 8 份文档

## 必读 (🟢)
- `README.md` — 项目目标/快速上手/贡献指南,每次探索都先扫一眼
- `docs/superpowers/specs/` — 既有设计文档,涉及架构必查
- `skills/done-sink/SKILL.md` — 沉淀物模式参考,新文档格式可对齐

## 参考 (🟡)
- `docs/windows/polyglot-hooks.md` — 多平台 hook 差异,仅当改动 hook 时读
- `RELEASE-NOTES.md` — 变更历史,改完一个功能后查是否要更新

## 跳过 (🔴)
- `package.json` — 依赖清单,与代码探索关系不大(已记住:无运行时依赖)

## 待整理 (🟠)
- `docs/superpowers/specs/2026-01-22-document-review-system-design.md` — 内容似乎已废弃,文件还在仓库

---
*描述越准,后续越能判断"是否与当前主题相关"。新增文档时给一句"什么时候该读"。*
```

Four tiers:

| Tier | Symbol | Meaning | Behavior |
|---|---|---|---|
| 必读 | 🟢 | Always relevant in some form | Read or skim on every exploration |
| 参考 | 🟡 | Relevant only in some topics | Read only when description matches the current topic |
| 跳过 | 🔴 | Not useful for exploration | Never read again (recorded so we don't re-Read) |
| 待整理 | 🟠 | Suspicious / stale / misnamed | Pause and ask the user before reading |

The footer reinforces the discipline: descriptions must include "when to read" so future relevance judgments are cheap.

### Single Exploration File — `<slug>.md`

```markdown
# 认证流 (2026-06-13)

> 一句话总结: 基于 JWT,access 15 分钟 / refresh 7 天,Redis 存黑名单。

## 关键文件
- `src/auth/login.ts:42` — 登录入口,签发 token
- `src/auth/middleware.ts:1` — 校验 access token
- `src/auth/refresh.ts:88` — 刷新逻辑

## 流程
1. 用户 POST /login → 校验密码 → 签发 access + refresh
2. 后续请求带 Authorization header
3. 中间件校验签名、过期、黑名单
4. 过期用 refresh 换新 access

## 坑
- `login.ts:42` 不会主动撤销旧 token,用户改密码后旧 token 仍有效 15 分钟
- `refresh.ts:120` 在 Redis 故障时降级为"放行所有 refresh token",安全风险

## 代码 vs 文档
- `README.md` 第 12 行说"使用 cookie session",但 `src/auth/middleware.ts:1` 实际校验 JWT。README 可能过期。

## 关联
- 相关: 用户表结构
- 待探索: 权限控制 (RBAC 是不是这套)
```

- The first line is a one-sentence summary — the dominant scan path is "INDEX.md + first line".
- Key files include `path:line` for direct Read targeting by future agents.
- The `坑` section is highlighted as the highest-value content.
- The `代码 vs 文档` section is **omitted entirely** when no relevant mismatch was found; it is added only when one is found and is relevant to the topic.
- The `关联` section cross-links INDEX.md entries and points to follow-up topics.

## Red Flags (Discipline Rules)

**Never:**

- Save an exploration file with empty content (at least one bullet under `## 关键文件` or `## 流程`).
- Overwrite an existing `<slug>.md` — if a file with that slug exists, append a date suffix (`<slug>-2026-06-13.md`).
- Trigger the casual-mode proposal in the middle of unrelated work.
- Read a 🟢/🟡 document whose one-line description is clearly off-topic for the current topic.
- Read any 🔴 document.
- Read a 🟠 document without first asking the user.
- Report a code-vs-doc mismatch that does not relate to the current topic.
- Delete, rename, or archive a 🟠 document without user confirmation.

**Always:**

- Read `DOCUMENTS.md` before reading any other project document.
- Update `INDEX.md` and `DOCUMENTS.md` together with every new exploration.
- Include the date in the exploration file's H1 heading.
- One-line impression for every newly registered document.
- Use `path:line` for file references inside the exploration file.

## Integration Points

This skill is **purely additive**. No other skill is required to change. The integration is documented here and stands on its own:

- `INDEX.md` and `DOCUMENTS.md` live at `docs/superpowers/explorations/`.
- When any skill needs project context, it can:
  1. Read `INDEX.md` to find related explorations.
  2. Read `DOCUMENTS.md` to find relevant documents (and skip irrelevant ones).
  3. Read specific files only if relevant.
- Skills are NOT required to use this; it's an opt-in resource.
- No hooks, no automatic injection — purely pull-based.
```

- [ ] **Step 3: Verify the file matches the spec**

Read `skills/explore/SKILL.md` and confirm each section is present:
- [ ] Frontmatter with `name: explore` and a one-line `description`
- [ ] `# Explore — Project Codebase Investigation` H1
- [ ] `## Overview`, `## Goals`, `## Non-Goals (YAGNI)`
- [ ] `## Triggers` with three bullets
- [ ] `## Two Modes` with Mode 1 (9 steps) and Mode 2 (4 steps)
- [ ] `## Sediment File Formats` with three sub-sections (INDEX.md, DOCUMENTS.md, single file)
- [ ] `## Red Flags (Discipline Rules)` with Never and Always lists
- [ ] `## Integration Points` with the 5-line pull-based description

- [ ] **Step 4: Commit**

Run:
```bash
git add skills/explore/SKILL.md
git commit -m "feat(explore): add explore skill workflow"
```

---

## Task 4: Run verification scenarios

This task is the "test phase". The spec's Verification Checklist (8 scenarios) is the test plan. Each scenario is run in a clean-ish context (read the spec, the SKILL.md, and the relevant sediment file), and the result is recorded.

**Files:**
- Modify: `docs/superpowers/specs/2026-06-13-explore-skill-design.md` (append a Verification Report section)
- Possibly modify: `skills/explore/SKILL.md` (if scenarios reveal gaps)

- [ ] **Step 1: Set up a clean test environment**

Run from a fresh shell:
```bash
cd c:/Users/zhengxu/projects/my-superpower
git status
```
Expected: working tree clean.

Confirm the four new files exist:
```bash
ls skills/explore/SKILL.md commands/explore.md docs/superpowers/explorations/INDEX.md docs/superpowers/explorations/DOCUMENTS.md
```
Expected: all four paths printed, no errors.

- [ ] **Step 2: Scenario 1 — Directed exploration, first run**

Walk through what happens when a user types `/explore auth` (or "探索一下 auth"):
- Agent announces `"Using the explore skill to ..."`
- Agent reads `docs/superpowers/explorations/DOCUMENTS.md` (empty template) — finds no relevant docs.
- Agent reads project code for the "auth" topic.
- Agent writes `docs/superpowers/explorations/auth.md` (or a similar slug).
- Agent updates `INDEX.md` with one new entry under a chosen category.
- Agent updates `DOCUMENTS.md` only if new project documents were read.

**Pass criteria:** All five steps are achievable from the SKILL.md text. If any step is ambiguous, the SKILL.md needs a fix.

- [ ] **Step 3: Scenario 2 — Casual mode, INDEX.md has content**

Walk through what happens when `INDEX.md` has at least 2 entries and the user types `/explore` (no topic):
- Agent reads `INDEX.md`.
- Agent identifies a sparse or missing category.
- Agent proposes 1–2 candidates.
- Agent waits for user selection.

**Pass criteria:** All four steps are in the SKILL.md.

- [ ] **Step 4: Scenario 3 — Casual mode, INDEX.md empty**

Walk through what happens when `INDEX.md` is the empty template and the user types `/explore`:
- Agent reads `INDEX.md`, sees the "尚无探索" line.
- Agent asks: "你最想了解这个项目的哪块?"

**Pass criteria:** The "INDEX.md missing or empty" branch in Mode 2 step 2a is present in the SKILL.md.

- [ ] **Step 5: Scenario 4 — Re-read avoidance**

Walk through what happens when a 🟢 document is relevant to two consecutive explorations:
- First exploration: Read + register in DOCUMENTS.md with a one-line impression.
- Second exploration: Agent reads DOCUMENTS.md first, sees the cached impression, and decides to skip the re-Read because the impression already covers the need.

**Pass criteria:** SKILL.md says "Read DOCUMENTS.md first" and "Read only relevant documents."

- [ ] **Step 6: Scenario 5 — Code-vs-doc mismatch, relevant**

Walk through what happens when the agent is exploring "auth" and `README.md` (marked 🟢 in DOCUMENTS.md) contradicts the code on the auth topic:
- Agent reads README.md because it's relevant.
- Agent notices a mismatch.
- Agent adds `## 代码 vs 文档` section to the exploration file.

**Pass criteria:** SKILL.md "Cross-check code vs documents" step is in Mode 1 step 5, and the "代码 vs 文档" section format is shown in the Single Exploration File subsection.

- [ ] **Step 7: Scenario 6 — Code-vs-doc mismatch, off-topic**

Walk through what happens when the agent is exploring "auth" and `RELEASE-NOTES.md` (marked 🟡 in DOCUMENTS.md with a description unrelated to auth) mentions an old error in a different feature:
- Agent reads `DOCUMENTS.md`, sees `RELEASE-NOTES.md` description is unrelated to "auth".
- Agent skips the re-Read.
- The error mentioned in `RELEASE-NOTES.md` is **not** reported in the auth exploration file.

**Pass criteria:** SKILL.md "Skip 🟢/🟡 entries whose description is clearly off-topic" and Red Flag "Report a code-vs-doc mismatch that does not relate to the current topic."

- [ ] **Step 8: Scenario 7 — 🟠 document encountered**

Walk through what happens when a document marked 🟠 is potentially relevant to the current topic:
- Agent reads `DOCUMENTS.md`, sees a 🟠 entry whose description might match.
- Agent pauses and asks the user before reading.

**Pass criteria:** SKILL.md "Pause on 🟠 entries and ask the user whether to read them" and Red Flag "Read a 🟠 document without first asking the user."

- [ ] **Step 9: Scenario 8 — Non-existent topic**

Walk through what happens when a user types `/explore unicorn-factory` and no related code exists:
- Agent attempts to Glob for relevant code.
- Agent finds nothing.
- Agent reports "找不到相关代码" and does NOT create empty sediment files.

**Pass criteria:** Red Flag "Save an exploration file with empty content." Agent must refuse to write empty files.

- [ ] **Step 10: Record the verification report**

Append to `docs/superpowers/specs/2026-06-13-explore-skill-design.md`:

```markdown

## Verification Report

> Run on 2026-06-13 against `skills/explore/SKILL.md` (commit <hash>)

| # | Scenario | Result | Notes |
|---|---|---|---|
| 1 | `/explore auth` directed, first run | PASS / FAIL | <one-line note> |
| 2 | `/explore` casual, INDEX.md has content | PASS / FAIL | <one-line note> |
| 3 | `/explore` casual, INDEX.md empty | PASS / FAIL | <one-line note> |
| 4 | 🟢 re-read avoidance | PASS / FAIL | <one-line note> |
| 5 | Code-vs-doc mismatch, relevant | PASS / FAIL | <one-line note> |
| 6 | Code-vs-doc mismatch, off-topic | PASS / FAIL | <one-line note> |
| 7 | 🟠 document encountered | PASS / FAIL | <one-line note> |
| 8 | Non-existent topic | PASS / FAIL | <one-line note> |

If any scenario is FAIL, fix the SKILL.md before this PR is opened.
```

Fill in the table from the prior steps. If any scenario is FAIL, the fix is a follow-up commit (not part of this task).

- [ ] **Step 11: Commit the verification report**

Run:
```bash
git add docs/superpowers/specs/2026-06-13-explore-skill-design.md
git commit -m "docs: record explore skill verification results"
```

If a SKILL.md fix was needed, also commit it as a separate commit before this one:
```bash
git add skills/explore/SKILL.md
git commit -m "fix(explore): address verification gap from scenario N"
```

---

## Self-Review

Run this after writing the plan, before handing off to execution.

- [ ] **Spec coverage:** Each spec section maps to a task:
  - File Changes → Task 1 + Task 2 + Task 3
  - Triggers → Task 2 + Task 3
  - Two Modes → Task 3
  - Sediment File Formats → Task 1 + Task 3
  - Red Flags → Task 3
  - Integration Points → Task 3
  - Verification Checklist → Task 4
- [ ] **Placeholder scan:** No "TBD", "TODO", "implement later" in any step. Each code step has the full file content.
- [ ] **Type consistency:** File paths and tool names match across tasks (`docs/superpowers/explorations/INDEX.md`, `docs/superpowers/explorations/DOCUMENTS.md`, `commands/explore.md`, `skills/explore/SKILL.md`).
