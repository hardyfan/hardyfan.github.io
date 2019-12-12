# 选择类函数

## TOP()函数
作用：返回一个字段中最大的N个值，字段类型必须是长整型或float64类型。

语法：

	SELECT TOP( <field_key>[,<tag_key(s)>],<N> )[,<tag_key(s)>|<field_key(s)>] [INTO_clause] FROM_clause [WHERE_clause] [GROUP_BY_clause] [ORDER_BY_clause] [LIMIT_clause] [OFFSET_clause] [SLIMIT_clause] [SOFFSET_clause]
使用示例

	> SELECT TOP("water_level",3) FROM "h2o_feet"

	name: h2o_feet
	time                   top
	----                   ---
	2015-08-29T07:18:00Z   9.957
	2015-08-29T07:24:00Z   9.964
	2015-08-29T07:30:00Z   9.954
这个例子返回表中 water_level字段中最大的三个值。

## BOTTOM()函数
作用：返回一个字段中最小的N个值。字段类型必须是长整型或float64类型。

语法：

	SELECT BOTTOM(<field_key>[,<tag_keys>],<N>)[,<tag_keys>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
使用示例

	> SELECT BOTTOM(water_level,3) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           bottom
	2015-08-29T14:30:00Z     -0.61
	2015-08-29T14:36:00Z     -0.591
	2015-08-30T15:18:00Z     -0.594
这个例子返回表中 water_level字段中最小的三个值。

也可将关联tag放在一起查询，但如果tag值少于N的值，则返回的值的个数只会取tag中字段值少的那个。

如下所示：

	> SELECT BOTTOM(water_level,location,3) FROM h2o_feet
	name: h2o_feet
	--------------
	time                           bottom     location
	2015-08-29T10:36:00Z     -0.243     santa_monica
	2015-08-29T14:30:00Z     -0.61      coyote_creek
语句取最小的三个值，然而结果只返回了2个值，因为 location 这个tag只有 两个取值。

## FIRST()函数
作用：返回一个字段中最老的取值。

语法：

	SELECT FIRST(<field_key>)[,<tag_key(s)>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT FIRST(water_level) FROM h2o_feet WHERE location = 'santa_monica'
	name: h2o_feet
	--------------
	time                           first
	2015-08-18T00:00:00Z     2.064
这个语句返回了 在 location为santa_monica条件下，最旧的那个water_level字段的取值和时间。

## LAST()函数
作用：返回一个字段中最新的取值。

语法：

	SELECT LAST(<field_key>)[,<tag_key(s)>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT LAST(water_level),location FROM h2o_feet WHERE time >= '2015-08-18T00:42:00Z' and time <= '2015-08-18T00:54:00Z'
	name: h2o_feet
	--------------
	time                           last      location
	2015-08-18T00:54:00Z     6.982     coyote_creek

## MAX()函数
作用：返回一个字段中的最大值。该字段类型必须是长整型，float64，或布尔类型。

语法：

	SELECT MAX(<field_key>)[,<tag_key(s)>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT MAX(water_level),location FROM h2o_feet
	name: h2o_feet
	--------------
	time                           max       location
	2015-08-29T07:24:00Z     9.964     coyote_creek
## MIN()函数
作用：返回一个字段中的最小值。该字段类型必须是长整型，float64，或布尔类型。

语法：

	SELECT MIN(<field_key>)[,<tag_key(s)>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT MIN(water_level),location FROM h2o_feet
	name: h2o_feet
	--------------
	time                          min       location
	2015-08-29T14:30:00Z    -0.61     coyote_creek
## PERCENTILE()函数
作用：返回排序值排位为N的百分值。字段的类型必须是长整型或float64。

百分值是介于100到0之间的整数或浮点数，包括100。

语法：

	SELECT PERCENTILE(<field_key>, <N>)[,<tag_key(s)>] FROM <measurement_name> [WHERE <stuff>] [GROUP BY <stuff>]
示例：

	> SELECT PERCENTILE(water_level,5),location FROM h2o_feet
	name: h2o_feet
	--------------
	time                      percentile     location
	2015-08-28T12:06:00Z      1.122             santa_monica
就是将water_level字段按照不同的location求百分比，然后取第五位数据。