# 云数据库 RDS

云数据库RDS（Relational Database Service，简称RDS）是一种基于云计算平台的稳定可靠、弹性伸缩、便捷管理的在线云数据库服务。云数据库RDS支持以下引擎：

- MySQL
- PostgreSQL
- SQL Server
- MariaDB



### 一、概念

---

#### 实例

云数据库RDS的最小管理单元是实例，一个实例代表了一个独立运行的数据库。用户可以在云数据库RDS系统中自助创建及管理各种数据库引擎的实例。

#### 实例类型

- 单机实例
- 主备实例
- 集群版实例

| 实例类型   | **简介**                                                     | **使用说明**                                                 | **适用场景**                                                 |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 单机实例   | 采用单个数据库节点部署架构。与主流的主备实例相比，它只包含一个节点，但具有高性价比。 | 单机版出现故障后，无法保障及时恢复。                         | 个人学习。微型网站。中小企业的开发测试环境。                 |
| 主备实例   | 采用一主一备的经典高可用架构，支持跨AZ高可用，选择主可用区和备可用区不在同一个可用区（AZ）。主实例和备实例共用一个IP地址。 | 备机提高了实例的可靠性，创建主机的过程中，会同步创建备机，备机创建成功后，用户不可见。当主节点故障后，会发生主备切换，期间数据库客户端会发生短暂中断。若存在复制延时，主备切换时间会长一点，数据库客户端需要支持重新连接。 | 大中型企业的生产数据库。覆盖互联网、物联网、零售电商、物流、游戏等行业的应用。 |
| 集群版实例 | 采用微软AlwaysOn高可用架构，支持1主1备5只读集群模式，拥有更高可用性，可靠性，可拓展能力。 | 仅限RDS for SQL Server使用。                                 | 金融行业。互联网行业。酒店行业。在线教育。                   |



### 二、平台功能使用

> 这里以阿里云数据库RDS MySQL举例

#### **1. Top SQL：** 提供数据库中执行频率最高的SQL查询，帮助用户识别和优化性能瓶颈。

阿里提供性能洞察（新版）能力**基于MySQL的performance_schema能力**，汇聚SQL信息，帮助您评估目标数据库负载情况，找到性能问题的源头。

+ 条件-前提条件：

  - 不支持RDS MySQL基础系列。

  - 不支持RDS MySQL 5.5。


  - 数据库实例内存需要大于等于8 GB。

  - 数据库实例需要开启performance_schema

+ 查询区域：

  + 可查看最近一个月内任意七天的数据所有SQL的**资源消耗**、**执行次数**、**扫描行数**和**执行耗时**，并且支持对比查看对不同时段中目标SQL的详细指标。

**慢查询日志slow_log**
优点：MySQL自带，记录的慢SQL语句完整；
缺点：需要登录mysql服务器；如果slow_log文件太大，还需要利用其他工具分析日志，比如mysqldumpslow；

**performance_schema**

> 启用Performance Schema功能，需要重启数据库实例，建议您在业务低峰期操作，并确保应用程序具有重连机制。
>
> 开启Performance_schema会对数据库实例的资源会产生一定的消耗，详情请参见[Performance Schema压力测试报告](https://help.aliyun.com/zh/das/user-guide/stress-testing-report-of-performance-schema)。

授权语句

```
grant select on performance_schema.* to 'your_account_name'@'%';  
```

获取performanece_schema参数(终端上才显示)

```
show global variables like'%performance_schema';
```

查看性能模式实现了几个提供配置信息的系统变量(终端显示)

```
SHOW VARIABLES LIKE 'perf%';
```

常用语句

```
****查询平均响应时间最多****
SELECT AVG_TIMER_WAIT/1000000000000 as AVG_TIMER_WAIT_S,essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY AVG_TIMER_WAIT DESC limit 100;

****查询行读最多****
SELECT SUM_ROWS_EXAMINED/COUNT_STAR as AVG_ROWS_EXAMINED,essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY SUM_ROWS_EXAMINED/COUNT_STAR DESC limit 100;

**** 查询执行次数最多 ****
SELECT *
FROM performance_schema.events_statements_summary_by_digest
ORDER BY COUNT_STAR  DESC limit 100;

**** 查询排序次数最多 ****
	SELECT SUM_SORT_ROWS/COUNT_STAR as AVG_SORT_ROWS,essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY SUM_SORT_ROWS/COUNT_STAR DESC limit 100;

**** 查询返回结果集最多 ****
SELECT SUM_ROWS_SENT/COUNT_STAR as AVG_ROWS_SENT, essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY SUM_ROWS_SENT/COUNT_STAR  DESC limit 100;

**** 查询是否无索引 ****
SELECT SUM_NO_INDEX_USED/COUNT_STAR as AVG_NO_INDEX_USED, essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY SUM_NO_INDEX_USED/COUNT_STAR  DESC limit 100;

**** 查询锁定时间最多 ****
SELECT SUM_LOCK_TIME/COUNT_STAR/1000000000000 as AVG_LOCK_TIME, essbd.*
FROM performance_schema.events_statements_summary_by_digest essbd
ORDER BY SUM_LOCK_TIME/COUNT_STAR  DESC limit 100;

**** 查询IO最多 ****
SELECT AVG_TIMER_READ/1000000000000 as AVG_TIMER_READ_S,
AVG_TIMER_FETCH /1000000000000 as AVG_TIMER_FETCH_S,tiwsbtt.*
FROM performance_schema.table_io_waits_summary_by_table tiwsbtt
order by AVG_TIMER_READ desc limit 100;
```

> 如果服务端是用的druid连接池，那么可以配置druid monitor来进行监控。
>
> 优点：从应用层直接进行监控，还可以看到连接池的使用情况；
> 缺点：需要额外配置druid monitor，如果是微服务框架，貌似每个服务都需要独立去查看监控数据；



#### **2. SQL 限流：** 实施SQL查询的限流措施，防止某些查询对数据库产生不良影响。

>阿里SQL限流的实现方法
>
>| **参数**              | **说明**                                                     | **支持的数据库**                      |
>| --------------------- | ------------------------------------------------------------ | ------------------------------------- |
>| **通过关键词限流**    | 根据SQL语句中的关键词进行匹配从而限流。                      | RDS MySQLPolarDB MySQL版PolarDB-X 2.0 |
>| **通过SQL模板ID限流** | 对SQL模板ID进行匹配从而限流，可从SQL日志、会话、EXPLAIN执行结果中获得SQL模板ID。SQL模板ID是长度为8的16进制字符串。 | PolarDB-X 2.0                         |
>| **通过执行耗时限流**  | 根据SQL执行时间进行限流。创建了该类限流模式的规则后，当指定类型SQL的执行时间超过设定的阈值时，该类型SQL所属的模板ID会被自动加入到限流规则中，SQL会按照规则指定的并发度执行。您可以同时设置限流SQL模板ID数量上限，达到上限后，便不再自动对其他SQL创建限流规则。 | PolarDB-X 2.0                         |

下面是针对MySQL的

条件-实例为如下版本：

- RDS MySQL 8.0 高可用系列
- RDS MySQL 5.7 高可用系列
- RDS MySQL 5.6 高可用系列

功能限制-自动SQL限流的限流模式是**通过关键词限流**：

- 支持的SQL类型有**SELECT**、**UPDATE**、**DELETE**和**INSERT**。
- 不支持限流**INSERT...SELECT...**类型的SQL语句。
- 仅RDS MySQL 8.0和PolarDB MySQL版 5.7、8.0支持限流**INSERT**类型的SQL语句。

**3. 会话管理：**数据库建立的连接所相关的信息 ，平台提供对数据库会话的监控和管理，帮助识别和处理长时间运行的会话等问题。

条件-实例为如下版本：

- RDS MySQL 8.0 高可用系列或集群系列
- RDS MySQL 5.7 高可用系列或集群系列
- RDS MySQL 5.6 高可用系列
- RDS MySQL 5.5 高可用系列

1. **锁诊断：** 监控和诊断数据库中的锁定情况，帮助解决由于锁定而引起的性能问题。

   条件-实例为如下版本：

   - RDS MySQL 8.0 高可用版或集群版
   - RDS MySQL 5.7 高可用版或集群版
   - RDS MySQL 5.6 高可用版
   - RDS MySQL 5.5 高可用版

2. **空间容量诊断：** 监控数据库存储空间的使用情况，提供容量规划建议，防止空间不足导致的问题。

   条件-实例为如下版本：

   - RDS MySQL 8.0 高可用版或集群版
   - RDS MySQL 5.7 高可用版或集群版
   - RDS MySQL 5.6 高可用版
   - RDS MySQL 5.5 高可用版

   注意：

   + 空间分析功能最多分析20000张表。

3. **性能洞察：** 提供深入的性能分析，帮助用户理解数据库的性能瓶颈，并提供优化建议。

   条件-前提条件：

   - 不支持RDS MySQL基础系列。
   - 不支持RDS MySQL 5.5。

   - 数据库实例内存需要大于等于8 GB。
   - 数据库实例需要开启performance_schema

   功能实现：

   - 快速定位所有SQL的**资源消耗**、**执行次数**、**扫描行数**和**执行耗时**。

   - 支持查看目标数据库不同指标的性能趋势和目标SQL的详细信息。

   - 支持通过SQL ID和关键词查询目标SQL的详细信息。

   - 支持查看目标SQL的**资源消耗**、**执行次数**、**扫描行数**和**执行耗时**趋势，以及SQL样本信息。

   - 支持对目标SQL进行限流和优化。

   - 支持对比查看不同时间段中所有SQL的**资源消耗**、**执行次数**、**扫描行数**和**执行耗时**，并且支持对比查看对不同时段中目标SQL的详细指标。

   - 支持查看无流量表和无流量索引。DAS会在每天凌晨采集表和索引的使用数据，判断表和索引是否还在使用中，并统计表和索引无流量的天数。

     >  无流量天数的最小设置为7天。即在开启无流量表和索引识别后，需要至少7天才能查看数据库中的无流量表和无流量索引数据。

   查询区域：

   + 可查看最近一个月内任意七天的数据所有SQL的**资源消耗**、**执行次数**、**扫描行数**和**执行耗时**，并且支持对比查看对不同时段中目标SQL的详细指标。

4. **索引优化：** 分析和优化数据库中的索引，以提高查询性能和降低查询时间。

   条件-实例为如下版本：

   - RDS MySQL 8.0 高可用系列或集群系列
   - RDS MySQL 5.7 高可用系列或集群系列
   - RDS MySQL 5.6 高可用系列
   - RDS MySQL 5.5 高可用系列

   > 当慢SQL优化完成后，可能会出现全局性能变差的问题，为了提升**SQL自动优化**的准确率，建议开启[SQL洞察和审计](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-sql-explorer-and-audit-feature-on-an-apsaradb-rds-for-mysql-instance#task-2078931)。

   功能介绍：

   + DAS在自动创建索引时，使用MySQL内核原生支持的Online DDL，因此不会像普通索引添加那样引发锁表



### 三、各个服务商对RDS高级运维服务功能支持对比

#### 阿里云数据库RDS

|                | Top SQL                                                      | SQL 限流                                                     | 会话管理                                                     | 锁诊断                                                       | 空间容量诊断                                                 | 性能洞察                                                     | 索引优化                                                     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **MySQL**      | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-new-performance-insight-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i30) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-automatic-sql-throttling-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i75) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-session-management-feature-for-an-apsaradb-rds-for-postgresql-instance?spm=a2c4g.11186623.0.i76) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-deadlock-analysis-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i79) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-storage-analysis-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i134) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-new-performance-insight-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i142) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/use-the-automatic-sql-optimization-feature-for-an-apsaradb-rds-for-mysql-instance?spm=a2c4g.11186623.0.i202) |
| **PostgreSQL** | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/locate-sql-statements-with-the-highest-resource-consumption?spm=a2c4g.11174283.0.i1) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/use-the-rds-ccl-extension-to-perform-sql-throttling-on-an-apsaradb-rds-for-postgresql-instance?spm=a2c4g.11186623.0.i82) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/use-the-session-management-feature-for-an-apsaradb-rds-for-postgresql-instance-1?spm=a2c4g.11186623.0.i71) |                                                              | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/use-the-storage-analysis-feature-for-an-apsaradb-rds-for-postgresql-instance?spm=a2c4g.11186623.0.i129) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/use-the-performance-insight-feature-for-an-apsaradb-rds-for-postgresql-instance?spm=a2c4g.11186623.0.i154) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-postgresql/using-the-index-adviser-extension-on-an-apsaradb-rds-for-postgresql-instance?spm=a2c4g.11186623.0.i208) |
| **SQL Server** | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-sql-server/view-the-sql-statement-statistics-of-an-apsaradb-rds-for-sql-server-instance?spm=a2c4g.11174283.0.i3) |                                                              |                                                              | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-sql-server/view-the-deadlock-statistics-of-an-apsaradb-rds-for-sql-server-instance?spm=a2c4g.11186623.0.i99) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-sql-server/view-the-storage-information-of-an-apsaradb-rds-for-sql-server-instance?spm=a2c4g.11186623.0.i124) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-sql-server/use-the-performance-insight-feature-for-an-apsaradb-rds-for-sql-server-instance?spm=a2c4g.11186623.0.i159) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-sql-server/view-the-index-usage-statistics-of-an-apsaradb-rds-for-sql-server-instance?spm=a2c4g.11186623.0.0.53161009z2MYxb) |
| **MariaDB**    |                                                              |                                                              |                                                              |                                                              | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mariadb/api-rds-2014-08-15-describeresourceusage-mariadb?spm=a2c4g.11186623.0.i118) | [可以](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mariadb/view-the-resource-metrics-and-engine-metrics-of-an-apsaradb-rds-for-mariadb-instance?spm=a2c4g.11186623.0.0.5c4f2c2e9MAotS)[ json](https://help.aliyun.com/zh/rds/apsaradb-rds-for-mariadb/api-rds-2014-08-15-describedbinstanceperformance-mariadb?spm=a2c4g.11186623.0.i188) |                                                              |



#### 华为云数据库RDS

|                | Top SQL                                                      | SQL 限流                                                     | 会话管理                                                     | 锁诊断                                                       | 空间容量诊断                                                 | 性能洞察                                                     | 索引优化 |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| **MySQL**      | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_0031.html) | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_0033.html) | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_0026.html) | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_0029.html) | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_00007.html) | [可以](https://support.huaweicloud.com/usermanual-rds/rds_08_00001.html) |          |
| **PostgreSQL** |                                                              |                                                              |                                                              |                                                              |                                                              | [可以](https://support.huaweicloud.com/bestpractice-rds/rds_pg_0027.html) |          |
| **SQL Server** | [可以](https://support.huaweicloud.com/usermanual-rds/rds_sqlserver_08_0031.html) |                                                              | [可以](https://support.huaweicloud.com/usermanual-rds/rds_sqlserver_08_0038.html) |                                                              | [可以](https://support.huaweicloud.com/usermanual-rds/rds_sqlserver_08_0024.html) |                                                              |          |
| **MariaDB**    |                                                              | [可以](https://support.huaweicloud.com/usermanual-rds/maria_03_0118.html) | [可以](https://support.huaweicloud.com/usermanual-rds/maria_03_0114.html) |                                                              | [可以](https://support.huaweicloud.com/usermanual-rds/maria_03_0116.html) | [可以](https://support.huaweicloud.com/usermanual-rds/maria_03_0115.html) |          |







### 四、MySQL演示

是否支持在线查看某个时间段的SQL执行信息 (执行时间次数，平均时间，SQL文本信息）

