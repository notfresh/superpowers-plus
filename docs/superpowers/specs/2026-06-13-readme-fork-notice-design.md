# README + WHAT-IS-NEW 文档设计 (2026-06-13)

> 一句话总结: 在仓库根新建一份中英双语 WHAT-IS-NEW.md, 并在 README 顶部加一个 callout 块引导过去。

## 背景

本仓库是 `obra/superpowers` v5.1.0 的 fork (commit f2cbfbe 是分叉点)。
在 fork 之后, 本仓库新增了两个 skill:

- `skills/explore/SKILL.md` —— 探索陌生代码库, 把发现沉淀到 `docs/superpowers/explorations/`, 维护 `INDEX.md` (已探索主题) 和 `DOCUMENTS.md` (已发现文档)
- `skills/done-sink/SKILL.md` —— 任务结束后回顾, 把本次工作总结沉淀到 `docs/superpowers/done-sink/`

另附 Claude Code slash 命令 `/explore` (仅 Claude Code 范围)。

这两个 skill 尚未合并到上游。访问者从 GitHub 进来时,
默认会看到上游的 README, 完全不知道本仓库做了什么, 所以需要一份新增功能说明。

## 目标

- 访问者第一眼知道这是 fork, 知道和上游有什么差异
- 提供一个详细的"新增功能"文档入口
- 不影响上游 README 的可读性, callout 块尽量短

## 非目标 (YAGNI)

- 不重写整个 README
- 不去合并上游 (不是这次任务的范围)
- 不新增 changelog/release-notes 文件 (上游已有 `RELEASE-NOTES.md`, 本次也不动它)
- 不在多个 harness 适配 (README callout 块不分平台)
- 不解释为什么没合并上游 (用户明确说只列新内容)

## 设计

### 1. 新文件 `WHAT-IS-NEW.md` (仓库根)

结构: 中文在上, 英文在下。中文 blockquote 包裹, 英文 blockquote 包裹。

中文 section 内容:
1. 一句话声明: 本仓库是 `obra/superpowers` v5.1.0 的 fork
2. 新增 skill 列表 (含 `skills/<name>/SKILL.md` 的相对链接)
3. 一句话描述每个 skill 做什么
4. 附带的 Claude Code slash 命令: `/explore`
5. 诚实声明: 这两个 skill 是本仓库独有, 尚未合并到上游
6. 沉淀物路径提示: `docs/superpowers/explorations/` 和 `docs/superpowers/done-sink/`

英文 section: 上面内容的中英对照翻译, 同样的章节顺序。

文件长度: 中文 + 英文总共估计 60~100 行。

### 2. 编辑 `README.md`

在 "How it works" 段落 (第 19 行) 和 "Sponsorship" section (第 22 行) 之间, 插一个两行 callout 块。

```
> **📌 This is a fork of [obra/superpowers](https://github.com/obra/superpowers) v5.1.0.** ...
>
> **📌 本仓库是 ... 的 fork。** ...
```

两行中英双语 + 一个 `WHAT-IS-NEW.md` 的相对链接。
使用 blockquote (`>`) 视觉上和其他段落区分, 不引入新 heading。

### 3. 提交

一个 commit:

- `WHAT-IS-NEW.md` (新增)
- `README.md` (修改)

Commit message: `docs: add WHAT-IS-NEW and fork notice to README`

## 实现步骤 (后续 plan 会展开)

1. 写 `WHAT-IS-NEW.md` (中英两个 section)
2. 编辑 `README.md`, 在指定位置插 callout 块
3. `git add` 两个文件, `git commit`
4. 检查 commit 后的 `git log --oneline -5` 和 `git show` 确认 diff 干净

## 验收

- 仓库根有 `WHAT-IS-NEW.md`, 中英两 section 都存在且结构一致
- `README.md` 在 "How it works" 后能看到 callout 块
- callout 块的链接可点
- commit 干净, 没有带多余文件
