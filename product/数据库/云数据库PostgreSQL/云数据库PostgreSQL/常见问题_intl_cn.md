### 为什么没有插入数据，但已用存储空间会增加？ 
由于 PostgreSQL 的 MVCC 机制：1）被 DELETE 的行并不会直接物理删除；2）Update 的行是通过插入新行实现的，过期数据也不会直接物理删除。因此，即使没有插入数据也会存在存储数据量增加的情况。

 当前云数据库已默认开启“autovacuum”配置参数，内核会自动回收过期数据，因此已用存储空间会在系统回收过期数据后自动释放出来。用户也可以手动执行“VACUUM”命令来回收过期数据（执行该命令后存储空间统计并不会立即下降，而是会把这些过期数据回收并标记为可重复利用）。如果想完全清理数据，考虑使用带参数的"VACUUM FULL"命令（该命令会锁表，强烈建议只在维护期间使用）。 
 VACUUM命令参考 [postgresql 官方文档](https://www.postgresql.org/docs/current/static/sql-vacuum.html )。

 ### 为什么 CPU 利用率会超过 100%？
 PostgreSQL 默认使用闲时超用的策略，即允许您的业务抢占一部分额外的空闲的 CPU 资源；因此，当您的实例超过默认给您分配的 CPU 核数时，您的 CPU 利用率监控视图会显示超过 100%，这个是正常的。

 若您的 CPU 负载长期高于 60%，则建议您尽快升级数据库。


 ### 为什么磁盘占用空间大于实际数据量？

 更新导致 xlog 日志剧增，系统来不及归档和删除，占用了磁盘空间。或者查询操作含有大数据量的排序、连接等操作，处理过程中产生临时表并溢出到磁盘，短时间内造成大量空间占用。


 ### 如何开启或使用插件

 腾讯云 postgresql 已支持大部分常用插件，可直接使用。而部分插件开启需要超级管理员权限，可到腾讯云控制台开启。或联系腾讯工作人员说明实例ID和插件名称即可开启。
