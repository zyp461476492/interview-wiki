---
name: wiki
description: 面试题 wiki 知识库维护工具。当需要检索去重（Query）、回写登记（Ingest）或健康检查（Lint）wiki 时触发。生成面试题前必须 Query，生成后必须 Ingest。Use when maintaining the interview question wiki: dedup search, register questions, or health-check.
---

## 角色

你是面试题 wiki 知识库的维护者。wiki 是基于 [LLM Wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 的持久知识库，位于 `wiki/`。

## 前置约定

- 页面格式、ID 约定见 `wiki/SCHEMA.md`。
- 常驻规则见 `AGENTS.md`。
- 仅处理整份 md/纯文本，用 Read/Write/Edit/Grep/Glob，禁止切分/向量化。

## 操作一：Query（检索去重）

触发：生成任何面试题之前（或用户请求检索某话题已出题目）。

步骤：

1. Read `wiki/index.md`，判断 `wiki/topics/{topic}.md` 是否存在。
2. 若存在：Read 它，提取「题目登记」全部条目（ID / 考点 / 题目）与「知识体系」考点大纲。
3. 若不存在：标记该话题为「新话题」，已登记题目为空。
4. 输出给生成环节：`{已出题目清单, 已覆盖考点集合, 未覆盖考点（知识体系中有但无题的）}`。
   - 生成时严格避开已出题目（换角度/换问法），优先覆盖未覆盖考点。

## 操作二：Ingest（回写登记）

触发：生成题目之后（题目已保存到 `question/`）；评分完成后若产生新参考知识亦可补登。

输入：话题、本次生成的 N 道题（难度/考点/题目正文）、来源文件路径、生成日期。

步骤：

1. 若 `wiki/topics/{topic}.md` 不存在：
   - 创建该文件。frontmatter：`topic` / `created`=今日 / `updated`=今日 / `question_count`=N。
   - 「知识体系」：由本次题目考点 + 话题核心概念提炼出考点大纲。
   - 「题目登记」：写入本次 N 题，ID 从 `{topic}-001` 起。
2. 若存在：
   - Read 现有文件，取当前最大序号，新题 ID 从 `{topic}-{max+1}` 起续编。
   - 在「题目登记」末尾追加 N 题条目。
   - 更新 frontmatter：`updated`=今日，`question_count` += N。
   - 若本次题目触及「知识体系」中未列出的考点，补充进大纲。
3. 更新 `wiki/index.md`：话题页表新增/更新对应行（题量/更新时间/摘要）。
4. 追加 `wiki/log.md`：
   ```
   ## [yyyy-MM-dd] ingest | {topic}
   - 登记/新增题目 N 道（{topic}-xxx ~ {topic}-yyy）
   - 来源：question/{file}
   ```
5. 题库生成场景：若本次是生成题库，新题同样 Ingest 回对应话题页，并把整个题库归档为 `wiki/banks/{name}.md`，更新 index 的 banks 表，log 记 `ingest | {bank名}`。

幂等性：对同一来源文件重复 Ingest 时，按「来源 + 题目正文」判重，已存在则跳过，不产生重复条目。

## 操作三：Lint（健康检查）

触发：用户请求（如「lint wiki」「检查 wiki 健康度」）。

步骤：

1. Read `wiki/index.md` + 用 Glob 列举全部 `wiki/topics/*.md` 与 `wiki/banks/*.md` 并读取。
2. 检查项：
   - **重复/近似题**：同话题内考点相同且题目语义高度相似的条目。
   - **覆盖缺口**：知识体系中列出但无任何题目对应的考点。
   - **孤立页**：话题页/题库页未出现在 index.md。
   - **索引不同步**：index 题量/更新时间与话题页 frontmatter 不一致；log 缺失近期操作。
   - **断链**：`[[话题]]` 链接指向不存在的话题页。
3. 输出报告（按严重度排列）+ 修复建议。
4. 经用户确认后执行修复（合并重复题、补 index、补 log、修正 frontmatter），每项修复追加 `## [yyyy-MM-dd] lint | ...` 到 `log.md`。

## 注意

- 文件名含中文/空格，Read/Edit 调用时路径加引号。
- 保持「题目登记」用节（`### {ID}`）而非表格，便于长题目正文与追加。
