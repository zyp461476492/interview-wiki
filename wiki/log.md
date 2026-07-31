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
