# HTTP API写入操作

## 说明
为了方便，本文主要使用curl来发起http请求，示例当中也是使用curl这个工具来模拟HTTP 请求。

在实际使用中，可以将请求写入代码中，通过其他编程语言来模拟HTTP请求。

## InfluxDB通过HTTP API操作数据库
1. 建立数据库

		curl -POST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE mydb"
		
	执行这个语句后，会在本地建立一个名为mydb的数据库。

2. 删除数据库

		curl -POST http://localhost:8086/query --data-urlencode "q=DROP DATABASE mydb"
	其实使用HTTP API就是向 InfluxDB 接口发送相应的POST请求。

	将语句通过POST方式发送到服务器。

## InfluxDB通过HTTP API添加数据
InfluxDB通过HTTP API添加数据主要使用如下格式：

	curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'
说明：db=mydb是指使用mydb这个数据库。

--data-binary后面是需插入数据。

cpu_load_short是表名（measurement），tag字段是host和region，值分别为：server01和us-west。

field key字段是value，值为0.64。

时间戳（timestamp）指定为1434055562000000000。

这样，就向mydb数据库的cpu_load_short表中插入了一条数据。

其中，db参数必须指定一个数据库中已经存在的数据库名，数据体的格式遵从InfluxDB规定格式，首先是表名，后面是tags，然后是field，最后是时间戳。tags、field和时间戳三者之间以空格相分隔。

## InfluxDB通过HTTP API添加多条数据
InfluxDB通过HTTP API添加多条数据与添加单条数据相似，示例如下：

	curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server02 value=0.67
	cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
	cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257'
这条语句向数据库mydb的表cpu_load_short中插入了三条数据。

第一条指定tag为host，值为server02，第二条指定tag为host和region，值分别为server02和us-west，第三条指定tag为direction，host，region，值分别为：in，server01，us-west。

## InfluxDB 的HTTP API响应
在使用HTTP API时，InfluxDB的响应主要有以下几个：

1）2xx：204代表no content，200代表InfluxDB可以接收请求但是没有完成请求。一般会在body体中带有出错信息。

2）4xx：InfluxDB不能解析请求。

3）5xx：系统出现错误。

 