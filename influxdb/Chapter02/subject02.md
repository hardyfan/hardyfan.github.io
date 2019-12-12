# InfluxDB关键概念

## InfluxDB特点
 

- 可以设置metric的保存时间。
- 支持通过条件过滤以及正则表达式删除数据。
- 支持类似 sql 的语法。
- 可以设置数据在集群中的副本数。
- 支持定期采样数据，写入另外的measurement，方便分粒度存储数据。

## InfluxDB概念

### 数据格式 Line Protocol
在 InfluxDB 中，我们可以粗略的将要存入的一条数据看作一个虚拟的 key 和其对应的 value(field value)，格式如下：

cpu_usage,host=server01,region=us-west value=0.64 1434055562000000000

虚拟的 key 包括以下几个部分： database, retention policy, measurement, tag sets, field name, timestamp。 database 和 retention policy 在上面的数据中并没有体现，通常在插入数据时在 http 请求的相应字段中指定。

database: 数据库名，在 InfluxDB 中可以创建多个数据库，不同数据库中的数据文件是隔离存放的，存放在磁盘上的不同目录。

retention policy: 存储策略，用于设置数据保留的时间，每个数据库刚开始会自动创建一个默认的存储策略 autogen，数据保留时间为永久，之后用户可以自己设置，例如保留最近2小时的数据。插入和查询数据时如果不指定存储策略，则使用默认存储策略，且默认存储策略可以修改。InfluxDB 会定期清除过期的数据。

measurement: 测量指标名，例如 cpu_usage 表示 cpu 的使用率。

tag sets: tags 在 InfluxDB 中会按照字典序排序，不管是 tagk 还是 tagv，只要不一致就分别属于两个 key，例如 host=server01,region=us-west 和 host=server02,region=us-west 就是两个不同的 tag set。

field name: 例如上面数据中的 value 就是 fieldName，InfluxDB 中支持一条数据中插入多个 fieldName，这其实是一个语法上的优化，在实际的底层存储中，是当作多条数据来存储。

timestamp: 每一条数据都需要指定一个时间戳，在 TSM 存储引擎中会特殊对待，以为了优化后续的查询操作。

### Point

InfluxDB 中单条插入语句的数据结构，series + timestamp 可以用于区别一个 point，也就是说一个 point 可以有多个 field name 和 field value。

### Series
series 相当于是 InfluxDB 中一些数据的集合，在同一个 database 中，retention policy、measurement、tag sets 完全相同的数据同属于一个 series，同一个 series 的数据在物理上会按照时间顺序排列存储在一起。

series 的 key 为 measurement + 所有 tags 的序列化字符串，这个 key 在之后会经常用到。

代码中的结构如下：

	type Series struct {
		mu          sync.RWMutex
		Key         string              // series key
		Tags        map[string]string   // tags
		id          uint64              // id
		measurement *Measurement        // measurement
	}

### Shard
shard 在 InfluxDB 中是一个比较重要的概念，它和 retention policy 相关联。每一个存储策略下会存在许多 shard，每一个 shard 存储一个指定时间段内的数据，并且不重复，例如 7点-8点 的数据落入 shard0 中，8点-9点的数据则落入 shard1 中。每一个 shard 都对应一个底层的 tsm 存储引擎，有独立的 cache、wal、tsm file。

创建数据库时会自动创建一个默认存储策略，永久保存数据，对应的在此存储策略下的 shard 所保存的数据的时间段为 7 天，计算的函数如下：

	func shardGroupDuration(d time.Duration) time.Duration {
		if d >= 180*24*time.Hour || d == 0 { // 6 months or 0
			return 7 * 24 * time.Hour
		} else if d >= 2*24*time.Hour { // 2 days
			return 1 * 24 * time.Hour
		}
		return 1 * time.Hour
	}

如果创建一个新的 retention policy 设置数据的保留时间为 1 天，则单个 shard 所存储数据的时间间隔为 1 小时，超过1个小时的数据会被存放到下一个 shard 中。
