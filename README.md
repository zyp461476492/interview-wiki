# interview-wiki

基于 [LLM Wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 的面试题知识库与练习系统。LLM 增量维护一个持久 wiki（话题 + 题目 + 参考答案），用于生成去重、评分基准与题库编排；用户围绕 wiki 进行面试练习。

> 原始模式存档见 [`docs/llm-wiki-original.md`](docs/llm-wiki-original.md)。

## 核心思想

不同于每次从原始文档临时检索的 RAG，本系统让 LLM **增量维护一个持久的 markdown wiki**：每生成一批题目就登记进 wiki，下次同话题生成时先检索去重、评分时从 wiki 取回参考答案作为稳定基准。知识随使用复利积累，越用越准、越用越全。

## 三层架构

| 层 | 目录 | 说明 |
| --- | --- | --- |
| **Raw sources** | `raw/` + `question/` | 不可变来源：`raw/` 放用户策展的外部源材料（LLM 只读），`question/` 放练习会话交互记录 |
| **Wiki** | `wiki/` | LLM 生成维护的持久知识库：话题页（知识体系+题目登记+参考答案）/ 题库页 / 索引 / 日志 |
| **Schema** | `AGENTS.md` + `wiki/SCHEMA.md` + `.opencode/skills/` | 约定与工作流 |

## 目录结构

```
resume-agent/
├── AGENTS.md                       # 常驻规则层（始终加载）
├── ARCHITECTURE.md                 # 架构记录
├── docs/                           # 设计文档
│   ├── wiki-design.md              # wiki 系统设计
│   └── llm-wiki-original.md        # LLM Wiki 原始模式存档
├── raw/                            # Raw sources 层：用户策展的外部源材料（LLM 只读）
├── wiki/                           # Wiki 层（LLM 生成维护）
│   ├── SCHEMA.md                   # 规格：目录布局/页面格式/ID 约定
│   ├── index.md                    # 内容索引
│   ├── log.md                      # append-only 操作日志
│   ├── topics/                     # 话题页
│   └── banks/                      # 题库页
├── question/                       # 练习会话（交互记录）
└── .opencode/skills/
    ├── wiki/SKILL.md               # 三操作：Ingest / Query / Lint
    ├── topic-interviewer/          # 面试题生成与评分
    └── bank-generator/             # 题库生成（选题+补新题+归档 banks）
```

## 基本使用流程

### 面试练习（主流程）

在 [opencode](https://opencode.ai) 中打开本项目，技能会按需自动触发。典型流程：

1. **发起练习**：告诉 AI 一个技术话题，可选指定题目数量。
   ```
   练习一下 Java 并发
   生成 10 道 Redis 持久化的题
   ```
   未指定数量时默认 8 道。

2. **AI 生成题目**：AI 先 Query wiki 去重（避开已出题目、优先未覆盖考点），再生成题目与参考答案。
   - 题目保存到 `question/{yyyy-MM-dd}-{topic}-{index}.md`（**不含答案**，避免你提前看到）。
   - 参考答案同步 Ingest 进 `wiki/topics/{topic}.md`（话题页 `#### 参考答案`）。
   - AI 会告知你题目文件路径。

3. **作答**：打开题目文件，在每题 `### A：` 下方填写自己的回答。

4. **提交评分**：作答完成后，在对话中回复「完成」。

5. **AI 评分反馈**：AI 从 wiki 取回已登记参考答案作为基准，逐题打分，在文件中补写每题 `### evaluation`（得分/点评/AI 参考答案），并汇总总成绩（X / 100）。

> 示例见 [`question/2026-07-30-java基础-2.md`](question/2026-07-30-java基础-2.md)。

### 流程图

```
用户给话题（可选数量）
   │
   ▼
Query wiki（去重）─> 读 topics + index ─> 已出题目/考点
   │
   ▼
生成 N 题 + 参考答案（避开重复）─> 保存 question/（仅题目）+ Ingest wiki（含答案）
   │
   ▼
用户作答 ─> 回复「完成」
   │
   ▼
Query wiki（检索答案）─> 评分 ─> 写 evaluation 到 question 文件 ─> 汇总总成绩
```

## 其他操作

### 投喂外部源材料（可选）

想让 wiki 吸收某份外部材料的知识（补充话题页「知识体系」考点）：

1. 把源材料（文章/笔记/PDF 转文本等）放入 `raw/`（建议命名 `{话题}-{slug}.md`）。
2. 告诉 AI：「Ingest `raw/xxx.md` 到 {话题}」。
3. AI 读取 raw 文件（只读不改），提取考点补进话题页，并在 `wiki/log.md` 记录来源。

> `raw/` 不进 wiki 索引，溯源靠 log 与话题页「来源」。详见 [`raw/README.md`](raw/README.md)。

### 健康检查（Lint）

随时让 AI 检查 wiki 健康度：检查重复/近似题、参考答案缺失、覆盖缺口、孤立页、索引不同步、断链等，并按确认后修复。

```
lint 一下 wiki
检查 wiki 健康度
```

### 查询已出题目

```
Java 基础 已经出过哪些题？
```
AI 会 Query wiki 列出该话题已登记题目与考点覆盖情况。

### 生成题库

基于 wiki 已登记题目生成精选题集，归档到 `wiki/banks/`（引用已登记题，不足时补新题并回写话题页）：

```
生成 50 道 Java 高级题库
把 java基础 整理成一套 30 题题库
```

> 题库格式见 [`wiki/SCHEMA.md`](wiki/SCHEMA.md)，流程见 [`.opencode/skills/bank-generator/SKILL.md`](.opencode/skills/bank-generator/SKILL.md)。

## wiki 三操作

| 操作 | 触发时机 | 简述 |
| --- | --- | --- |
| **Query** | 生成题目前；评分前 | 去重模式取已出题目/考点；答案检索模式取回参考答案 |
| **Ingest** | 生成题目后；评分后；放入 raw 源材料时 | 创建/更新话题页（题目登记+参考答案），更新 index 与 log |
| **Lint** | 用户请求时 | 健康检查并修复 |

详见 [`.opencode/skills/wiki/SKILL.md`](.opencode/skills/wiki/SKILL.md)。

## 关键约束

- 仅处理整份 md / 纯文本，不引入文件切分、向量化、外部检索引擎；导航靠 `wiki/index.md`。
- `raw/` 为只读源层，LLM 不得修改；联网搜索结果不落盘。
- 参考答案的权威来源是 wiki 话题页 `#### 参考答案`，`question/` 中的 AI 参考答案为评分时从 wiki 取回的副本。

## 进一步阅读

- [`ARCHITECTURE.md`](ARCHITECTURE.md) — 整体架构与数据流
- [`docs/wiki-design.md`](docs/wiki-design.md) — wiki 系统完整设计
- [`wiki/SCHEMA.md`](wiki/SCHEMA.md) — 页面格式与 ID 约定
- [`AGENTS.md`](AGENTS.md) — 常驻规则层
