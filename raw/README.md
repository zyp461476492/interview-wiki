# Raw Sources

> 本目录是 LLM Wiki 模式中的 **Raw sources 层**：用户策展的不可变外部源材料。
> LLM 只读，永不修改；wiki 是由 LLM 维护的产物层（见 `wiki/`），二者分离。

## 用途

存放供 Ingest 的原始源材料：文章、笔记、剪藏、PDF 转出的文本等。当你想让 wiki 吸收某份外部材料的知识（补充话题页「知识体系」考点、登记题目等）时，把文件放进本目录，再让 LLM 执行 wiki 的 Ingest 操作。

## 约束

- **只读**：LLM 读取本目录文件用于 Ingest，但不得修改/删除其中任何文件。
- **不可变**：源材料一经放入即视为定稿；如需更新请放入新文件而非改写旧文件，保持溯源可靠。
- **纯文本优先**：md / 纯文本最易处理；PDF 等需先转为文本（或由 LLM 读取后摘录）。
- **不落盘联网搜索结果**：topic-interviewer 流程中的互联网检索是临时的、不写入本目录；本目录只放用户主动提供的材料。

## 命名约定

扁平结构，文件名建议 `{话题或描述}-{slug}.md`，便于按话题检索：

```
raw/
├── README.md                          # 本文件
├── java并发-美团线程池文章.md
├── redis-持久化原理笔记.md
└── mysql-索引失效场景整理.md
```

- 话题名与 `wiki/topics/{topic}.md` 保持一致写法（如 `java并发`）。
- 一份材料横跨多个话题时，取主要话题或用通用描述命名即可，Ingest 时由 LLM 判定归属。

## 与 Ingest 的关系（可选）

本目录的使用是**可选约定**，不强制接入生成流程：

1. 用户将源材料放入 `raw/`。
2. 用户告知 LLM「Ingest `raw/xxx.md` 到 {topic}」。
3. LLM 执行 wiki 的 Ingest：读取 raw 文件 -> 提取考点补充进 `wiki/topics/{topic}.md` 的「知识体系」-> 在 `wiki/log.md` 记 `## [yyyy-MM-dd] ingest | {topic} (from raw/{file})`。
4. 溯源：话题页「知识体系」或相关条目可注明来源 `raw/{file}`；`raw/` 本身不进 `wiki/index.md`（index 只索引 wiki 页，raw 是源层）。

详见 `.opencode/.skills/wiki/SKILL.md` 的 Ingest 操作。
