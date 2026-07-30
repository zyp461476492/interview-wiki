# 项目架构

> 本文件记录 resume-agent 的整体架构，供后续改动时参照与同步更新。
> 详细设计见 `docs/wiki-design.md`，wiki 维护规格见 `wiki/SCHEMA.md`，常驻规则见 `AGENTS.md`。

## 项目目标

基于 [Karpathy 的 LLM Wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)，构建一套由 LLM 增量维护的面试题知识库（wiki）：

- 持久化积累话题与题目（**含参考答案**），作为去重依据与题库来源；
- 生成新题前检索 wiki 去重；
- 评分时优先从 wiki 检索已登记参考答案作为评分基准。

## 三层架构（LLM Wiki 模式映射）

| 层             | 说明                       | 本项目对应                                                                              |
| -------------- | -------------------------- | --------------------------------------------------------------------------------------- |
| Raw sources    | 不可变来源，LLM 只读       | 互联网检索结果 + 练习会话 `question/*.md`（评分时写入 evaluation，AI 参考答案为 wiki 副本） |
| Wiki           | LLM 维护的持久 markdown 库 | `wiki/`（话题页含知识体系+题目登记+参考答案 / 题库页 / 索引 / 日志）                    |
| Schema         | 约定 / 工作流              | `AGENTS.md`（规则）+ `wiki/SCHEMA.md`（规格）+ `.opencode/.skills/wiki/SKILL.md`（操作） |

## 目录结构

```
resume-agent/
├── AGENTS.md                       # 常驻规则层（始终加载）
├── ARCHITECTURE.md                 # 本文件：架构记录
├── docs/                           # 设计文档（跨会话执行用）
│   └── wiki-design.md              # wiki 系统设计文档
├── wiki/                           # Wiki 层（LLM 生成维护）
│   ├── SCHEMA.md                   # 规格：目录布局/页面格式/ID 约定
│   ├── index.md                    # 内容索引（topics + banks）
│   ├── log.md                      # append-only 操作日志
│   ├── topics/                     # 话题页：知识体系 + 题目登记 + 参考答案
│   └── banks/                      # 题库页：精选题集（归档回 wiki）
├── question/                       # 练习会话（raw source / 交互记录，wiki 外）
└── .opencode/.skills/
    ├── wiki/SKILL.md               # 三操作：Ingest / Query / Lint
    └── topic-interviewer/          # 面试题生成与评分
        ├── SKILL.md                # 接入 Query/Ingest
        └── references/question-template.md
```

## 三种操作

- **Query（检索）**：两种模式。生成题目前用「去重模式」--读 `wiki/index.md` + `wiki/topics/{topic}.md`，输出已登记题目与已覆盖考点，生成时避开；评分前用「答案检索模式」--读话题页，按题取回已登记 `#### 参考答案` 作为评分基准。**未 Query 不生成。**
- **Ingest（回写登记）**：生成/评分后调用。创建/更新话题页（知识体系 + 题目登记 + **参考答案**），更新 `wiki/index.md`，追加 `wiki/log.md`；评分后若对答案有更优补充则更新对应题参考答案。
- **Lint（健康检查）**：用户请求时调用。检查重复/近似题、参考答案缺失、覆盖缺口、孤立页、索引不同步，报告并修复。

详见 `.opencode/.skills/wiki/SKILL.md` 与 `docs/wiki-design.md`。

## 数据流

```
用户给话题
   │
   ▼
[topic-interviewer] ──Query(去重)──> 读 wiki/topics + index ──> 已出题目/考点
   │                                                 │
   ▼                                                 │
生成 8 题 + 参考答案（避开重复）<──────────────────────┘
   │
   ├──> 保存 question/{date}-{topic}-{index}.md（仅题目，不含答案；index 为该话题递增序号）
   │
   ▼
[topic-interviewer] ──Ingest──> 写 wiki/topics（题目登记 + 参考答案）+ index + log
   │
   ▼
通知用户作答 -> 用户作答
   │
   ▼
[topic-interviewer] ──Query(检索答案)──> 读 wiki/topics ──> 已登记参考答案
   │
   ▼
评分（以 wiki 参考答案为基准）+ 写 evaluation 到 question 文件
   │
   ▼
（可选）若评分中发现更优答案 -> Ingest 更新 wiki 参考答案
```

题库生成：读 `wiki/topics/*` + `wiki/banks/*` -> Query 去重 -> 生成精选题集（含参考答案）-> 归档到 `wiki/banks/{name}.md` + Ingest 新题回话题页。

## 关键约束

- **仅处理整份 md / 纯文本文件**，使用 Read/Write/Edit/Grep/Glob，不引入文件切分、向量化、外部检索引擎。
- 导航靠 `wiki/index.md`（中等规模 ~数百页内够用）。
- 历史题目不回填，wiki 从空开始，只登记今后生成的题目。

## 改动同步约定

修改 wiki 相关结构/流程时，同步更新：`ARCHITECTURE.md`、`docs/wiki-design.md`、`wiki/SCHEMA.md`、`AGENTS.md`、`.opencode/.skills/wiki/SKILL.md` 中受影响的部分。
