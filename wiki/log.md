# Wiki 日志

> append-only 操作日志。每条以 `## [yyyy-MM-dd] op | subject` 开头，便于检索。
> op ∈ {ingest, query, lint}。

<!-- 示例：
## [2026-07-30] ingest | Java 基础
- 首次建立话题页 topics/java基础.md
- 登记题目 8 道（java基础-001 ~ java基础-008）
- 来源：question/2026-07-30-java基础-6617.md
-->

## [2026-07-31] ingest | Java 基础
- 首次建立话题页 topics/java基础.md（含知识体系大纲）
- 登记/新增题目 8 道（java基础-001 ~ java基础-008，含参考答案）
- 来源：question/2026-07-30-java基础-1.md
- 注：该文件为未评分预生成题目，参考答案为本次 Ingest 时同步补写

## [2026-07-31] ingest | Java 基础
- 续编登记题目 8 道（java基础-009 ~ java基础-016，含参考答案）
- 来源：question/2026-07-30-java基础-2.md
- 注：该文件已评分，参考答案取自 evaluation 区 AI 参考答案（评分后回写）

## [2026-07-31] query | Java 基础
- 生成前检索：已登记 16 题（java基础-001 ~ 016），覆盖 String 不可变/final三兄弟/泛型擦除/sync与volatile/类加载/Full GC/单例/JMM/==与equals/装箱拆箱/HashMap/接口抽象类/异常/OOM/ConcurrentHashMap/SOLID
- 避开以上角度，选择 10 个未覆盖高频考点：ArrayList/LinkedList、值传递、反射、序列化、深浅拷贝、Stream/Lambda、ThreadLocal、对象创建方式、内部类、注解机制

## [2026-07-31] ingest | Java 基础
- 新增登记题目 10 道（java基础-017 ~ java基础-026，含参考答案）
- 同步更新：知识体系大纲（新增核心机制/对象创建与复制/新特性等考点）、题目索引表
- 来源：question/2026-07-31-java基础-3.md
- 注：该文件为未评分预生成题目，参考答案为本次 Ingest 时同步补写

## [2026-08-04] ingest | MySQL
- 首次建立话题页 topics/mysql.md（含知识体系大纲）
- 登记/新增题目 10 道（mysql-001 ~ mysql-010，含参考答案）
- 来源：question/2026-08-04-mysql-1.md
- 同步更新：wiki/index.md 话题页表新增 MySQL 行

## [2026-08-04] lint | Java 基础
- 校验通过：题目索引表 26 行 = 题目登记 26 节 = frontmatter question_count 26 = index.md 题量 26；26 题均有「#### 参考答案」
- 修复断链：topics/java基础.md 关联区移除指向不存在话题页的 [[java并发]]、[[jvm]]（关联区只链接已存在话题页，新话题建立时补链）
- 修复 SCHEMA 示例自洽：话题页示例 question_count 0 -> 2
- 规范明确：Query 默认不记 log，仅去重结果显著影响生成决策时可记一条（已写入 SCHEMA 与 wiki SKILL）
