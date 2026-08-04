---
topic: Java 基础
created: 2026-07-31
updated: 2026-07-31
question_count: 26
---

# Java 基础

## 知识体系

- 基础语法与类型（`==`/equals、hashCode 契约、八种基本类型、自动装箱/拆箱、包装类缓存、值传递/引用传递）
- 核心类（String/StringBuilder/StringBuffer、String 不可变）
- 面向对象（接口 vs 抽象类、OOP 设计思想、SOLID 原则、内部类）
- 对象创建与复制（对象创建方式、深拷贝/浅拷贝）
- 集合框架（HashMap 底层原理、数组+链表+红黑树、扩容机制、扰动函数、ArrayList/LinkedList）
- 泛型（类型擦除、泛型限制）
- 异常体系（Checked/Unchecked Exception、异常选型设计）
- 核心机制（反射机制、注解机制、序列化机制）
- 并发编程（synchronized/volatile、锁升级、JMM/happens-before、线程安全集合、ConcurrentHashMap、ThreadLocal、单例模式）
- 新特性（Java 8 Stream API、Lambda、函数式编程）
- JVM（类加载机制/双亲委派、Full GC 排查、OOM 排查）

## 题目索引

> 一题一行的轻量目录。去重 Query 只读本表 + 知识体系即可，无需读详情；答案检索用 Grep `^### {ID}` 定位后分段 Read。

| ID | 考点 | 难度 | 摘要 |
|----|------|------|------|
| java基础-001 | String 不可变 | 基础 | String/StringBuilder/StringBuffer 区别与不可变好处 |
| java基础-002 | final/finally/finalize | 基础 | 三者作用与关系、finally 不执行场景 |
| java基础-003 | 泛型/类型擦除 | 进阶 | 类型擦除机制、限制与坑、List\<String\> vs List\<Integer\> |
| java基础-004 | synchronized/volatile、锁升级 | 进阶 | 二者区别与场景、JDK1.6 锁升级过程 |
| java基础-005 | 类加载/双亲委派 | 进阶 | 类加载机制、双亲委派作用与打破场景 |
| java基础-006 | Full GC 排查 | 进阶 | 频繁 Full GC 排查思路、工具、常见原因 |
| java基础-007 | 单例模式 | 进阶 | 线程安全单例多种实现对比（DCL/内部类/枚举等） |
| java基础-008 | JMM/volatile/happens-before | 进阶 | JMM、volatile 可见性/有序性、happens-before |
| java基础-009 | ==/equals、hashCode | 基础 | == 与 equals 区别、重写 equals 必须重写 hashCode |
| java基础-010 | 基本类型/装箱拆箱 | 基础 | 八种基本类型、int/Integer、装箱拆箱机制与缓存 |
| java基础-011 | HashMap 底层 | 进阶 | 数组+链表+红黑树、1.8 优化、扩容机制 |
| java基础-012 | 接口 vs 抽象类 | 基础 | 区别对比与选型场景 |
| java基础-013 | 异常体系 | 基础 | Checked/Unchecked 区别与设计选型 |
| java基础-014 | OOM 排查 | 进阶 | OOM 类型判别、排查思路与工具 |
| java基础-015 | 线程安全集合/ConcurrentHashMap | 进阶 | 线程安全集合、读多写少 Map 选型与底层机制 |
| java基础-016 | OOP/SOLID | 进阶 | 面向对象思想、SOLID 原则与项目案例 |
| java基础-017 | ArrayList/LinkedList | 进阶 | 底层数组vs链表、增删改查性能对比与场景选型 |
| java基础-018 | 值传递/引用传递 | 基础 | Java 只有值传递、对象属性修改可见性分析 |
| java基础-019 | 反射机制 | 进阶 | 反射能力、应用场景、性能问题与优化 |
| java基础-020 | 序列化机制 | 进阶 | Serializable、serialVersionUID、transient、安全问题 |
| java基础-021 | 深拷贝/浅拷贝 | 进阶 | 区别与实现方式（clone/序列化）对比 |
| java基础-022 | Stream API/Lambda | 进阶 | map/filter/reduce/collect、短路操作、懒加载 |
| java基础-023 | ThreadLocal | 进阶 | 原理、内存泄漏、清理、InheritableThreadLocal |
| java基础-024 | 对象创建方式 | 基础 | new/反射/clone/反序列化/Unsafe 是否调用构造方法 |
| java基础-025 | 内部类 | 基础 | 四种内部类特点与场景、匿名内部类访问 final |
| java基础-026 | 注解机制 | 进阶 | 元注解含义、自定义注解与反射读取 |

## 题目登记

### java基础-001
- 难度：基础 | 考点：String/StringBuilder/StringBuffer、String 不可变
- 题目：**`String`、`StringBuilder`、`StringBuffer` 三者有什么区别？为什么 Java 要把 `String` 设计成不可变（immutable）的？不可变带来了哪些好处？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **三者区别**：`String` 不可变（内部 `final char[]`，JDK9+ 为 `byte[]`）；`StringBuilder` 可变、非线程安全、性能好；`StringBuffer` 可变、线程安全（方法用 `synchronized` 修饰）、性能略低。
- **String 设计成不可变的原因/好处**：
  1. **字符串常量池复用**：不可变才能安全共享同一常量池实例，节省内存。
  2. **线程安全**：不可变对象天然线程安全，无需同步。
  3. **hashCode 缓存**：计算一次后不会变，适合做 HashMap 的 key，提高哈希性能。
  4. **安全性**：防止敏感数据（如数据库连接串、类加载器加载的类名）被恶意篡改。
  5. **不可变保证 `equals`/`hashCode` 语义稳定**，作为集合 key 不会出现"放入后取不出"的问题。

### java基础-002
- 难度：基础 | 考点：final/finally/finalize
- 题目：**请分别说明 `final`、`finally`、`finalize` 的作用。它们之间有任何关系吗？`finally` 块在哪些情况下不会被执行？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **final**：修饰符。修饰类→不可继承；修饰方法→不可重写；修饰变量→引用不可重新赋值（基本类型值不可变，对象引用不可变但对象内容可变）。
- **finally**：异常处理机制的一部分，`try-catch-finally` 中无论是否抛出异常都会执行，通常用于资源关闭。
- **finalize**：`Object` 的方法，GC 回收对象前由 JVM 调用（最多一次），用于释放资源。JDK9 已废弃，不推荐使用（执行时间不确定、可能导致复活对象、性能差），推荐用 `try-with-resources` 或 `Cleaner` 替代。
- **三者无直接关系**，仅命名相似。
- **finally 不执行的情况**：
  1. `try`/`catch` 块中调用了 `System.exit()` 导致 JVM 直接退出。
  2. 执行 `try`/`catch` 块前就抛出异常或 return（即根本没进入 try 块）。
  3. 当前线程被杀死（如守护线程在所有非守护线程结束后被强制终止）。
  4. `try`/`catch` 块中出现无限循环或死锁，永远到不了 finally。
  - 注意：`try`/`catch` 中 `return` **不会**阻止 finally 执行，finally 会在 return 之前执行。

### java基础-003
- 难度：进阶 | 考点：泛型、类型擦除
- 题目：**请说明 Java 泛型的实现机制。什么是「类型擦除（Type Erasure）」？类型擦除会带来哪些限制和坑？`List<String>` 和 `List<Integer>` 的 `class` 是否相等，为什么？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **泛型机制**：Java 泛型是编译期的类型检查机制，运行时不存在泛型类型信息（伪泛型）。
- **类型擦除**：编译后泛型类型参数被擦除，`List<String>`、`List<Integer>` 都被擦除为原生类型 `List`，类型参数替换为上界（默认 `Object`）。
- **`List<String>.class == List<Integer>.class` 为 true**：擦除后运行时只有一个 `List` 类，无法区分泛型参数。
- **限制与坑**：
  1. 不能用基本类型作泛型参数（需用包装类，如 `List<Integer>` 而非 `List<int>`）。
  2. 不能 `new T()`（类型被擦除，运行时不知道 T 是什么）。
  3. 不能 `new T[]`（数组协变与泛型不兼容）。
  4. 运行时不能用 `instanceof List<String>`，只能 `instanceof List`。
  5. 静态字段/方法不能引用类的泛型类型参数。
  6. 重载冲突：`void m(List<String>)` 与 `void m(List<Integer>)` 擦除后签名相同，无法重载。
  7. 泛型不能继承 `Throwable`（不能创建泛型异常类）。

### java基础-004
- 难度：进阶 | 考点：synchronized vs volatile、锁升级
- 题目：**`synchronized` 和 `volatile` 的区别是什么？各自适用于什么场景？请详细说明 `synchronized` 在 JDK 1.6 之后的「锁升级」过程（偏向锁 -> 轻量级锁 -> 重量级锁）。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **区别**：
  - `synchronized`：互斥锁，保证**原子性 + 可见性 + 有序性**；修饰方法或代码块；可重入；发生竞争时线程阻塞。
  - `volatile`：轻量级同步机制，保证**可见性 + 有序性**（禁止指令重排序），**不保证原子性**；不阻塞；适用于状态标志。
- **适用场景**：`synchronized` 用于复合操作/临界区互斥；`volatile` 用于一写多读的可见性标志（如 `boolean stop`）。
- **锁升级过程（JDK 1.6+，锁存储在对象头 Mark Word 中）**：
  1. **无锁**：对象刚创建，Mark Word 无锁信息。
  2. **偏向锁**：假设只有一个线程访问，首次加锁在 Mark Word 记录线程 ID，后续同线程无需 CAS。出现竞争时撤销偏向。
  3. **轻量级锁**：多个线程交替访问（低竞争），用 CAS 自旋获取锁，不阻塞线程；自旋失败则升级。
  4. **重量级锁**：高竞争、自旋失败，升级为 OS 级互斥量（monitor），未获锁线程进入阻塞队列，依赖操作系统调度，开销大。
  - 升级不可逆（偏向锁可批量撤销重偏向但一般不可降级）。

### java基础-005
- 难度：进阶 | 考点：类加载机制、双亲委派
- 题目：**请说明 Java 的类加载机制与「双亲委派模型」。双亲委派的作用是什么？请举出至少两种打破双亲委派模型的典型场景，并说明其原理。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **类加载过程**：加载（查找字节流生成 Class）→ 验证 → 准备（静态变量分配内存赋零值）→ 解析（符号引用转直接引用）→ 初始化（执行 `<clinit>`）。
- **双亲委派模型**：类加载请求先委托给父加载器，父加载器无法加载时子加载器才尝试加载。层级：BootstrapClassLoader → ExtClassLoader/PlatformClassLoader → AppClassLoader → 自定义。
- **作用**：①安全，防止核心类（如 `java.lang.Object`）被自定义类篡改；②避免重复加载，保证类的唯一性。
- **打破场景**：
  1. **JDBC / SPI**：核心类 `DriverManager`（Bootstrap 加载）需要加载厂商实现类（在 classpath 下，Bootstrap 看不到），通过 `Thread.currentThread().getContextClassLoader()`（通常为 AppClassLoader）反向加载，打破自上而下委派。
  2. **Tomcat**：每个 Web 应用有独立 `WebappClassLoader`，优先加载自己 WEB-INF/classes（违反"先父后子"），实现应用间类隔离。
  3. **OSGi / 模块化**：网状类加载模型，按模块/包委托，彻底打破树状双亲委派。
  4. **热部署**：自定义 ClassLoader 重新加载类实现热更新。

### java基础-006
- 难度：进阶 | 考点：Full GC 排查、JVM 调优
- 题目：**某线上服务接口响应时间突然变慢，经初步排查怀疑是频繁 Full GC 导致。请描述你的完整排查思路、使用的工具，以及常见引发 Full GC 的原因有哪些？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **排查思路**：
  1. **确认是否频繁 Full GC**：`jstat -gcutil <pid> 1000` 观察 FGC 次数/耗时、各代占用变化；或查看 GC 日志。
  2. **定位触发原因**：看是老年代不足、Metaspace 不足还是显式 `System.gc()`。
  3. **dump 分析**：`jmap -dump:format=b,file=heap.hprof <pid>` 导出堆转储，用 MAT/JProfiler 分析大对象、内存泄漏。
  4. **结合线程与业务**：`jstack` 查看线程；排查是否有大对象、缓存无限增长、大列表查询。
- **常用工具**：`jstat`（GC 统计）、`jmap`（堆信息/dump）、`jstack`（线程）、`jinfo`（JVM 参数）、GC 日志分析（`-Xlog:gc*`）、MAT/JProfiler/VisualVM（堆分析）、Arthas（在线诊断）。
- **常见 Full GC 原因**：
  1. 老年代空间不足（大对象直接进老年代、长期存活对象多）。
  2. Metaspace 空间不足（动态生成大量类，如 CGLIB/反射）。
  3. 内存泄漏（静态集合/缓存无限增长、资源未关闭）。
  4. `System.gc()` 被显式调用（可加 `-XX:+DisableExplicitGC` 禁用）。
  5. CMS 的 Concurrent Mode Failure / Promotion Failed（老年代空间不足以容纳晋升对象，触发 Full GC）。
  6. 空间分配担保失败（Minor GC 前检查老年代连续空间不足）。

### java基础-007
- 难度：进阶 | 考点：单例模式、线程安全
- 题目：**在项目中需要实现一个「线程安全的单例模式」。请给出至少两种实现方式（如饿汉式、双重检查锁、静态内部类、枚举等），并对比分析它们的优缺点、线程安全性和延迟加载特性。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **饿汉式**：类加载时即创建实例。
  - 线程安全（类加载保证）；非延迟加载；无锁性能好。缺点：不管是否使用都创建，可能浪费资源。
- **双重检查锁（DCL）**：`volatile` + `synchronized`，第一次检查为 null 才加锁，加锁后再检查。
  - 延迟加载、线程安全；`volatile` 防止指令重排序（new 对象的非原子操作）。缺点：实现稍复杂。
- **静态内部类**：利用类加载机制保证线程安全，内部类在被引用时才加载。
  - 延迟加载、线程安全、无锁、实现简洁。推荐方式之一。
- **枚举**：`enum` 天然单例。
  - 线程安全、防反射攻击、防序列化破坏、代码最简洁。**最佳实践**（Effective Java 推荐）。缺点：非延迟加载（枚举类加载即初始化）。

### java基础-008
- 难度：进阶 | 考点：JMM、volatile、happens-before
- 题目：**谈谈你对 Java 内存模型（JMM）的理解。`volatile` 是如何保证「可见性」和「有序性」的？「happens-before」原则在实践中有什么意义？请举例说明。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-1.md

#### 参考答案
- **JMM**：Java 内存模型定义了多线程间共享变量的访问规则——每个线程有工作内存（CPU 缓存/寄存器），共享变量在主内存，线程间通过主内存交互。JMM 规定何时将工作内存刷回主存、何时从主存读取，解决可见性与有序性问题。
- **volatile 保证可见性**：写 volatile 变量后强制刷新到主内存，并使其他线程工作内存中该变量副本失效（缓存行失效），读时从主内存重新加载。
- **volatile 保证有序性**：通过插入内存屏障（Memory Barrier）禁止编译器/CPU 对 volatile 变量前后的指令重排序。写 volatile 前的普通写不会重排到写 volatile 之后（StoreStore 屏障），读 volatile 后的普通读不会重排到读之前（LoadLoad 屏障）。
- **happens-before**：定义操作间的可见性偏序关系，若 A happens-before B，则 A 的结果对 B 可见。规则包括：
  1. 程序顺序规则（同线程内前操作 hb 后操作）。
  2. volatile 规则（volatile 写 hb 后续 volatile 读）。
  3. 锁规则（unlock hb 后续 lock）。
  4. 线程启动/终止规则。
  5. 传递性。
- **实践意义**：无需理解底层内存屏障，用 happens-before 规则推理代码线程安全。例如：线程 A 写 volatile `flag=true`（之前写入 `data`），线程 B 读到 `flag==true` 时，根据 volatile 规则 + 程序顺序 + 传递性，`data` 的写对 B 可见，无需对 `data` 加锁。

### java基础-009
- 难度：基础 | 考点：==/equals、hashCode 契约
- 题目：**Java 中 `==` 和 `equals()` 的区别是什么？为什么在重写 `equals()` 时通常也必须重写 `hashCode()`？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- `==`：基本类型比较值，引用类型比较内存地址（是否同一对象）。
- `equals()`：`Object` 中默认等价于 `==`，通常被重写为"逻辑相等"（内容相等）的比较。
- 重写 `equals` 必须重写 `hashCode` 的原因：
  1. 契约要求：`equals` 相等的两个对象必须具有相同 `hashCode`。
  2. 否则 `HashMap`/`HashSet` 会出错——两个 `equals` 相等的对象可能因 hashCode 不同被分到不同桶，导致"已存在"判断失败（例如 `HashSet` 中出现重复元素）。
  3. 反之，hashCode 相等的对象不一定 equals 相等（哈希冲突）。

### java基础-010
- 难度：基础 | 考点：基本类型、int/Integer、装箱拆箱
- 题目：**请列举 Java 的八种基本数据类型，并说明 `int` 与 `Integer` 的区别。自动装箱（autoboxing）和拆箱（unboxing）是在什么阶段、如何实现的？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- 八种基本类型：`byte(1)、short(2)、int(4)、long(8)、float(4)、double(8)、char(2)、boolean(1)`。
- `int` 是基本类型，默认值 0，不可为 null，存在栈/寄存器；`Integer` 是包装类，默认值 null，是对象，存在堆中，可用于泛型（如 `List<Integer>`）。
- 自动装箱/拆箱是**编译期**语法糖：编译器将 `Integer i = 10;` 转为 `Integer i = Integer.valueOf(10);`，将 `int n = i;` 转为 `int n = i.intValue();`。
- `Integer` 内置缓存（`IntegerCache`），范围 `-128 ~ 127`，该范围内 `valueOf` 返回同一对象，因此 `Integer a=127, b=127; a==b` 为 true，而 `128` 时为 false。

### java基础-011
- 难度：进阶 | 考点：HashMap 底层、红黑树、扩容机制
- 题目：**请详细说明 `HashMap` 的底层实现原理。JDK 1.8 相比 1.7 在结构上做了哪些优化？为什么引入红黑树？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- JDK 1.8 `HashMap` 底层为 `Node<K,V>[]` 数组 + 链表 + 红黑树。
- put 流程：对 key 做 hash 扰动 `(h = key.hashCode()) ^ (h >>> 16)`，定位桶下标 `(n-1) & hash`；空桶直接放入；否则遍历链表/树，key 相同则覆盖，不同则尾插。
- 1.8 相比 1.7 的优化：
  1. 引入红黑树：当链表长度 ≥ 8 且数组容量 ≥ 64 时，链表转为红黑树，查询从 O(n) 降为 O(log n)；当树节点 ≤ 6 时退化为链表。
  2. 1.7 扩容时头插法在多线程下会形成环形链表导致死循环，1.8 改为尾插法。
  3. 1.8 扩容时元素位置要么不变，要么 `原位置 + 旧容量`，优化了 rehash。
- 负载因子默认 0.75，阈值 = 容量 × 负载因子，超过则扩容（翻倍）。

### java基础-012
- 难度：基础 | 考点：接口 vs 抽象类
- 题目：**接口（interface）和抽象类（abstract class）有什么区别？请结合实际场景说明：在什么情况下应优先使用接口，什么情况下应优先使用抽象类？**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
| 维度 | 接口 | 抽象类 |
| --- | --- | --- |
| 继承 | 可多实现 | 单继承 |
| 成员变量 | `public static final` 常量 | 任意成员变量 |
| 方法 | 默认 `public abstract`，Java 8+ 支持 `default`/`static` | 可有抽象方法和具体方法 |
| 构造器 | 无 | 有 |
| 设计语义 | 表示"能做什么"（能力/契约） | 表示"是什么"（血缘/分类） |

- 优先用接口：定义跨类型的能力契约，如 `Comparable`、`Serializable`，让不相关类具备同一行为。
- 优先用抽象类：存在共享代码与状态时，如模板方法模式（`AbstractList` 提供基础实现，子类只实现 `get`/`size`）。

### java基础-013
- 难度：基础 | 考点：异常体系、Checked/Unchecked Exception
- 题目：**在 Java 异常体系中，Checked Exception 和 Unchecked Exception（RuntimeException）的区别是什么？它们各自适用于怎样的设计场景？举例说明你会在项目中如何选择。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- Checked Exception（受检异常）：继承自 `Exception` 但非 `RuntimeException`。编译器强制处理（`try-catch` 或 `throws`）。
  - 适用场景：可预期、可恢复的外部条件，调用方应处理。如 `IOException`（文件读取失败）、`ClassNotFoundException`。
  - 选择依据：当异常是业务流程的一部分、调用方有能力恢复时使用。
- Unchecked Exception（非受检异常）：继承自 `RuntimeException`。编译器不强制处理。
  - 适用场景：编程错误、不可恢复的运行时问题。如 `NullPointerException`、`IllegalArgumentException`、`IndexOutOfBoundsException`。
  - 选择依据：表示前置条件被破坏，应通过修复代码而非 catch 来解决。
- 项目示例：自定义业务异常 `BusinessException extends RuntimeException`，用于参数校验失败、业务规则不满足（如余额不足），避免污染方法签名；而调用外部 API 的 `IOException` 用 Checked 强制上层处理重试/降级。

### java基础-014
- 难度：进阶 | 考点：OOM 排查、JVM 工具
- 题目：**某线上 Java 服务频繁抛出 `OutOfMemoryError`，请描述你的完整排查思路，并说明你会使用哪些工具（命令行 / 可视化）来定位问题根因。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
1. **确认 OOM 类型**：看异常信息——`Java heap space`（堆溢出）、`Metaspace`（元空间）、`Direct buffer memory`（直接内存）、`GC overhead limit exceeded`（GC 耗时过高）。
2. **保留现场**：若服务还存活，立即 `jmap -dump:format=b,file=heap.hprof <pid>` 导出堆转储；建议启动时配置 `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=...` 自动 dump。
3. **监控与日志**：`jstat -gcutil <pid> 1000` 观察 GC 频率与各代占用；开启并分析 GC 日志（`-Xlog:gc*`）。
4. **堆转储分析**：用 **MAT (Memory Analyzer Tool)** / **JProfiler** / **VisualVM** 打开 hprof：
   - 查看 Dominator Tree 找占内存最大的对象。
   - 用 Leak Suspects 报告定位内存泄漏点。
   - 查看对象 GC Root 引用链，找出无法回收的原因。
5. **线程与业务定位**：`jstack <pid>` 查看线程状态；结合 Arthas 的 `dashboard`/`profiler` 在线诊断。
6. **根因分类**：内存泄漏（如静态集合无限增长、未关闭资源）-> 修复代码；内存确实不够 -> 调大 `-Xmx` 或优化数据结构。

### java基础-015
- 难度：进阶 | 考点：线程安全集合、ConcurrentHashMap
- 题目：**请列举 Java 中常见的线程安全集合。在「高并发读、少量写」的业务场景下，你会选择哪种 `Map` 实现？请给出选择理由，并说明其底层是如何保证线程安全与读性能的。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- 常见线程安全集合：
  - `ConcurrentHashMap`（并发哈希表）
  - `CopyOnWriteArrayList` / `CopyOnWriteArraySet`（写时复制）
  - `Vector`、`Hashtable`（遗留，性能差，不推荐）
  - `Collections.synchronizedXxx()`（包装器，粗粒度锁）
  - `ConcurrentLinkedQueue` / `ConcurrentLinkedDeque`（无界非阻塞队列）
  - `BlockingQueue` 体系（`ArrayBlockingQueue`、`LinkedBlockingQueue` 等）
- 「高并发读、少量写」Map 选型：**`ConcurrentHashMap`**。
  - 理由：读操作无锁（基于 volatile 保证可见性），写操作只锁单个桶（分段锁/CAS），读性能极高，适合读多写少。
  - 底层机制（JDK 1.8）：
    - 基于 `Node[]` 数组 + 链表 + 红黑树。
    - **读无锁**：`Node` 的 `val` 和 `next` 为 `volatile`，保证读可见性；读时若遇到扩容转发节点（`ForwardingNode`）会转到新表读取。
    - **写细粒度锁**：put 时对桶首节点 `synchronized`（1.7 是 Segment 分段锁），不影响其他桶；初始化与扩容用 CAS + 自旋。
    - 扩容支持多线程协助迁移（`transfer`），降低单次扩容停顿。

### java基础-016
- 难度：进阶 | 考点：OOP 设计思想、SOLID 原则
- 题目：**谈谈你对 Java「面向对象」设计思想的理解。在实际项目中，你是如何运用 OOP 设计原则（如单一职责、开闭原则、里氏替换等）来提升代码可维护性的？请结合一个你经历过的具体案例说明。**
- 首次生成：2026-07-30 | 来源：question/2026-07-30-java基础-2.md

#### 参考答案
- **面向对象思想**：通过封装、继承、多态、抽象，将现实世界建模为对象，以"数据 + 行为"为单位组织代码，降低复杂度、提升复用性与可扩展性。
- **核心原则（SOLID）**：
  - **S**ingle Responsibility（单一职责）：一个类只做一件事，降低耦合。
  - **O**pen-Closed（开闭原则）：对扩展开放、对修改关闭，通过抽象与多态新增功能而非改动老代码。
  - **L**iskov Substitution（里氏替换）：子类必须能替换父类而不破坏行为，保证继承体系的正确性。
  - **I**nterface Segregation（接口隔离）：接口细化，避免实现类被迫依赖不需要的方法。
  - **D**ependency Inversion（依赖倒置）：依赖抽象而非具体实现，便于替换与测试。
- **案例示例**：
  - 订单促销系统：将"计算折扣"抽象为 `DiscountStrategy` 接口（开闭原则 + 单一职责），新增"满减""打折""优惠券"策略只需新增实现类，无需修改 `OrderService`；通过 `DiscountStrategyFactory` 依赖接口注入（依赖倒置），便于单元测试替换 Mock。
  - 效果：新增促销规则不影响核心下单流程，可测试性强，符合开闭原则。

### java基础-017
- 难度：进阶 | 考点：ArrayList/LinkedList、List 实现
- 题目：**`ArrayList` 和 `LinkedList` 有什么区别？请从底层数据结构、增删改查性能、内存占用、应用场景等方面进行对比。为什么说 `ArrayList` 的随机访问是 O(1) 而 `LinkedList` 是 O(n)？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **底层数据结构**：`ArrayList` 基于动态数组（`Object[]`），`LinkedList` 基于双向链表（`Node` 有 prev/next 指针）。
- **随机访问性能**：
  - `ArrayList.get(i)` 直接通过数组下标访问，O(1)；`LinkedList.get(i)` 需从表头/表尾遍历到第 i 个节点，O(n)。
  - 这就是 ArrayList 随机访问 O(1)、LinkedList O(n) 的原因：数组支持随机寻址，链表只能顺序遍历。
- **增删性能**：
  - `ArrayList` 尾部插入均摊 O(1)，中间插入/删除需移动元素 O(n)；`LinkedList` 在已知节点处插入/删除 O(1)（但定位节点仍 O(n)）。
- **内存占用**：`ArrayList` 连续内存、空间紧凑；`LinkedList` 每个节点额外存储前驱/后继指针，内存开销更大，且内存不连续缓存不友好。
- **应用场景**：读多写少、随机访问多 -> `ArrayList`（实际开发默认选择）；频繁在头尾插入删除、且无需随机访问 -> `LinkedList`（如实现队列/双端队列，但实践中 `ArrayDeque` 通常更优）。

### java基础-018
- 难度：基础 | 考点：值传递/引用传递
- 题目：**Java 是「值传递」还是「引用传递」？请结合代码示例说明：当一个对象作为参数传入方法后，方法内部修改该对象的属性，外部是否可见？如果方法内部让该参数引用指向一个新对象，外部会受影响吗？为什么？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **Java 只有值传递**：方法参数传递的是实参的「副本」。基本类型传值的副本，引用类型传引用（地址）的副本。
- **修改对象属性可见**：传入对象后，方法内通过该引用修改对象属性，外部可见--因为方法内的引用副本与外部引用指向**同一个对象**。
- **重新指向新对象不影响外部**：方法内让参数引用指向新对象（`param = new Object()`），只改变了副本的指向，外部引用仍指向原对象，不受影响。
- 示例：
  ```java
  void modify(User u) { u.setName("b"); }      // 外部可见，u 副本与原引用同对象
  void reassign(User u) { u = new User("c"); }  // 外部不可见，仅副本改指向
  ```
- 结论：Java 的引用类型传的是「引用的值（地址）的副本」，本质是值传递，不存在让方法内改动直接作用到外部引用本身的引用传递。

### java基础-019
- 难度：进阶 | 考点：反射机制
- 题目：**什么是 Java 的反射机制？它能在运行时做哪些事情？反射有哪些典型应用场景？反射的性能问题主要出在哪里，有哪些优化手段？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **反射机制**：程序在运行期动态获取类信息、构造对象、调用方法、访问字段的能力，核心入口是 `Class` 对象。
- **运行时能力**：`Class.forName` 加载类；`getDeclaredMethods/Fields/Constructors` 获取成员；`Constructor.newInstance` 创建实例；`Method.invoke` 调用方法；`Field.set/get` 访问字段（含 private，需 `setAccessible(true)`）。
- **典型应用场景**：Spring IoC（按类名实例化 Bean、依赖注入）、ORM/MyBatis（结果集映射到实体）、JDBC 驱动加载、JSON 序列化框架、动态代理、注解处理。
- **性能问题来源**：方法查找需遍历校验、参数类型匹配与装箱、`Method.invoke` 需做访问权限检查、JIT 难以内联（多态分发）、每次可能产生临时对象。
- **优化手段**：
  1. 缓存 `Class`/`Method`/`Field` 对象，避免重复查找。
  2. `setAccessible(true)` 跳过访问检查。
  3. 高频场景用 `MethodHandle` 或 `LambdaMetafactory` 生成调用器（接近直接调用性能）。
  4. 框架层用 ASM/CGLIB 生成字节码替代反射。

### java基础-020
- 难度：进阶 | 考点：序列化机制、Serializable
- 题目：**什么是 Java 对象的序列化与反序列化？`Serializable` 接口的作用是什么？`serialVersionUID` 有什么用？请说明 `transient` 关键字的作用，以及序列化可能带来的安全问题。**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **序列化/反序列化**：将对象转为字节序列（便于存储/网络传输），以及从字节序列恢复对象。
- **`Serializable` 作用**：标记接口（无方法），实现后表示该类可被 `ObjectOutputStream`/`ObjectInputStream` 序列化；未实现会抛 `NotSerializableException`。
- **`serialVersionUID`**：序列化版本号。反序列化时 JVM 会比对字节流中的 UID 与本地类 UID，不一致抛 `InvalidClassException`。显式声明可保证类兼容演变（增删字段不报错，缺失字段给默认值）；不声明则编译器按类结构自动生成，类一改就变，导致反序列化失败。
- **`transient`**：修饰字段表示该字段不参与默认序列化（如敏感密码、临时缓存），反序列化后为默认值（null/0）。
- **安全问题**：
  1. 反序列化会调用 `readObject` 等方法，攻击者可构造恶意字节流触发任意类的方法（反序列化漏洞，如 commons-collections 链），导致 RCE。
  2. 不要反序列化不可信来源的数据；用白名单过滤（`ObjectInputFilter`，JDK 9+）。
  3. 敏感字段应 `transient` 或加密；推荐用 JSON 等跨语言、可控格式替代 Java 原生序列化。

### java基础-021
- 难度：进阶 | 考点：深拷贝/浅拷贝
- 题目：**请解释 Java 中「深拷贝（Deep Copy）」和「浅拷贝（Shallow Copy）」的区别。如何实现深拷贝？请至少给出两种实现方式并对比其优缺点。**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **浅拷贝**：复制对象本身，但引用类型字段仍指向被拷贝对象的同一实例（共享内部对象）。`Object.clone()` 默认是浅拷贝。
- **深拷贝**：递归复制对象及其所有引用指向的对象，拷贝后两个对象图完全独立，互不影响。
- **实现方式一：手动 `clone()` 递归拷贝**
  - 实现 `Cloneable`，重写 `clone()`，对引用字段再调用其 `clone()`。
  - 优点：可控、性能较好；缺点：代码繁琐，多层嵌套易漏，引用类型需都支持 clone。
- **实现方式二：序列化/反序列化**
  - 对象实现 `Serializable`，先序列化为字节再反序列化出新对象。
  - 优点：通用、一行递归拷贝整个对象图；缺点：性能差（IO 开销）、所有相关类必须实现 Serializable。
- **其他方式**：第三方库（如 Jackson 序列化为 JSON 再读回，Gson，Apache Commons `SerializationUtils.clone()`）；或拷贝构造方法/工厂方法手动 new。
- 对比：手动 clone 性能好但维护成本高；序列化通用但慢且有安全/兼容限制。

### java基础-022
- 难度：进阶 | 考点：Stream API、Lambda、函数式编程
- 题目：**请说明 Java 8 引入的 `Stream API` 和 `Lambda` 表达式。`map`、`filter`、`reduce`、`collect` 等操作的区别是什么？什么是「短路操作」？请说明 Stream 的「懒加载（lazy）」特性是如何提升性能的。**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **Lambda**：简洁地表示函数式接口（仅一个抽象方法）的实例，语法 `(params) -> body`，配合 Stream 用于传递行为。
- **Stream API**：对集合的声明式、可链式、可并行数据处理流水线，分中间操作与终端操作。
- **操作区别**：
  - `map`：一对一映射转换元素（`Stream<R> map(Function<T,R>)`）。
  - `filter`：按条件过滤保留元素。
  - `reduce`：归约，将流聚合为一个值（如求和、求积）。
  - `collect`：终端操作，将流收集为集合/Map/字符串等（`Collectors.toList/groupingBy/joining`）。
- **中间操作 vs 终端操作**：`map/filter/sorted` 是中间操作（返回 Stream，不触发计算）；`collect/reduce/forEach/count` 是终端操作（触发整条流水线执行）。
- **短路操作**：不需要处理完所有元素即可得出结果的操作。如 `limit(n)`、`findFirst()`、`anyMatch()`--找到即停。
- **懒加载提升性能**：中间操作不会立即执行，只有遇到终端操作才一次性「融合」执行，且可结合短路避免全量遍历。例如 `list.stream().filter(x->x>0).findFirst()` 只需处理到第一个正数即停，不遍历整个列表。这种延迟计算 + 短路融合显著减少无谓计算。

### java基础-023
- 难度：进阶 | 考点：ThreadLocal、内存泄漏
- 题目：**请说明 `ThreadLocal` 的作用与实现原理。为什么 `ThreadLocal` 可能导致内存泄漏？应如何正确使用与清理？`InheritableThreadLocal` 与 `ThreadLocal` 有何区别？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **作用**：为每个线程提供变量的独立副本，实现线程间数据隔离，避免共享竞争（典型场景：用户上下文、数据库连接、SimpleDateFormat 线程安全）。
- **实现原理**：每个 `Thread` 持有一个 `ThreadLocalMap`，key 是 `ThreadLocal` 实例（弱引用），value 是该线程的副本值。`ThreadLocal.set/get` 实际操作的是当前线程的 `ThreadLocalMap`。
- **内存泄漏原因**：key 是弱引用，`ThreadLocal` 实例无强引用时 key 被回收变为 null；但 value 是强引用，且 `ThreadLocalMap` 的 Entry 生命周期与线程相同。线程长期存活（如线程池复用）时，key=null 的 Entry 的 value 无法回收，导致泄漏。
- **正确使用与清理**：用完务必在 `finally` 中调用 `remove()` 清除当前线程的副本；用 `try-finally` 包裹 `set/get`。
- **`InheritableThreadLocal` 区别**：子线程可以访问父线程创建时设置的值（父线程的 `InheritableThreadLocal` 值会在子线程 `Thread` 初始化时复制过来）。普通 `ThreadLocal` 子线程取不到父线程的值。
  - 局限：仅在线程「创建时」复制，线程池复用线程时不会再次复制，需用阿里 TransmittableThreadLocal 解决。

### java基础-024
- 难度：基础 | 考点：对象创建方式、反射、反序列化
- 题目：**Java 中创建对象有哪几种方式？请分别说明 `new`、反射、`clone()`、反序列化、`Unsafe.allocateInstance()` 等方式的区别，以及它们是否调用构造方法。**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **`new`**：最常用，在堆上分配并调用构造方法初始化。
- **反射 `Class.newInstance()` / `Constructor.newInstance()`**：运行时按类名创建实例，调用对应构造方法。`Class.newInstance()`（已废弃）只能调无参构造且需可访问；`Constructor.newInstance()` 更灵活，可调有参构造。
- **`clone()`**：实现 `Cloneable` 接口，调用 `Object.clone()` 复制对象，**不调用构造方法**，是内存拷贝。
- **反序列化**：通过 `ObjectInputStream` 从字节流恢复对象，**不调用构造方法**（但要求类实现 `Serializable`）。
- **`Unsafe.allocateInstance()`**：直接分配内存创建对象，**不调用构造方法**，也不执行初始化（字段为默认值），属于底层危险 API。
- 是否调用构造方法汇总：`new` 调用、反射调用、`clone` 不调用、反序列化不调用、`Unsafe` 不调用。

### java基础-025
- 难度：基础 | 考点：内部类
- 题目：**Java 中的内部类有哪几种（静态内部类、成员内部类、局部内部类、匿名内部类）？它们各自的特点与使用场景是什么？为什么匿名内部类只能访问 `final`（或 effectively final）的局部变量？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **静态内部类（static nested）**：用 `static` 修饰，不依赖外部类实例，只能访问外部类静态成员。使用最广（如 `HashMap.Node`、Builder 模式），可独立实例化。
- **成员内部类**：无 static，持有外部类实例引用，可访问外部类所有成员。实例化需先有外部实例 `outer.new Inner()`。
- **局部内部类**：定义在方法/代码块内，作用域限于方法，可访问方法内 final 变量。使用较少。
- **匿名内部类**：没有名字，直接 `new 父类/接口(){...}`，常用于回调、事件监听、`Comparator` 等。Java 8 后多用 Lambda 替代函数式接口的匿名内部类。
- **只能访问 final（effectively final）局部变量的原因**：局部内部类/匿名内部类的对象生命周期可能超出方法栈（如被异步持有），而局部变量随方法栈销毁。编译器实际是将用到的局部变量**拷贝**一份到内部类中，为保证拷贝与原变量始终一致，必须要求变量不可变（final）。effectively final 指虽未显式写 final 但从未被重新赋值，Java 8+ 视同 final。

### java基础-026
- 难度：进阶 | 考点：注解机制、元注解、自定义注解
- 题目：**请说明 Java 注解（Annotation）的工作机制。常见的元注解（`@Retention`、`@Target`、`@Inherited`、`@Documented`、`@Repeatable`）分别是什么含义？如何自定义一个注解并在运行时通过反射读取它？**
- 首次生成：2026-07-31 | 来源：question/2026-07-31-java基础-3.md

#### 参考答案
- **注解机制**：注解是附加在类/方法/字段上的元数据标记，本身不执行逻辑，需由编译器（如 `@Override`）、工具（如 Lint）或运行时反射/字节码增强（如 Spring、MyBatis）读取并处理。
- **元注解含义**：
  - `@Retention`：保留策略，`SOURCE`（仅源码，编译丢弃，如 `@Override`）、`CLASS`（保留到 class 文件，默认，运行时不加载）、`RUNTIME`（运行时可反射读取）。
  - `@Target`：可标注的目标，如 `TYPE/METHOD/FIELD/PARAMETER` 等。
  - `@Inherited`：子类是否继承父类的注解（仅对类有效）。
  - `@Documented`：是否出现在 Javadoc 中。
  - `@Repeatable`：是否可重复标注（需配合容器注解，Java 8+）。
- **自定义注解 + 反射读取示例**：
  ```java
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.METHOD)
  public @interface MyAnno { String value() default ""; }

  // 反射读取
  Method m = obj.getClass().getMethod("doIt");
  if (m.isAnnotationPresent(MyAnno.class)) {
      MyAnno a = m.getAnnotation(MyAnno.class);
      String v = a.value(); // 读取注解属性
  }
  ```
  - 只有 `@Retention(RUNTIME)` 的注解才能在运行时通过 `getAnnotation`/`getDeclaredAnnotation` 读取。
  - 典型应用：Spring `@Component`/`@RequestMapping`、JUnit `@Test`、自定义权限校验 `@RequiresLogin`。

## 关联

- （暂无已建立的相关话题；新话题建立后在此互加 `[[话题]]` 链接，只链接已存在的话题页）
