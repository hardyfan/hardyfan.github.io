# InfluxDB启动

## 服务端启动

如果是通过包安装的，可以使用如下语句启动：

	sudo service influxdb start
如果直接下载的二进制包，则进入InfluxDB目录下的usr/bin文件夹，执行：

	./influxd

即可。  
这样就启动了服务端。

## 客户端

在usr/bin里使用influx即可登入Influx服务器。也可以将路径加入环境变量中，这样既可在任意地方使用influx。

InfluxDB自带web管理界面，在浏览器中输入 http://服务器IP:8083 即可进入web管理页面。
