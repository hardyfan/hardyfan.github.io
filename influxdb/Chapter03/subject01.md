# 基本操作

如同MYSQL一样，InfluxDB提供多数据库支持，对数据库的操作也与MYSQL相同。

## 数据库操作

### 显示数据库：

	> show databases
	name: databases
	---------------
	name
	telegraf
	_internal
	lir
	testDB
	testMyDb

### 新建数据库：

	> create database test
	> show databases
	name: databases
	---------------
	name
	telegraf
	_internal
	lir
	testDB
	testMyDb
	xk_name
	test

### 删除数据库

	> drop database test
	> show databases
	name: databases
	---------------
	name
	telegraf
	_internal
	lir
	testDB
	testMyDb
	xk_name

### 使用某个数据库

	> use xk_name
	Using database xk_name

## 数据表操作

在InfluxDB当中，并没有表（table）这个概念，取而代之的是MEASUREMENTS，MEASUREMENTS的功能与传统数据库中的表一致，因此我们也可以将MEASUREMENTS称为InfluxDB中的表。

### 显示所有表

	>SHOW MEASUREMENTS
	name: measurements
	------------------
	name
	weather

### 新建表

InfluxDB中没有显式的新建表的语句，只能通过insert数据的方式来建立新表。如下所示：

	insert disk_free,hostname=server01 value=442221834240i 1435362189575692182

其中 disk_free 就是表名，hostname是索引，value=xx是记录值，记录值可以有多个，最后是指定的时间

执行后结果如下

	> select * from disk_free
	name: disk_free
	---------------
	time            hostname    value
	1435362189575692182    server01    442221834240

### 删除表

	> drop measurement disk_free
	> show measurements
	name: measurements
	------------------
	name
	weather


## 数据操作

### 增加数据

增加数据采用insert的方式，要注意的是 InfluxDB的insert中，表名与数据之间用逗号（,）分隔，tag和field之间用 空格分隔，多个tag或者多个field之间用逗号（,）分隔。

	> insert disk_free,hostname=server01 value=442221834240i 1435362189575692182
	> select * from disk_free
	name: disk_free
	---------------
	time            hostname    value
	1435362189575692182    server01    442221834240


在这条语句中，disk_free是表名,hostname=server01是tag，属于索引，value=xx是field，这个可以随意写，随意定义。

### 查询数据

查询语句与SQL一样，在此不再赘述。

### 修改和删除数据  

InfluxDB属于时序数据库，没有提供修改和删除数据的方法。

但是删除可以通过InfluxDB的数据保存策略（Retention Policies）来实现，这个会在以后的文章中讲到。

## series操作

series表示这个表里面的数据，可以在图表上画成几条线，series主要通过tags排列组合算出来。

我们可以查询表的series，如下所示：

	> show series from mem
	key
	mem,host=ResourcePool-0246-billing07
	mem,host=billing07


## 界面操作

InfluxDB还提供了管理界面，大大降低了入门难度，在启动了InfluxDB服务之后，直接输入 `<IP>:8083` 即可访问界面。
