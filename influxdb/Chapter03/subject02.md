# 连续查询

连续查询主要用在将数据归档，以降低系统空间的占用率，主要是以降低精度为代价。

## 定义
InfluxDB的连续查询是在数据库中自动定时启动的一组语句，语句中必须包含 SELECT 关键词和 GROUP BY time() 关键词。

InfluxDB会将查询结果放在指定的数据表中。

## 目的
使用连续查询是最优的降低采样率的方式，连续查询和存储策略搭配使用将会大大降低InfluxDB的系统占用量。

而且使用连续查询后，数据会存放到指定的数据表中，这样就为以后统计不同精度的数据提供了方便。

## 操作
只有管理员用户可以操作连续查询。

1）新建连续查询

新建连续查询的语法如下所示：

	CREATE CONTINUOUS QUERY <cq_name> ON <database_name> 
	[RESAMPLE [EVERY <interval>] [FOR <interval>]] 
	BEGIN SELECT <function>(<stuff>)[,<function>(<stuff>)] INTO <different_measurement> 
	FROM <current_measurement> [WHERE <stuff>] GROUP BY time(<interval>)[,<stuff>] 
	END

查询部分被 CREATE CONTINUOUS QUERY [...] BEGIN 和 END 所包含，主要的逻辑代码也是在这一部分。

使用示例：

	> CREATE CONTINUOUS QUERY cq_30m ON telegraf BEGIN SELECT mean(used) INTO mem_used_30m FROM mem GROUP BY time(30m) END
	> SHOW CONTINUOUS QUERIES
	name: telegraf
	--------------
	name    query
	cq_30m    CREATE CONTINUOUS QUERY cq_30m ON telegraf BEGIN 
	SELECT mean(used) INTO telegraf."default".mem_used_30m FROM telegraf."default".mem 
	GROUP BY time(30m) END


	name: _internal
	---------------
	name    query

示例在telegraf库中新建了一个名为 cq_30m 的连续查询，每三十分钟取一个used字段的平均值，加入 mem_used_30m 表中。使用的数据保留策略都是 default。

2）显示所有已存在的连续查询

查询所有连续查询可以使用如下语句：

	> SHOW CONTINUOUS QUERIES
	name: telegraf
	--------------
	name    query
	cq_30m    CREATE CONTINUOUS QUERY cq_30m ON telegraf 
	BEGIN SELECT mean(used) INTO telegraf."default".mem_used_30m FROM telegraf."default".mem 
	GROUP BY time(30m) END


	name: _internal
	---------------
	name    query

可以看到其连续查询的名称以及 语句等信息。

3）删除Continuous Queries

删除连续查询的语句如下：

	DROP CONTINUOUS QUERY <cq_name> ON <database_name>

## 其他说明

在InfluxDB中，将连续查询与数据存储策略一起使用会达到最好的效果。

比如，将精度高的表的存储策略定为一个周，然后将精度底的表存储策略定的时间久一点，这要就可以实现高低搭配，以满足不同的工作需要。
