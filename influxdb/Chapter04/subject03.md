# 变换类函数

## DERIVATIVE()函数
作用：返回一个字段在一个series中的变化率。

InfluxDB会计算按照时间进行排序的字段值之间的差异，并将这些结果转化为单位变化率。其中，单位可以指定，默认为1s。

语法：

	SELECT DERIVATIVE(<field_key>, [<unit>]) FROM <measurement_name> [WHERE <stuff>]
其中，unit取值可以为以下几种：

u --microseconds
s --seconds
m --minutes
h --hours
d --days
w --weeks
DERIVATIVE()函数还可以在GROUP BY time()的条件下与聚合函数嵌套使用，格式如下：

	SELECT DERIVATIVE(AGGREGATION_FUNCTION(<field_key>),[<unit>]) FROM <measurement_name> WHERE <stuff> GROUP BY time(<aggregation_interval>)
示例：

假设location = santa_monica 条件下数据有以下几条：

	name: h2o_feet
	--------------
	time                           water_level
	2015-08-18T00:00:00Z     2.064
	2015-08-18T00:06:00Z     2.116
	2015-08-18T00:12:00Z     2.028
	2015-08-18T00:18:00Z     2.126
	2015-08-18T00:24:00Z     2.041
	2015-08-18T00:30:00Z     2.051
计算每一秒的变化率：

	> SELECT DERIVATIVE(water_level) FROM h2o_feet WHERE location = 'santa_monica' LIMIT 5
	name: h2o_feet
	--------------
	time                           derivative
	2015-08-18T00:06:00Z     0.00014444444444444457
	2015-08-18T00:12:00Z     -0.00024444444444444465
	2015-08-18T00:18:00Z     0.0002722222222222218
	2015-08-18T00:24:00Z     -0.000236111111111111
	2015-08-18T00:30:00Z     2.777777777777842e-05
第一行数据的计算公式为(2.116 - 2.064) / (360s / 1s)

计算每六分钟的变化率

	> SELECT DERIVATIVE(water_level,6m) FROM h2o_feet WHERE location = 'santa_monica' LIMIT 5
	name: h2o_feet
	--------------
	time                           derivative
	2015-08-18T00:06:00Z     0.052000000000000046
	2015-08-18T00:12:00Z     -0.08800000000000008
	2015-08-18T00:18:00Z     0.09799999999999986
	2015-08-18T00:24:00Z     -0.08499999999999996
	2015-08-18T00:30:00Z     0.010000000000000231
第一行数据的计算过程如下：(2.116 - 2.064) / (6m / 6m)

计算每12分钟的变化率：

	> SELECT DERIVATIVE(water_level,12m) FROM h2o_feet WHERE location = 'santa_monica' LIMIT 5
	name: h2o_feet
	--------------
	time                           derivative
	2015-08-18T00:06:00Z     0.10400000000000009
	2015-08-18T00:12:00Z     -0.17600000000000016
	2015-08-18T00:18:00Z     0.19599999999999973
	2015-08-18T00:24:00Z     -0.16999999999999993
	2015-08-18T00:30:00Z     0.020000000000000462
第一行数据计算过程为：(2.116 - 2.064 / (6m / 12m)

计算每12分钟最大值的变化率

	> SELECT DERIVATIVE(MAX(water_level)) FROM h2o_feet WHERE location = 'santa_monica' AND time >= '2015-08-18T00:00:00Z' AND time < '2015-08-18T00:36:00Z' GROUP BY time(12m)
	name: h2o_feet
	--------------
	time                           derivative
	2015-08-18T00:12:00Z     0.009999999999999787
	2015-08-18T00:24:00Z     -0.07499999999999973
这个函数功能非常多，也非常复杂，更多对于此功能的详细解释请看官网：https://docs.influxdata.com/influxdb/v0.13/query_language/functions/#derivative

## DIFFERENCE()函数
作用：返回一个字段中连续的时间值之间的差异。字段类型必须是长整型或float64。

最基本的语法：

	SELECT DIFFERENCE(<field_key>) FROM <measurement_name> [WHERE <stuff>]
与GROUP BY time()以及其他嵌套函数一起使用的语法格式：

	SELECT DIFFERENCE(<function>(<field_key>)) FROM <measurement_name> WHERE <stuff> GROUP BY time(<time_interval>)
其中，函数可以包含以下几个：

COUNT(), MEAN(), MEDIAN(),SUM(), FIRST(), LAST(), MIN(), MAX(), 和 PERCENTILE()。

使用示例

例子中使用的源数据如下所示：

	> SELECT water_level FROM h2o_feet WHERE location='santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:36:00Z'
	name: h2o_feet
	--------------
	time                            water_level
	2015-08-18T00:00:00Z      2.064
	2015-08-18T00:06:00Z      2.116
	2015-08-18T00:12:00Z      2.028
	2015-08-18T00:18:00Z      2.126
	2015-08-18T00:24:00Z      2.041
	2015-08-18T00:30:00Z      2.051
	2015-08-18T00:36:00Z      2.067
计算water_level间的差异：

	> SELECT DIFFERENCE(water_level) FROM h2o_feet WHERE location='santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:36:00Z'
	name: h2o_feet
	--------------
	time                            difference
	2015-08-18T00:06:00Z      0.052000000000000046
	2015-08-18T00:12:00Z      -0.08800000000000008
	2015-08-18T00:18:00Z      0.09799999999999986
	2015-08-18T00:24:00Z      -0.08499999999999996
	2015-08-18T00:30:00Z      0.010000000000000231
	2015-08-18T00:36:00Z      0.016000000000000014
数据类型都为float类型。

## ELAPSED()函数
作用：返回一个字段在连续的时间间隔间的差异，间隔单位可选，默认为1纳秒。

单位可选项如下图：

image

语法：

	SELECT ELAPSED(<field_key>, <unit>) FROM <measurement_name> [WHERE <stuff>]
示例：

计算h2o_feet字段在纳秒间隔下的差异。

	> SELECT ELAPSED(water_level) FROM h2o_feet WHERE location = 'santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:24:00Z'
	name: h2o_feet
	--------------
	time                            elapsed
	2015-08-18T00:06:00Z      360000000000
	2015-08-18T00:12:00Z      360000000000
	2015-08-18T00:18:00Z      360000000000
	2015-08-18T00:24:00Z      360000000000
在一分钟间隔下的差异率：

	> SELECT ELAPSED(water_level,1m) FROM h2o_feet WHERE location = 'santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:24:00Z'
	name: h2o_feet
	--------------
	time                            elapsed
	2015-08-18T00:06:00Z      6
	2015-08-18T00:12:00Z      6
	2015-08-18T00:18:00Z      6
	2015-08-18T00:24:00Z      6
注意：如果设置的时间间隔比字段数据间的时间间隔更大时，则函数会返回0，如下所示：

	> SELECT ELAPSED(water_level,1h) FROM h2o_feet WHERE location = 'santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:24:00Z'
	name: h2o_feet
	--------------
	time                            elapsed
	2015-08-18T00:06:00Z      0
	2015-08-18T00:12:00Z      0
	2015-08-18T00:18:00Z      0
	2015-08-18T00:24:00Z      0

## MOVING_AVERAGE()函数
作用：返回一个连续字段值的移动平均值，字段类型必须是长整形或者float64类型。

语法：

基本语法

	SELECT MOVING_AVERAGE(<field_key>,<window>) FROM <measurement_name> [WHERE <stuff>]
与其他函数和GROUP BY time()语句一起使用时的语法

	SELECT MOVING_AVERAGE(<function>(<field_key>),<window>) FROM <measurement_name> WHERE <stuff> GROUP BY time(<time_interval>)
此函数可以和以下函数一起使用：

COUNT(), MEAN(),MEDIAN(), SUM(), FIRST(), LAST(), MIN(), MAX(), and PERCENTILE().

示例：

	> SELECT water_level FROM h2o_feet WHERE location = 'santa_monica' AND time >= '2015-08-18T00:00:00Z' and time <= '2015-08-18T00:36:00Z'
	name: h2o_feet
	--------------
	time                            water_level
	2015-08-18T00:00:00Z      2.064
	2015-08-18T00:06:00Z      2.116
	2015-08-18T00:12:00Z      2.028
	2015-08-18T00:18:00Z      2.126
	2015-08-18T00:24:00Z      2.041
	2015-08-18T00:30:00Z      2.051
	2015-08-18T00:36:00Z      2.067

## NON_NEGATIVE_DERIVATIVE()函数
作用：返回在一个series中的一个字段中值的变化的非负速率。

语法：

	SELECT NON_NEGATIVE_DERIVATIVE(<field_key>, [<unit>]) FROM <measurement_name> [WHERE <stuff>]
其中unit取值可以为以下几个：

image

与聚合类函数放在一起使用时的语法如下所示：

	SELECT NON_NEGATIVE_DERIVATIVE(AGGREGATION_FUNCTION(<field_key>),[<unit>]) FROM <measurement_name> WHERE <stuff> GROUP BY time(<aggregation_interval>)
此函数示例请参阅：DERIVATIVE()函数

## STDDEV()函数
作用：返回一个字段中的值的标准偏差。值的类型必须是长整型或float64类型。

语法：

	SELECT STDDEV(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT STDDEV(water_level) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           stddev
	1970-01-01T00:00:00Z     2.279144584196145
示例2：

	> SELECT STDDEV(water_level) FROM h2o_feet WHERE time >= '2015-08-18T00:00:00Z' and time < '2015-09-18T12:06:00Z' GROUP BY time(1w), location
	name: h2o_feet
	tags: location = coyote_creek
	time                           stddev
	----                           ------
	2015-08-13T00:00:00Z     2.2437263080193985
	2015-08-20T00:00:00Z     2.121276150144719
	2015-08-27T00:00:00Z     3.0416122170786215
	2015-09-03T00:00:00Z     2.5348065025435207
	2015-09-10T00:00:00Z     2.584003954882673
	2015-09-17T00:00:00Z     2.2587514836274414

	name: h2o_feet
	tags: location = santa_monica
	time                           stddev
	----                           ------
	2015-08-13T00:00:00Z     1.11156344587553
	2015-08-20T00:00:00Z     1.0909849279082366
	2015-08-27T00:00:00Z     1.9870116180096962
	2015-09-03T00:00:00Z     1.3516778450902067
	2015-09-10T00:00:00Z     1.4960573811500588
	2015-09-17T00:00:00Z     1.075701669442093