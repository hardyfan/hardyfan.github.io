# 聚合类函数

## count()函数
返回一个（field）字段中的非空值的数量。

语法：

	SELECT COUNT(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	>SELECT COUNT(water_level) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           count
	1970-01-01T00:00:00Z     15258
说明 water_level这个字段在 h2o_feet表中共有15258条数据。

注意：InfluxDB中的函数如果没有指定时间的话，会默认以 epoch 0 (1970-01-01T00:00:00Z) 作为时间。

可以在where 中加入时间条件，如下：

	> SELECT COUNT(water_level) FROM h2o_feet WHERE time >= '2015-08-18T00:00:00Z' AND time < '2015-09-18T17:00:00Z' GROUP BY time(4d)
	name: h2o_feet
	--------------
	time                           count
	2015-08-17T00:00:00Z     1440
	2015-08-21T00:00:00Z     1920
	2015-08-25T00:00:00Z     1920
	2015-08-29T00:00:00Z     1920
	2015-09-02T00:00:00Z     1915
	2015-09-06T00:00:00Z     1920
	2015-09-10T00:00:00Z     1920
	2015-09-14T00:00:00Z     1920
	2015-09-18T00:00:00Z     335
这样结果中会包含时间结果。

## DISTINCT()函数
返回一个字段（field）的唯一值。

语法：

	SELECT DISTINCT(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例

	> SELECT DISTINCT("level description") FROM h2o_feet
	name: h2o_feet
	--------------
	time                           distinct
	1970-01-01T00:00:00Z     between 6 and 9 feet
	1970-01-01T00:00:00Z     below 3 feet
	1970-01-01T00:00:00Z     between 3 and 6 feet
	1970-01-01T00:00:00Z     at or greater than 9 feet
这个例子显示level description这个字段共有四个值，然后将其显示了出来，时间为默认时间。

## MEAN() 函数
返回一个字段（field）中的值的算术平均值（平均值）。字段类型必须是长整型或float64。

语法格式：

	SELECT MEAN(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例

	> SELECT MEAN(water_level) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           mean
	1970-01-01T00:00:00Z     4.286791371454075
说明water_level字段的平均值为4.286791371454075

时间为默认时间，当然，你也可以加入where条件。

## MEDIAN()函数
从单个字段（field）中的排序值返回中间值（中位数）。字段值的类型必须是长整型或float64格式。

语法：

	SELECT MEDIAN(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例

	> SELECT MEDIAN(water_level) from h2o_feet
	name: h2o_feet
	--------------
	time                           median
	1970-01-01T00:00:00Z     4.124
说明表中 water_level字段的中位数是 4.124

## SPREAD()函数
返回字段的最小值和最大值之间的差值。数据的类型必须是长整型或float64。

语法：

	SELECT SPREAD(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例

	> SELECT SPREAD(water_level) FROM h2o_feet
	name: h2o_feet
	--------------
	time                            spread
	1970-01-01T00:00:00Z      10.574
## SUM()函数
返回一个字段中的所有值的和。字段的类型必须是长整型或float64。

语法：

	SELECT SUM(<field_key>) FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例：

	> SELECT SUM(water_level) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           sum
	1970-01-01T00:00:00Z     67777.66900000002
此语句计算出了 h2o_feet表中 所有 water_level 字段的和。
