# Grafana安装和配置

## 1、grafana解释

>Grafana 是面向 Graphite, InfluxDB & OpenTSDB 开源(Apache v2)可视化仪表盘，使用 Go 和 Node.js 开发。官网为：https://grafana.com/

## 2、grafana安装

>采用rpm来安装，简单快捷

```

wget wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana-5.3.2-1.x86_64.rpm
yum localinstall grafana-5.3.2-1.x86_64.rpm -y
systemctl daemon-reload
systemctl start grafana-server.service
systemctl status grafana-server.service
systemctl enable grafana-server.service

```

## 3、登陆grafana系统

>登陆http://ip:3000，初始密码admin/admin，登陆后会提醒修改密码


## 4、配置grafana

```
step1：确认grafana的数据源
目前默认就支持的数据源有：
Cloudwatch
Elasticsearch
Graphite
InfluxDB
Microsoft SQL Server
Mysql
OpenTSDB
PostgreSQL
Prometheus

如果需要接入其他的数据源，例如zabbix的数据，只需要安装zbbix的plugin插件即可。
插件的下载链接为https://grafana.com/plugins
本地采用的是prometheus，所以选择prometheus。

step2: 配置grafana数据源
name: Prometheus
type：prometheus

HTTP：http://192.168.1.118:9090
ACCESS: Server

然后点击Save && Test测试链接是否正常


step3: 创建监控模板

grafana模板使用的查询语句与添加的数据源一致，如果数据源为prometheus，获取数据的查询语句必须是prometheusSQL。语法可以去prometheus官网了解。
自己创建模板比较繁琐，一般去grafana官网挑选别人做好并共享的模板。
官网darshboard地址https://grafana.com/dashboards
看到喜欢的模板，直接点击进去，获取对应的id
然后再本地的grafana上选择导入模板，输入id保存即可，经常出现配置不一致导致无法获取数据，多尝试几个就行了。


```
