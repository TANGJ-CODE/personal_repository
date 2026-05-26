# Java 后端开发面试题库

## 1. 基础知识

### Java 基础

**Q1: HashMap 的底层实现原理**
- **回答思路**: 先说 JDK 7 和 8 的区别，再到 put 流程，最后扩容机制
- **答案**:
  - JDK 7: 数组 + 链表，头插法
  - JDK 8: 数组 + 链表 + 红黑树，尾插法，链表长度>=8转红黑树
  - put 流程: 计算hash -> 定位桶 -> 无值直接插入 -> 有值遍历链表/树 -> key相同则覆盖 -> 新增size+1 -> 检查扩容
  - 扩容: threshold = capacity * loadFactor，扩容为2倍

**Q2: ConcurrentHashMap 实现原理**
- **回答思路**: JDK 7 和 8 区别，锁粒度变化
- **答案**:
  - JDK 7: Segment分段锁，继承ReentrantLock
  - JDK 8: Node数组 + CAS + synchronized，锁粒度到Node节点
  - 读写分离: 读操作无锁，写操作CAS+synchronized

**Q3: 线程池参数及拒绝策略**
- **回答思路**: 7个核心参数 + 4种拒绝策略 + 线程池状态
- **答案**:
  - 参数: corePoolSize、maximumPoolSize、keepAliveTime、unit、workQueue、threadFactory、handler
  - 拒绝策略: AbortException(抛异常)、CallerRunsPolicy(调用者执行)、DiscardOldestPolicy(丢弃最老)、DiscardPolicy(丢弃)
  - 状态: RUNNING、SHUTDOWN、STOP、TIDYING、TERMINATED

## 2. JVM

**Q4: JVM 内存模型**
- **回答思路**: 按区域划分，说明各区域作用
- **答案**:
  - 堆: 存放对象实例，分为新生代(Eden+S0+S1)和老年代
  - 方法区: 类信息、常量、静态变量(JDK 8后用元空间)
  - 栈: 方法调用，局部变量
  - 程序计数器: 当前执行字节码行号
  - 本地方法栈: Native方法调用

**Q5: 垃圾收集算法和收集器**
- **回答思路**: 算法 + 常用收集器对比
- **答案**:
  - 算法: 标记-清除(碎片)、标记-整理(无碎片)、复制(新生代)、分代收集
  - 收集器:
    - Serial: 单线程
    - Parallel: 多线程
    - CMS: 低停顿，三色标记
    - G1: 区域化，可预测停顿
    - ZGC/Shenandoah: 低延迟

**Q6: 类加载过程和双亲委派模型**
- **回答思路**: 加载->验证->准备->解析->初始化 + 双亲委派的好处
- **答案**:
  - 过程: 加载(二进制流)、验证(格式/语义)、准备(默认值)、解析(符号引用)、初始化(赋值)
  - 双亲委派: AppClassLoader -> ExtClassLoader -> BootstrapClassLoader
  - 好处: 避免重复加载、保证核心类安全

## 3. 多线程并发

**Q7: synchronized 和 ReentrantLock 区别**
- **回答思路**: 实现方式、特性对比、使用场景
- **答案**:
  - synchronized: JVM层面，自动释放，不可中断
  - ReentrantLock: API层面，手动释放，可中断，可公平锁，可condition
  - 性能: JDK 6优化后synchronized接近ReentrantLock

**Q8: volatile 关键字作用**
- **回答思路**: 可见性、有序性、原子性
- **答案**:
  - 保证可见性(JMM主内存刷回工作内存)
  - 禁止指令重排(内存屏障)
  - 不保证原子性(复合操作仍需锁)
  - 典型应用: 单例DCL、状态标志

**Q9: CAS 和 AQS**
- **回答思路**: 原理 + 局限性 + AQS核心
- **答案**:
  - CAS: Compare-And-Swap，乐观锁，CPU指令级别，解决ABA用版本号
  - AQS: AbstractQueuedSynchronizer，state变量+CLH队列，模板方法模式
  - 应用: ReentrantLock、CountDownLatch、Semaphore

## 4. Spring 框架

**Q10: IOC 和 AOP 原理**
- **回答思路**: 概念 -> 实现方式 -> 应用场景
- **答案**:
  - IOC: 控制反转，DI依赖注入，BeanFactory/ApplicationContext
  - 实现方式: 反射 + 工厂模式
  - AOP: 面向切面编程，动态代理(JDK/CGLIB)
  - 应用: 事务、日志、权限

**Q11: Spring Bean 生命周期**
- **回答思路**: 实例化->属性填充->初始化->销毁
- **答案**:
  - 实例化 -> 属性填充 -> BeanNameAware -> BeanFactoryAware -> ApplicationContextAware
  - BeanPostProcessor.postProcessBeforeInitialization -> @PostConstruct/init-method
  - BeanPostProcessor.postProcessAfterInitialization -> 使用中
  - @PreDestroy/destroy-method -> 销毁

**Q12: Spring 事务传播机制**
- **回答思路**: 7种类型 + 常用场景
- **答案**:
  - REQUIRED(默认): 有则加入无则新建
  - REQUIRES_NEW: 总是新建
  - NESTED: 嵌套事务，部分回滚
  - SUPPORTS/MANDATORY/NOT_SUPPORTED/NEVER

**Q13: Spring 循环依赖解决**
- **回答思路**: 三级缓存机制
- **答案**:
  - 一级缓存: singletonObjects(完整Bean)
  - 二级缓存: earlySingletonObjects(早期Bean)
  - 三级缓存: singletonFactories(Bean工厂，解决AOP代理)
  - 构造器注入无法解决

## 5. Spring Boot / Spring Cloud

**Q14: Spring Boot 自动装配原理**
- **回答思路**: @EnableAutoConfiguration + SPI机制
- **答案**:
  - @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan
  - EnableAutoConfiguration导入AutoConfigurationImportSelector
  - 读取META-INF/spring.factories(JDK 2.4)/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
  - 条件注解过滤(@ConditionalOnClass等)

**Q15: 微服务组件**
- **回答思路**: 各组件作用 + 对比
- **答案**:
  - 注册中心: Eureka(AP)、Nacos(CP+AP)、Consul
  - 配置中心: Config、Nacos Config
  - 熔断降级: Hystrix(停更)、Resilience4j、Sentinel
  - 网关: Zuul(1.x阻塞)、Spring Cloud Gateway(WebFlux非阻塞)
  - 链路追踪: Sleuth + Zipkin、SkyWalking

## 6. 数据库

**Q16: MySQL 索引原理**
- **回答思路**: B+树结构 + 聚簇/非聚簇 + 覆盖索引
- **答案**:
  - B+树: 非叶子节点存索引，叶子节点存数据，叶子节点双向链表
  - 聚簇索引: 主键索引，数据和索引在一起
  - 非聚簇索引: 辅助索引，叶子节点存主键值
  - 覆盖索引: 查询字段都在索引中，避免回表
  - 最左前缀原则: 联合索引按顺序匹配

**Q17: MySQL 事务隔离级别**
- **回答思路**: 4级别 + 对应问题
- **答案**:
  - 读未提交: 脏读、不可重复读、幻读
  - 读已提交(默认): 不可重复读、幻读
  - 可重复读(InnoDB默认): 幻读(MVCC解决)
  - 串行化: 无问题，性能最差

**Q18: MySQL 锁机制**
- **回答思路**: 锁类型 + 锁粒度 + MVCC
- **答案**:
  - 按类型: 共享锁(S)、排他锁(X)、意向锁
  - 按粒度: 表锁、行锁、页锁
  - 行锁实现: Record Lock、Gap Lock、Next-Key Lock
  - MVCC: Read View + Undo Log，实现读写不冲突

**Q19: Redis 数据结构和应用场景**
- **回答思路**: 5种基础 + 3种高级 + 场景
- **答案**:
  - String: 缓存、计数器、分布式锁
  - Hash: 用户信息、购物车
  - List: 消息队列、最新列表
  - Set: 标签、共同关注
  - ZSet: 排行榜、延时队列
  - Bitmap: 签到、在线用户
  - HyperLogLog: UV统计
  - Geo: 附近的人

**Q20: Redis 持久化**
- **回答思路**: RDB + AOF + 混合
- **答案**:
  - RDB: fork子进程，快照保存，恢复快，可能丢数据
  - AOF: 日志追加，安全，文件大，恢复慢
  - 混合: 4.0+，RDB做基础，AOF记录增量

**Q21: Redis 高可用**
- **回答思路**: 主从 + 哨兵 + 集群
- **答案**:
  - 主从复制: 读写分离，数据备份
  - 哨兵: 监控、通知、自动故障转移
  - 集群: 16384个槽，分片存储，支持扩容

## 7. 分布式

**Q22: 分布式事务**
- **回答思路**: 2PC/3PC + TCC + 最终一致性
- **答案**:
  - 2PC: 两阶段提交，存在单点、阻塞问题
  - 3PC: 增加预准备阶段
  - TCC: Try-Confirm-Cancel，补偿机制
  - SAGA: 长事务拆分，正向+逆向操作
  - 本地消息表: 保证最终一致性
  - Seata: AT/TCC/SAGA/XA模式

**Q23: 分布式锁**
- **回答思路**: Redis/Zookeeper/数据库 + 注意点
- **答案**:
  - Redis: SET NX EX，看门狗续期，RedLock算法
  - Zookeeper: 临时顺序节点，Watch机制
  - 数据库: 唯一索引 + 乐观锁
  - 注意: 锁续期、锁释放(只能自己释放)、锁超时

**Q24: 分布式ID生成**
- **回答思路**: 雪花算法 + 数据库自增 + UUID
- **答案**:
  - 雪花算法: 时间戳+机器ID+序列号，19位数字
  - 数据库自增: 分库分表不友好
  - UUID: 无序，性能差
  - Redis: INCR，需要持久化
  - 号段模式: 缓存一批ID

## 8. 消息队列

**Q25: 消息队列的作用**
- **回答思路**: 解耦、异步、削峰
- **答案**:
  - 解耦: 生产者消费者不直接依赖
  - 异步: 非核心流程异步处理
  - 削峰: 缓冲突发流量
  - 问题: 消息丢失、重复消费、顺序性

**Q26: 如何保证消息不丢失**
- **回答思路**: 生产者、Broker、消费者三端
- **答案**:
  - 生产者: 确认机制、事务消息
  - Broker: 同步刷盘、副本同步确认
  - 消费者: 手动提交offset
  - 死信队列: 处理失败消息

**Q27: 如何保证消息顺序**
- **回答思路**: 单分区 + 消费端保序
- **答案**:
  - 生产端: 同一分区，有序发送
  - 消费端: 单线程消费，或内存队列+多线程

**Q28: 消息幂等性**
- **回答思路**: 去重表 + 状态机 + 唯一ID
- **答案**:
  - 唯一ID: Redis Set去重
  - 去重表: 数据库唯一约束
  - 状态机: 幂等更新状态

## 9. 设计模式

**Q29: 单例模式**
- **回答思路**: 饿汉/懒汉 + DCL + 枚举
- **答案**:
  - 饿汉: 类加载初始化，线程安全
  - 懒汉: 延迟加载，DCL双重检查
  - 静态内部类: 利用类加载机制
  - 枚举: 最佳实践，防反射攻击

**Q30: 工厂模式**
- **回答思路**: 简单工厂 + 工厂方法 + 抽象工厂
- **答案**:
  - 简单工厂: 一个工厂类，switch-case创建
  - 工厂方法: 每个产品一个工厂
  - 抽象工厂: 产品族，多个工厂

**Q31: 代理模式**
- **回答思路**: 静态代理 + JDK动态代理 + CGLIB
- **答案**:
  - 静态代理: 编译时确定
  - JDK动态代理: 接口代理，反射
  - CGLIB: 子类代理，字节码生成

## 10. 算法/数据结构

**Q32: 常用排序算法**
- **回答思路**: 时间复杂度 + 稳定性 + 场景
- **答案**:
  - 快速排序: O(nlogn)，不稳定，常用
  - 归并排序: O(nlogn)，稳定，外部排序
  - 堆排序: O(nlogn)，不稳定，Top K
  - 插入排序: O(n^2)，稳定，小数据量

**Q33: 常用数据结构**
- **回答思路**: 特点 + 时间复杂度 + 场景
- **答案**:
  - 数组: 随机访问O(1)，插入O(n)
  - 链表: 插入O(1)，访问O(n)
  - 栈: LIFO，函数调用
  - 队列: FIFO，消息队列
  - 树: 层次关系，索引
  - 图: 关系网络，最短路径

## 11. 网络编程

**Q34: TCP 三次握手和四次挥手**
- **回答思路**: 流程 + 每次目的
- **答案**:
  - 三次握手: SYN->SYN+ACK->ACK，建立可靠连接
  - 四次挥手: FIN->ACK->FIN->ACK，保证数据传输完毕

**Q35: HTTP 状态码**
- **回答思路**: 分类 + 常用
- **答案**:
  - 2xx: 成功(200 OK, 201 Created)
  - 3xx: 重定向(301永久, 302临时, 304缓存)
  - 4xx: 客户端错误(400参数, 401未认证, 403无权限, 404不存在)
  - 5xx: 服务端错误(500内部错误, 502网关错误, 503服务不可用)

**Q36: HTTP 和 HTTPS 区别**
- **回答思路**: 安全性 + 性能 + 证书
- **答案**:
  - HTTPS = HTTP + SSL/TLS，加密传输
  - HTTPS 需要CA证书，有成本
  - HTTPS 握手延迟，性能略差
  - 端口: HTTP 80, HTTPS 443

## 12. 项目经验

**Q37: 如何做技术选型**
- **回答思路**: 业务需求 + 团队能力 + 生态成熟度
- **答案**:
  - 业务匹配度: 功能是否满足
  - 性能要求: 并发、响应时间
  - 团队熟悉度: 学习成本、维护成本
  - 社区活跃度: 问题解决、版本更新
  - 成本: 开发、部署、运维

**Q38: 如何排查性能问题**
- **回答思路**: 指标 -> 定位 -> 优化
- **答案**:
  - 指标: CPU、内存、IO、网络、线程
  - 工具: JProfiler、Arthas、Prometheus
  - 定位: 慢查询、锁竞争、GC频繁
  - 优化: 索引、缓存、异步、批量

**Q39: 如何做代码Review**
- **回答思路**: 关注点 + 工具 + 流程
- **答案**:
  - 代码规范: 命名、注释、格式
  - 逻辑正确: 边界条件、异常处理
  - 性能: 循环、数据库查询、锁
  - 安全: SQL注入、XSS、权限
  - 工具: SonarQube、Git PR Review

**Q40: 如何做系统设计**
- **回答思路**: 需求分析 -> 架构设计 -> 详细设计
- **答案**:
  - 需求分析: 功能、非功能(性能、可用性、扩展性)
  - 架构设计: 分层、微服务、技术选型
  - 数据设计: 表结构、索引、缓存策略
  - 接口设计: RESTful、参数校验、异常处理
  - 部署设计: 容器化、CI/CD、监控

---

## 备考建议

1. **理解原理**: 不要死记硬背，理解背后的原理和设计思想
2. **结合项目**: 用自己项目中的实际例子来回答
3. **扩展思路**: 面试官可能会追问，要能扩展相关知识
4. **手写代码**: 准备一些常见算法的手写实现
5. **看源码**: 对自己熟悉的技术，建议看过部分源码