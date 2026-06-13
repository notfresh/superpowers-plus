# README Fork Notice + WHAT-IS-NEW Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a bilingual `WHAT-IS-NEW.md` at repo root and a 2-line callout block in `README.md` pointing to it, so visitors know this fork adds two new skills.

**Architecture:** Two-file docs change. New `WHAT-IS-NEW.md` carries the detailed content (Chinese on top, English below). `README.md` gets a small callout block in the existing intro section. No code, no tests — verification is by reading the rendered output and confirming links resolve.

**Tech Stack:** Markdown, git.

---

## File Structure

- `WHAT-IS-NEW.md` (new, repo root) — bilingual fork notice + new-skill descriptions
- `README.md` (modified, line ~20) — insert callout block between "How it works" and "Sponsorship"

## Task 1: Write `WHAT-IS-NEW.md`

**Files:**
- Create: `WHAT-IS-NEW.md`

- [ ] **Step 1: Write the file with full bilingual content**

Create `WHAT-IS-NEW.md` at repo root with this exact content:

```markdown
# What's New in This Fork

> 本仓库是 [obra/superpowers](https://github.com/obra/superpowers) **v5.1.0** 的 fork。
> 在上游的基础上新增了两个 skill, 详见下表。

| Skill | 作用 | 沉淀位置 |
|---|---|---|
| [`explore`](skills/explore/SKILL.md) | 探索陌生代码库, 通过对话把发现沉淀成可复用的知识库 | `docs/superpowers/explorations/` |
| [`done-sink`](skills/done-sink/SKILL.md) | 任务结束后回顾, 把本次工作的发现/学到/完成沉淀成文档 | `docs/superpowers/done-sink/` |

**附带的 Claude Code slash 命令:** `/explore` (在 `commands/explore.md` 注册, 仅 Claude Code 范围)。

**注意:** 这两个 skill 是本仓库独有, 尚未合并到上游。
上游 `obra/superpowers` 不会包含它们; 如果你从这里复制内容回到上游, 请只复制你需要的部分。

---

## 中文

本仓库在 [obra/superpowers](https://github.com/obra/superpowers) v5.1.0 的基础上做了两处新增:

### 1. [`explore`](skills/explore/SKILL.md) — 代码库探索

通过对话探索项目的代码架构、约定和设计, 并把发现沉淀为 Markdown 文件, 逐步积累成可复用的项目知识库。

沉淀产生并维护两份文件:

- `INDEX.md` — 已探索主题的字典 (按 `架构 / 数据模型 / 约定 / 杂项` 等语义分类)
- `DOCUMENTS.md` — 已发现项目文档的登记表 (按 `🟢 必读 / 🟡 参考 / 🔴 跳过 / 🟠 待整理` 四档分级)

单独一份探索报告落在 `docs/superpowers/explorations/<slug>.md`, 内含 `关键文件 / 流程 / 坑 / 代码 vs 文档 / 关联` 等 section。

**触发方式:**
- 显式 slash: `/explore` 或 `/explore <topic>`
- 自然语言: "探索一下 X" / "调研一下" / "看看这个项目怎么做的"
- 隐式提议: agent 在 `INDEX.md` 没覆盖时主动提议 "要不要我探索一下 X?"

### 2. [`done-sink`](skills/done-sink/SKILL.md) — 任务回顾

工作完成后, 主动询问是否要回顾本次工作, 并把发现/学到/完成沉淀为 Markdown 文件。

**触发时机 (agent 会主动问 "需不需要回顾一下?"):**
- `subagent-driven-development` 单个任务完成时
- `finishing-a-development-branch` 全流程结束时
- `requesting-code-review` 评审 cycle 完成后
- `executing-plans` 全部任务完成后
- 任何有实质交付的工作完成后

**手动触发:** 随时可以说 "帮我做个反思" / "回顾一下"。

**沉淀文件格式:** `<YYYY-MM-DD>-<slug>.md`, 三个 section: `发现 / 学到 / 完成`。

### 附带的 slash 命令

仅 Claude Code 范围:

- `/explore` — 触发 explore skill (有/无 topic 都可)

### 与上游的差异

- 新增 `skills/explore/` 和 `skills/done-sink/` 两个 skill 目录
- 新增 `commands/explore.md` slash 命令文件
- 新增 `docs/superpowers/explorations/` 和 `docs/superpowers/done-sink/` 两个沉淀物目录
- 上游所有 skill 和 README 主体内容未做修改

上游 README 的安装说明、贡献指南、Release Notes 仍然有效, 请以本 fork 的 `WHAT-IS-NEW.md` 为准了解本仓库新增的功能。

---

## English

This repository is a fork of [obra/superpowers](https://github.com/obra/superpowers) v5.1.0 with two extra skills added on top:

### 1. [`explore`](skills/explore/SKILL.md) — Codebase Exploration

Investigate a project's code architecture, conventions, and design through dialogue, and sediment findings as Markdown files that grow into a reusable knowledge base.

Two sediment files are maintained:

- `INDEX.md` — dictionary of explored topics (categorized as `Architecture / Data Model / Conventions / Misc`)
- `DOCUMENTS.md` — registry of discovered project documents (rated `🟢 must-read / 🟡 reference / 🔴 skip / 🟠 unorganized`)

A single exploration report lives at `docs/superpowers/explorations/<slug>.md` with sections: `Key files / Flow / Gotchas / Code vs docs / Related`.

**Triggers:**
- Explicit slash: `/explore` or `/explore <topic>`
- Natural language: "explore X" / "look into this project"
- Implicit proposal: agent suggests "要不要我探索一下 X?" when `INDEX.md` does not cover a relevant topic

### 2. [`done-sink`](skills/done-sink/SKILL.md) — Task Reflection

After a task or workflow completes, offer to capture what was discovered, learned, and accomplished as a Markdown file in `docs/superpowers/done-sink/`.

**When the agent offers reflection (asks "需不需要回顾一下?"):**
- After a `subagent-driven-development` task completes
- After `finishing-a-development-branch`
- After a `requesting-code-review` cycle
- After `executing-plans` finishes
- After any significant work completion

**Manual trigger:** say "帮我做个反思" or "回顾一下" at any time.

**Sediment file format:** `<YYYY-MM-DD>-<slug>.md` with three sections: `Discovered / Learned / Accomplished`.

### Bundled Slash Command

Claude Code only:

- `/explore` — invokes the explore skill (works with or without a topic)

### Difference from Upstream

- New skill directories: `skills/explore/` and `skills/done-sink/`
- New slash command file: `commands/explore.md`
- New sediment directories: `docs/superpowers/explorations/` and `docs/superpowers/done-sink/`
- No upstream skills or README body content was modified

The upstream install instructions, contribution guide, and release notes still apply. Use this `WHAT-IS-NEW.md` to learn what this fork adds.
```

- [ ] **Step 2: Verify file content**

Run: `head -5 WHAT-IS-NEW.md`
Expected: first line is `# What's New in This Fork`.

Run: `grep -c '^## ' WHAT-IS-NEW.md`
Expected: `4` (Chinese `##` section, English `##` section, plus the two `### N. [...]` subsections in each — wait, the section count is `4` for top-level `## ` plus the inline `###` headers. Verify by reading the file: should have `## 中文`, `## English`, and within each, `### 1.`, `### 2.`, `###` for slash command, `###` for difference. Total `## ` count should be `2`; total `### ` count should be `8`).

Run: `grep -c '^### ' WHAT-IS-NEW.md`
Expected: `8`.

If counts don't match, fix the file before continuing.

- [ ] **Step 3: Commit the new file alone**

```bash
git add WHAT-IS-NEW.md
git commit -m "$(cat <<'EOF'
docs: add WHAT-IS-NEW.md describing fork's new skills

Bilingual (Chinese first, English below) listing the two extra skills
this fork adds on top of obra/superpowers v5.1.0: explore and
done-sink, plus the /explore slash command.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

Expected: commit hash printed, status clean for this file.

---

## Task 2: Add callout block to `README.md`

**Files:**
- Modify: `README.md` (insert 2-line callout between line 19 and line 22)

- [ ] **Step 1: Locate the insertion point**

Run: `sed -n '15,25p' README.md`
Expected: shows the "How it works" paragraphs ending, blank line, then `## Sponsorship` heading. The blank line right before `## Sponsorship` is where the callout goes.

- [ ] **Step 2: Insert the callout block via Edit**

The current `README.md` has a blank line between the closing paragraph of "How it works" (`... Your coding agent just has Superpowers.`) and the `## Sponsorship` heading. Use the Edit tool to insert these two lines after the blank line that precedes `## Sponsorship`:

old_string (the blank line + `## Sponsorship` heading):

```


## Sponsorship
```

new_string (blank line + callout + blank line + `## Sponsorship`):

```


> **📌 This is a fork of [obra/superpowers](https://github.com/obra/superpowers) v5.1.0.** Two extra skills are added: [`explore`](skills/explore/SKILL.md) and [`done-sink`](skills/done-sink/SKILL.md). See [WHAT-IS-NEW.md](WHAT-IS-NEW.md) for what changed.
>
> **📌 本仓库是 [obra/superpowers](https://github.com/obra/superpowers) v5.1.0 的 fork。** 在上游基础上新增了两个 skill: [`explore`](skills/explore/SKILL.md) 和 [`done-sink`](skills/done-sink/SKILL.md)。差异详见 [WHAT-IS-NEW.md](WHAT-IS-NEW.md)。

## Sponsorship
```

Note: the leading two newlines in `old_string` represent the blank line in the file (line 20) plus the heading. Do not add extra blank lines.

- [ ] **Step 3: Verify insertion**

Run: `sed -n '15,30p' README.md`
Expected: paragraphs end, blank line, callout blockquote (2 lines with `>`), blank line, then `## Sponsorship`.

Run: `grep -c 'WHAT-IS-NEW.md' README.md`
Expected: `2` (one in the English line, one in the Chinese line).

If count is not 2, fix and re-verify.

- [ ] **Step 4: Commit the README change**

```bash
git add README.md
git commit -m "$(cat <<'EOF'
docs: add fork notice callout to README

Bilingual 2-line callout block linking to WHAT-IS-NEW.md, placed
between the "How it works" intro and the Sponsorship section.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

Expected: commit hash printed, status clean.

---

## Task 3: Final verification

**Files:**
- Read: `git log`, `git show`, `WHAT-IS-NEW.md`, `README.md`

- [ ] **Step 1: Verify both commits exist**

Run: `git log --oneline -5`
Expected: top commit is "docs: add fork notice callout to README", second is "docs: add WHAT-IS-NEW.md describing fork's new skills", third is the design spec commit.

- [ ] **Step 2: Verify working tree is clean (modulo pre-existing uncommitted exploration files)**

Run: `git status`
Expected: only the four pre-existing items from before this task — modified `docs/superpowers/explorations/DOCUMENTS.md` and `INDEX.md`, plus untracked `docs/superpowers/explorations/agent-control-mechanism.md` and `brainstorming.md`. **None of WHAT-IS-NEW.md, README.md, or any new files should appear.**

- [ ] **Step 3: Verify links resolve**

Run: `ls skills/explore/SKILL.md skills/done-sink/SKILL.md WHAT-IS-NEW.md`
Expected: all three paths exist.

- [ ] **Step 4: Spot-check rendered output**

Open both files in a markdown viewer and confirm:
- WHAT-IS-NEW.md has Chinese on top, English below, table renders, all four skill `###` headers present
- README.md callout block is visible, links are clickable, no formatting glitches

---

## Self-Review

**1. Spec coverage:**
- New `WHAT-IS-NEW.md` with Chinese-on-top / English-below → Task 1 ✓
- README callout block at "How it works" / "Sponsorship" boundary → Task 2 ✓
- Fork disclosure → both callout and WHAT-IS-NEW ✓
- Both skills described → both sections of WHAT-IS-NEW ✓
- `/explore` slash command → mentioned in both sections ✓
- No "why not upstream" content → spec did not request it; not included ✓
- Commit each file separately → Tasks 1 step 3 and 2 step 4 ✓

**2. Placeholder scan:**
- No "TBD" / "TODO" / "implement later"
- No "add appropriate error handling" / "add validation"
- No "write tests for the above" — this is a docs change, no tests needed
- No "Similar to Task N" — each step stands alone
- All code blocks contain actual content (the file body in Task 1 step 1, the callout text in Task 2 step 2)

**3. Type consistency:** Not applicable (no code, no types).

**4. File paths:** `WHAT-IS-NEW.md` (root), `README.md` (root), `skills/explore/SKILL.md` (linked), `skills/done-sink/SKILL.md` (linked) — all consistent across tasks.
