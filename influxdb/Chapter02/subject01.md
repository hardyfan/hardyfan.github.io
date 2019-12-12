# InfluxDB基本概念

## 与传统数据库中的名词做比较
| influxDB中的名词 | 传统数据库中的概念 |
| -- | -- |
| database | 数据库 |
| measurement | 数据库中的表 |
| points | 表里面的一行数据 |
 

## InfluxDB中独有的概念


1. tag--标签：在InfluxDB中，tag是一个非常重要的部分，表名+tag一起作为数据库的索引，是“key-value”的形式。
2. field--数据：field主要是用来存放数据的部分，也是“key-value”的形式。
3. timestamp--时间戳：作为时序型数据库，时间戳是InfluxDB中最重要的部分，在插入数据时可以自己指定也可留空让系统指定。
说明：在插入新数据时，tag、field和timestamp之间用空格分隔。
4. series--序列：所有在数据库中的数据，都需要通过图表来展示，而这个series表示这个表里面的数据，可以在图表上画成几条线。
5. Retention policy--数据保留策略：可以定义数据保留的时长，每个数据库可以有多个数据保留策略，但只能有一个默认策略。
6. Point--点：表示每个表里某个时刻的某个条件下的一个field的数据，因为体现在图表上就是一个点，于是将其称为point。
