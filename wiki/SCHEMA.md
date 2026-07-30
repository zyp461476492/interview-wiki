# Wiki 规格

> wiki 的结构、页面格式、ID 约定。LLM 维护 wiki 时严格遵循本规格。
> 规则层见 `AGENTS.md`，操作步骤见 `.opencode/.skills/wiki/SKILL.md`。

## 目录布局

```
wiki/
├── SCHEMA.md          # 本文件
├── index.md           # 内容索引（topics + banks）
├── log.md             # append-only 操作日志
├── topics/            # 话题页：知识体系 + 题目登记
│   └── {topic}.md
└── banks/             # 题库页：精选题集
    └── {name}.md
```

## 话题页格式 `topics/{topic}.md`

```markdown
---
topic: Java 基础
created: 2026-07-30
updated: 2026-07-30
question_count: 0
---

# Java 基础

## 知识体系

（该话题核心考点大纲，首次 Ingest 时建立，Lint 时维护）
- 基础语法与类型（==/equals、装箱拆箱、基本类型）
- 面向对象（接口/抽象类、OOP 原则）
- ...

## 题目登记

### java基础-001
- 难度：基础 | 考点：String 不可变
- 题目：String/StringBuilder/StringBuffer 三者有什么区别？为什么 Java 把 String 设计成不可变？
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-6617.md

#### 参考答案
（生成题目时同步写入的标准/参考答案；评分时优先从此处检索；评分/Lint 后可增量完善）
- String 内部用 final char[]（JDK9+ byte[]）存储，不可变；StringBuilder 可变、非线程安全；StringBuffer 可变、线程安全（synchronized）。
- 不可变原因：字符串常量池复用、线程安全、hashCode 缓存、防止安全类（如作为 HashMap key）被篡改。
- ...

## 关联

- 相关话题：[[java并发]]、[[jvm]]
```

字段说明：

- frontmatter：`topic`（显示名）/ `created` / `updated` / `question_count`（已登记题数）。
- 知识体系：考点大纲，用于覆盖度检查与题库编排。
- 题目登记：每题一节（`### {ID}`），含难度、考点、题目正文、首次生成日期、来源文件；节内 `#### 参考答案` 子节存放该题标准/参考答案（生成题目时同步写入，评分时优先检索，评分/Lint 后可增量完善）。
- 关联：用 `[[话题名]]` 交叉链接。

## 题库页格式 `banks/{name}.md`

```markdown
---
bank: Java 高级题库
size: 50
created: 2026-07-30
topics: [java并发, jvm, java集合]
---

# Java 高级题库（50 题）

## 说明
基于 topics/java并发、jvm、java集合 整理，覆盖进阶并发、JVM 调优、集合原理。

## 题目

### Q1 [进阶] (考点: 分段锁)
题目正文……（若引用已登记题，注明 ID 如 java并发-003；若为新题，生成后 Ingest 回对应话题页）

### Q2 ...
```

## index.md 格式

两张表：topics、banks。

```markdown
# Wiki 索引

> 内容导向目录。Query/Ingest 前先读此文件定位页面。

## 话题页

| 话题 | 题量 | 更新时间 | 摘要 |
|------|------|----------|------|
| [Java 基础](topics/java基础.md) | 8 | 2026-07-30 | Java 语法/OOP/集合/并发/JVM 核心考点 |

## 题库页

| 题库 | 题量 | 创建时间 | 覆盖话题 | 摘要 |
|------|------|----------|----------|------|
```

## log.md 格式

append-only。每条以 `## [yyyy-MM-dd] op | subject` 开头，`op ∈ {ingest, query, lint}`。

```markdown
## [2026-07-30] ingest | Java 基础
- 首次建立话题页 topics/java基础.md
- 登记题目 8 道（java基础-001 ~ java基础-008，含参考答案）
- 来源：question/2026-07-30-java基础-6617.md

## [2026-07-30] query | Java 基础
- 生成前检索：已登记 8 题，避开重复角度
```

## ID 约定

- 话题页文件名 = 话题文本（如 `java基础.md`）；显示名可含空格（frontmatter `topic`）。
- 题目 ID = `{topic}-{seq}`，话题内三位自增（`java基础-001`）。
- 题库页文件名 = 题库名（如 `java高级50题.md`）。

## 约束

- 仅 md / 纯文本，整文件读写，不切分、不向量化。
- 文件名含中文/空格时，工具调用注意加引号。
- **参考答案的权威来源是 wiki 话题页**（`#### 参考答案`）：生成时写入，评分时优先检索，`question/*.md` 中 evaluation 区的 AI 参考答案为从 wiki 取回的副本。
