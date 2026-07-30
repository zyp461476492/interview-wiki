# Wiki 系统设计文档

> 本文档记录面试题 wiki 系统的完整设计，供跨会话执行与维护参照。
> 若设计有变更，请同步更新本文档、`ARCHITECTURE.md`、`wiki/SCHEMA.md`、`AGENTS.md`。

## 1. 背景与动机

项目原有一个 `topic-interviewer` 技能：接收技术话题 -> 检索考点 -> 生成 8 道面试题 -> 存到 `question/` -> 用户作答 -> 评分。

问题：**无去重机制**。同一话题多次生成会产生概念重叠的题目（已有的两份「Java 基础」中 HashMap、并发集合重复出现），且无持久化的话题/题目登记，知识无法复利积累。

## 2. 目标

- 持久化积累「话题 + 题目」，作为去重依据与题库来源。
- 生成新题前先检索 wiki，避免重复。
- 后续基于 wiki 内容生成题库（精选题集）。

## 3. 非目标

- 不做向量化检索 / RAG 基础设施。
- 不引入文件切分 / 外部检索引擎。
- 不回填历史题目（wiki 从空开始）。

## 4. 理论基础：LLM Wiki 模式

源自 [Karpathy 的 LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。核心：LLM 增量维护一个持久、可复利积累的 markdown 知识库，而非每次从原始文档临时检索。

**三层：**

- **Raw sources**：不可变来源，LLM 只读。
- **Wiki**：LLM 生成维护的 markdown 文件集合（摘要/实体页/概念页/索引/日志）。
- **Schema**：约定文档，告诉 LLM 如何维护 wiki。

**三操作：**

- **Ingest**：读来源 -> 更新 wiki 页面 -> 更新索引 -> 追加日志。
- **Query**：读索引 + 相关页 -> 综合作答（好的答案可归档回 wiki）。
- **Lint**：定期健康检查（矛盾/过期/孤立/缺口）。

**两特殊文件：**

- **index.md**：内容导向目录。
- **log.md**：append-only 时间线，条目以 `## [date] op | subject` 开头便于检索。

## 5. 模式映射到面试题领域

| LLM Wiki 层  | 本项目对应                                                                          |
| ------------ | ----------------------------------------------------------------------------------- |
| Raw sources  | 互联网检索结果 + 练习会话 `question/*.md`（含 AI 参考答案）                         |
| Wiki         | `wiki/`（话题页 + 题库页 + 索引 + 日志）                                            |
| Schema       | `AGENTS.md`（规则）+ `wiki/SCHEMA.md`（规格）+ `opencode/.skills/wiki/SKILL.md`（操作） |

**产物归属：**

- 话题页（知识体系 + 题目登记）：持久知识，去重依据 -> `wiki/topics/`
- 题库（精选题集）：生成产物，归档回 wiki -> `wiki/banks/`
- 练习会话（题目+作答+评分+参考答案）：个性化交互记录，ingest 的 raw source -> `question/`（wiki 外）

> 题库放进 wiki 而非独立目录，遵循 Karpathy「有价值的生成产物归档回 wiki 作为新页面」原则：题库是去个性化的可复用参考知识，应被 index 索引、被话题页反向链接、避免重复生成。

## 6. 目录结构

```
resume-agent/
├── AGENTS.md                       # 常驻规则层（始终加载）
├── ARCHITECTURE.md                 # 架构记录
├── docs/
│   └── wiki-design.md              # 本文件
├── wiki/
│   ├── SCHEMA.md                   # 规格：目录布局/页面格式/ID 约定
│   ├── index.md                    # 内容索引（topics + banks）
│   ├── log.md                      # append-only 操作日志
│   ├── topics/                     # 话题页：知识体系 + 题目登记
│   └── banks/                      # 题库页：精选题集
├── question/                       # 练习会话（raw source / 交互记录，wiki 外）
└── opencode/.skills/
    ├── wiki/SKILL.md               # 三操作：Ingest / Query / Lint
    └── topic-interviewer/
        ├── SKILL.md                # 接入 Query/Ingest
        └── references/question-template.md
```

## 7. 页面格式

详见 `wiki/SCHEMA.md`。要点：

- **话题页** `topics/{topic}.md`：frontmatter（topic/created/updated/question_count）+ 知识体系（考点大纲）+ 题目登记（每题一节 `### {ID}`，含难度/考点/题目/首次生成/来源）+ 关联（`[[话题]]` 链接）。
- **题库页** `banks/{name}.md`：frontmatter（bank/size/created/topics）+ 说明 + 题目列表（每题注明难度/考点，引用已登记 ID 或新题）。
- **index.md**：topics 表 + banks 表。
- **log.md**：append-only，`## [yyyy-MM-dd] op | subject`。

## 8. ID 约定

- 话题页文件名 = 话题文本（如 `java基础.md`）。
- 题目 ID = `{topic}-{seq}`，话题内三位自增（`java基础-001`）。
- 题库页文件名 = 题库名（如 `java高级50题.md`）。

## 9. 三操作详细步骤

### 9.1 Query（检索去重）

1. Read `wiki/index.md`，判断 `wiki/topics/{topic}.md` 是否存在。
2. 存在则 Read 它，提取题目登记全部条目（ID/考点/题目）与知识体系考点大纲；不存在则标记新话题。
3. 输出 `{已出题目清单, 已覆盖考点, 未覆盖考点}`，生成时避开已出题、优先覆盖未覆盖考点。

### 9.2 Ingest（回写登记）

1. 话题页不存在 -> 创建（frontmatter + 由考点提炼的知识体系 + 题目登记，ID 从 `{topic}-001` 起）。
2. 话题页存在 -> 续编 ID（`{topic}-{max+1}`），追加题目登记，更新 frontmatter（updated/question_count），补充新考点进知识体系。
3. 更新 `index.md` 话题行（题量/更新时间/摘要）。
4. 追加 `log.md`：`## [date] ingest | {topic}` + 明细。
5. 题库场景：新题 Ingest 回话题页，题库归档为 `wiki/banks/{name}.md`，更新 index 的 banks 表。
6. 幂等：同来源重复 Ingest 按「来源 + 题目正文」判重，已存在则跳过。

### 9.3 Lint（健康检查）

1. Read `index.md` + Glob 读取全部 `topics/*.md`、`banks/*.md`。
2. 检查：重复/近似题、覆盖缺口、孤立页、索引不同步、断链。
3. 输出报告 + 修复建议；经确认后修复，每项追加 `## [date] lint | ...` 到 log。

## 10. topic-interviewer 集成

在原有 6 步流程中插入 Query/Ingest：

1. 接收话题
2. 检索准备：**Query wiki**（读 index + 话题页，取已出题目/考点）+ 互联网搜索补充考点
3. 生成题目：避开 wiki 已登记题目，优先覆盖未出题考点；保存到 `question/{date}-{topic}-{rand}.md`
4. **回写登记（Ingest）**：8 题登记进 `wiki/topics/{topic}.md`（不存在则创建含知识体系），更新 index + log
5. 通知用户作答
6. 等待作答
7. 评分反馈

## 11. 题库生成

基于 `wiki/topics/*.md` 的知识体系 + 已登记题目，生成跨话题或单话题的大规模题库，归档到 `wiki/banks/{name}.md`。生成时同样 Query 去重，新题 Ingest 回对应话题页。当前仅建 `wiki/banks/` 占位并在 SCHEMA 描述工作流，专用技能可后续再加。

## 12. 决策记录

| 决策             | 选择                              | 理由                                                                        |
| ---------------- | --------------------------------- | --------------------------------------------------------------------------- |
| 基础形态         | 纯 markdown 知识库（LLM Wiki 模式） | 用户指定（Karpathy gist）                                                   |
| 操作落地形式     | skill + AGENTS.md 皆做            | 操作做成 `wiki` skill 可触发；常驻规则放 AGENTS.md 始终加载                 |
| 题库位置         | `wiki/banks/`（内化进 wiki）      | 符合「生成产物归档回 wiki」原则，可索引/反链/避免重复生成                    |
| 历史回填         | 不回填                            | 用户决定，wiki 从空开始                                                     |
| 检索工具         | 不用，仅整文件 Read/Grep          | 用户决定，md/纯文本够用，靠 index.md 导航                                   |
| 题目登记形式     | 节（`### {ID}`）而非表格          | 便于长题目正文与追加                                                        |

## 13. 后续工作

- 题库生成专用技能（当前仅占位 + SCHEMA 描述工作流）。
- 概念页（`wiki/concepts/`，跨话题复用高频考点）- 可选增强。
- 规模增长后评估是否引入轻量搜索（当前 index.md 足够）。
