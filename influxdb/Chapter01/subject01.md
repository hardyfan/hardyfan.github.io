# InfluxDB安装

本文以写这篇文章时的最新稳定版（Stable v0.13.0）为例，介绍下InfluxDB的安装。

OS X (via Homebrew)

	brew update
	brew install influxdb
	MD5: 4f0aa76fee22cf4c18e2a0779ba4f462

Ubuntu & Debian (64-bit)

	wget https://dl.influxdata.com/influxdb/releases/influxdb_0.13.0_amd64.deb
	sudo dpkg -i influxdb_0.13.0_amd64.deb
	MD5: bcca4c91bbd8e7f60e4a8325be67a08a

Ubuntu & Debian (ARM)

	wget https://dl.influxdata.com/influxdb/releases/influxdb_0.13.0_armhf.deb
	sudo dpkg -i influxdb_0.13.0_armhf.deb
	MD5: b64ada82b6abf5d6382ed08dde1e8579

RedHat & CentOS (64-bit)

	wget https://dl.influxdata.com/influxdb/releases/influxdb-0.13.0.x86_64.rpm
	sudo yum localinstall influxdb-0.13.0.x86_64.rpm
	MD5: 286b6c18aa4ef37225ea6605a729b61d

RedHat & CentOS (ARM)

	wget https://dl.influxdata.com/influxdb/releases/influxdb-0.13.0.armhf.rpm
	sudo yum localinstall influxdb-0.13.0.armhf.rpm
	MD5: 4cf99debb5315fbbb26166506807d965

Standalone Binaries (64-bit)

	wget https://dl.influxdata.com/influxdb/releases/influxdb-0.13.0_linux_amd64.tar.gz
	tar xvfz influxdb-0.13.0_linux_amd64.tar.gz
	MD5: 187854536393c67f7793ada1c096da8e

Standalone Binaries (ARM)

	wget https://dl.influxdata.com/influxdb/releases/influxdb-0.13.0_linux_armhf.tar.gz
	tar xvfz influxdb-0.13.0_linux_armhf.tar.gz

Docker Image

	docker pull influxdb