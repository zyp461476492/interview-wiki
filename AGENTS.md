# AGENTS.md

> 本文件为常驻规则层，始终加载。它告诉 LLM：项目存在一个 wiki 知识库、如何与之交互。
> 详细规格见 `wiki/SCHEMA.md`，操作步骤见 `.opencode/.skills/wiki/SKILL.md`，整体架构见 `ARCHITECTURE.md`。

## 项目概述

resume-agent 是一套基于 [LLM Wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 的面试题知识库与练习系统。`wiki/` 是由 LLM 增量维护的持久知识库，积累「话题 + 题目 + 参考答案」，用于生成去重、评分基准与题库编排。

## 关键约束

- **仅处理整份 md / 纯文本文件**。使用 Read / Write / Edit / Grep / Glob。
- **禁止**引入文件切分、向量化、embedding、外部检索引擎（qmd 等）。导航靠 `wiki/index.md`。
- **`raw/` 为只读源层**：用户策展的不可变外部源材料（文章/笔记/PDF 转文本等），LLM 只读不得修改；按需 Ingest 进 wiki，不落盘联网搜索结果，不进 `wiki/index.md`。
- wiki 由 LLM 维护，人一般不直接写；`question/` 是练习会话（交互记录），评分时由 LLM 写入 evaluation。
- **参考答案的权威来源是 wiki 话题页**：生成题目时同步写入 `#### 参考答案`，评分时优先 Query 检索；`question/*.md` 中 evaluation 区的 AI 参考答案为从 wiki 取回的副本，预生成答案不写入 question 文件（避免用户作答前看到）。

## 三种操作与触发时机

| 操作       | 时机                              | 简述                                                                                          |
| ---------- | --------------------------------- | --------------------------------------------------------------------------------------------- |
| **Query**  | 生成面试题**之前**；评分**之前**  | 去重模式：读 `wiki/index.md` + `wiki/topics/{topic}.md` 的**题目索引表 + 知识体系**（不读详情），取已登记题目与已覆盖考点，生成时避开。答案检索模式：Grep `^### {ID}` 定位题节取回已登记 `#### 参考答案` 作为评分基准。**未 Query 不生成。** |
| **Ingest** | 生成题目**之后**（及评分后、用户放入 `raw/` 源材料时）      | 创建/更新 `wiki/topics/{topic}.md`（知识体系 + **题目索引表** + 题目登记 + **参考答案**，索引表与题目登记同步维护），更新 `wiki/index.md`，追加 `wiki/log.md`；评分后若有更优答案则更新对应题；raw 源材料 Ingest 为可选支线（补知识体系考点、log 记来源）。**未 Ingest 视为流程未完成。** |
| **Lint**   | 用户请求时                        | 检查重复/近似题、参考答案缺失、覆盖缺口、索引表与题目登记不同步、孤立页、索引不同步，报告并修复。                                   |

具体步骤见 `.opencode/.skills/wiki/SKILL.md`；页面格式见 `wiki/SCHEMA.md`。

## 跨会话须知

新会话接手时，先读 `ARCHITECTURE.md` 与 `docs/wiki-design.md` 了解全貌，再按需读 `wiki/index.md` 定位具体页面。修改 wiki 结构/流程时，同步更新 `ARCHITECTURE.md`、`docs/wiki-design.md`、`wiki/SCHEMA.md` 及相关 skill。
