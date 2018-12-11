# prometheus监控

## 1、node_exporter安装

>node_exporter负责将主机的运行数据暴露出来，因此安装到所有需要监控的linux主机上。

### 1.1、node_exporter下载

>去github上找到项目，对应的地址为https://github.com/prometheus/node_exporter/releases，
找到最新稳定版下载所需版本即可。

## 1.2、安装并运行node_exporter

```
tar -zxvf node_exporter-0.16.0.linux-amd64.tar.gz
mv node_exporter-0.16.0.linux-amd64 node_exporter
cd node_exporter/

nohup ./node_exporter &

这样node_exporter就运行起来了
ps -ef |grep node_exporter
root     31427     1  0 11月13 ?      00:09:33 ./node_exporter
root     38683 33980  0 11:18 pts/1    00:00:00 grep --color=auto node_exporter
ss -tnlp | grep 31427
LISTEN     0      128         :::9100                    :::*                   users:(("node_exporter",pid=31427,fd=3))

可以看出node_exporter运行在9100端口上
打开浏览器，输入http://ip:9100可以查看node_exporter收集到的linux运行数据

通过curl查看的信息如下
[root@k8s-prod-2 ~]# curl -s http://localhost:9100/metrics | more
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 0.000119762
go_gc_duration_seconds{quantile="0.25"} 0.000187967
go_gc_duration_seconds{quantile="0.5"} 0.000231797
go_gc_duration_seconds{quantile="0.75"} 0.000292431
go_gc_duration_seconds{quantile="1"} 0.000655731
go_gc_duration_seconds_sum 5.466462447
go_gc_duration_seconds_count 21331
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 7
# HELP go_info Information about the Go environment.
# TYPE go_info gauge
go_info{version="go1.9.6"} 1
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 3.748344e+06
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 2.6704255472e+10
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 2.045504e+06
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 2.84067809e+08
# HELP go_memstats_gc_cpu_fraction The fraction of this program's available CPU time used by the GC since the program started.
# TYPE go_memstats_gc_cpu_fraction gauge
go_memstats_gc_cpu_fraction 0.00010158338367633976
# HELP go_memstats_gc_sys_bytes Number of bytes used for garbage collection system metadata.
# TYPE go_memstats_gc_sys_bytes gauge
go_memstats_gc_sys_bytes 610304

```

在所有需要监控的linux主机上都按照上述步骤安装并启动node_exporter即可。




## 2、mysqld_exporter安装

>mysqld_exporter负责将mysql的运行数据暴露出来，通过读取配置文件来获取对应的mysql数据库，因此安装到统一的监控主机上。


## 2.1、mysqld_exporter下载

> 去github上找到项目，对应的地址为https://github.com/prometheus/mysqld_exporter/releases，
找到最新稳定版下载所需版本即可。

## 2.2、mysqld_exporter安装并运行

```
tar -zxvf mysqld_exporter-0.11.0.linux-amd64.tar.gz
mv mysqld_exporter-0.11.0.linux-amd64 mysqld_exporter
cd mysqld_exporter
./mysqld_exporter -h
usage: mysqld_exporter [<flags>]

Flags:
  -h, --help                   Show context-sensitive help (also try --help-long and --help-man).
      --exporter.lock_wait_timeout=2  
                               Set a lock_wait_timeout on the connection to avoid long metadata locking.
      --exporter.log_slow_filter  
                               Add a log_slow_filter to avoid slow query logging of scrapes. NOTE: Not supported by Oracle MySQL.
      --collect.heartbeat.database="heartbeat"  
                               Database from where to collect heartbeat data
      --collect.heartbeat.table="heartbeat"  
                               Table from where to collect heartbeat data
      --collect.info_schema.processlist.min_time=0  
                               Minimum time a thread must be in each state to be counted
      --collect.info_schema.tables.databases="*"  
                               The list of databases to collect table stats for, or '*' for all
      --collect.perf_schema.eventsstatements.limit=250  
                               Limit the number of events statements digests by response time
      --collect.perf_schema.eventsstatements.timelimit=86400  
                               Limit how old the 'last_seen' events statements can be, in seconds
      --collect.perf_schema.eventsstatements.digest_text_limit=120  
                               Maximum length of the normalized statement text
      --collect.perf_schema.file_instances.filter=".*"  
                               RegEx file_name filter for performance_schema.file_summary_by_instance
      --collect.perf_schema.file_instances.remove_prefix="/var/lib/mysql/"  
                               Remove path prefix in performance_schema.file_summary_by_instance
      --web.listen-address=":9104"  
                               Address to listen on for web interface and telemetry.
      --web.telemetry-path="/metrics"  
                               Path under which to expose metrics.
      --config.my-cnf="/root/.my.cnf"  
                               Path to .my.cnf file to read MySQL credentials from.
      --collect.global_status  Collect from SHOW GLOBAL STATUS
      --collect.global_variables  
                               Collect from SHOW GLOBAL VARIABLES
      --collect.slave_status   Collect from SHOW SLAVE STATUS
      --collect.info_schema.processlist  
                               Collect current thread state counts from the information_schema.processlist
      --collect.info_schema.tables  
                               Collect metrics from information_schema.tables
      --collect.info_schema.innodb_tablespaces  
                               Collect metrics from information_schema.innodb_sys_tablespaces
      --collect.info_schema.innodb_metrics  
                               Collect metrics from information_schema.innodb_metrics
      --collect.auto_increment.columns  
                               Collect auto_increment columns and max values from information_schema
      --collect.binlog_size    Collect the current size of all registered binlog files
      --collect.perf_schema.tableiowaits  
                               Collect metrics from performance_schema.table_io_waits_summary_by_table
      --collect.perf_schema.indexiowaits  
                               Collect metrics from performance_schema.table_io_waits_summary_by_index_usage
      --collect.perf_schema.tablelocks  
                               Collect metrics from performance_schema.table_lock_waits_summary_by_table
      --collect.perf_schema.eventsstatements  
                               Collect metrics from performance_schema.events_statements_summary_by_digest
      --collect.perf_schema.eventswaits  
                               Collect metrics from performance_schema.events_waits_summary_global_by_event_name
      --collect.perf_schema.file_events  
                               Collect metrics from performance_schema.file_summary_by_event_name
      --collect.perf_schema.file_instances  
                               Collect metrics from performance_schema.file_summary_by_instance
      --collect.perf_schema.replication_group_member_stats  
                               Collect metrics from performance_schema.replication_group_member_stats
      --collect.info_schema.userstats  
                               If running with userstat=1, set to true to collect user statistics
      --collect.info_schema.clientstats  
                               If running with userstat=1, set to true to collect client statistics
      --collect.info_schema.tablestats  
                               If running with userstat=1, set to true to collect table statistics
      --collect.info_schema.innodb_cmp  
                               Collect metrics from information_schema.innodb_cmp
      --collect.info_schema.innodb_cmpmem  
                               Collect metrics from information_schema.innodb_cmpmem
      --collect.info_schema.query_response_time  
                               Collect query response time distribution if query_response_time_stats is ON.
      --collect.engine_tokudb_status  
                               Collect from SHOW ENGINE TOKUDB STATUS
      --collect.engine_innodb_status  
                               Collect from SHOW ENGINE INNODB STATUS
      --collect.heartbeat      Collect from heartbeat
      --collect.slave_hosts    Scrape information from 'SHOW SLAVE HOSTS'
      --log.level="info"       Only log messages with the given severity or above. Valid levels: [debug, info, warn, error, fatal]
      --log.format="logger:stderr"  
                               Set the log target and format. Example: "logger:syslog?appname=bob&local=7" or "logger:stdout?json=true"
      --version                Show application version.

根据命令的帮助，新建需要监控mysql数据库的配置文件，指定mysql服务器的地址、端口、用户名和密码
[root@monitor mysqld_exporter]# more .my.cnf
[client]
host=192.168.1.110
port=3306
user=develop
password=develop

nohup ./mysqld_exporter -config.my-cnf=".my.cnf" &

ps -ef | grep mysqld_exporter
root      1466     1  0 11月13 ?      00:02:58 ./mysqld_exporter --config.my-cnf=.my.cnf
root      4048  4021  0 11:31 pts/0    00:00:00 grep --color=auto mysqld_exporter
ss -tnlp | grep 1466
LISTEN     0      128         :::9104                    :::*                   users:(("mysqld_exporter",pid=1466,fd=3))

可以看出mysqld_exporter运行在主机的9104端口
打开浏览器，输入http://ip:9104可以查看暴露的mysql数据

使用curl查询结果如下:
curl -s http://localhost:9104/metrics | more
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 3.211e-05
go_gc_duration_seconds{quantile="0.25"} 4.5891e-05
go_gc_duration_seconds{quantile="0.5"} 6.0217e-05
go_gc_duration_seconds{quantile="0.75"} 8.9865e-05
go_gc_duration_seconds{quantile="1"} 0.000292202
go_gc_duration_seconds_sum 0.467676625
go_gc_duration_seconds_count 6026
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 9
# HELP go_info Information about the Go environment.
# TYPE go_info gauge
go_info{version="go1.10.3"} 1
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 3.308784e+06
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 1.6259537424e+10
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 1.581778e+06

```


## 3、redis_exporter安装

>redis_exporter负责将mysql的运行数据暴露出来，通过读取配置文件来获取对应的redis数据库，因此安装到统一的监控主机上。


### 3.1、redis_exporter下载

>prometheus官方没有提供redis的expoeter，只能找第三个个人写的redis的exporter。第三方的gitlab连接为https://github.com/oliver006/redis_exporter/releases，
好到对应的稳定版本下载即可。

### 3.2、redis_exporter安装并运行

```
mkdir redis_exporter
tar -zxvf redis_exporter-v0.22.0.linux-amd64.tar.gz
./redis_exporter -h
Usage of ./redis_exporter:
  -check-keys string
    	Comma separated list of key-patterns to export value and length/size, searched for with SCAN
  -check-single-keys string
    	Comma separated list of single keys to export value and length/size
  -debug
    	Output verbose debug information
  -log-format string
    	Log format, valid options are txt and json (default "txt")
  -namespace string
    	Namespace for metrics (default "redis")
  -redis-only-metrics
    	Whether to export go runtime metrics also
  -redis.addr string
    	Address of one or more redis nodes, separated by separator
  -redis.alias string
    	Redis instance alias for one or more redis nodes, separated by separator
  -redis.file string
    	Path to file containing one or more redis nodes, separated by newline. NOTE: mutually exclusive with redis.addr
  -redis.password string
    	Password for one or more redis nodes, separated by separator
  -script string
    	Path to Lua Redis script for collecting extra metrics
  -separator string
    	separator used to split redis.addr, redis.password and redis.alias into several elements. (default ",")
  -use-cf-bindings
    	Use Cloud Foundry service bindings
  -version
    	Show version information and exit
  -web.listen-address string
    	Address to listen on for web interface and telemetry. (default ":9121")
  -web.telemetry-path string
    	Path under which to expose metrics. (default "/metrics")

根据命令的帮助，新建需要监控redis数据库的配置文件，指定redis服务器的地址、端口、用密码
more redis-host.conf
redis://192.168.1.111:36381

nohup ./redis_exporter -redis.file=redis-host.conf &

ps -ef | grep redis_exporter
root      1511     1  0 11月13 ?      00:01:05 ./redis_exporter -redis.file=redis-host.conf
root      4065  4021  0 11:42 pts/0    00:00:00 grep --color=auto redis_exporter
ss -tnlp | grep 1511
LISTEN     0      128         :::9121                    :::*                   users:(("redis_exporter",pid=1511,fd=3))

查看监控数据的方式同上

curl -s localhost:9121/metrics | more
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 2.2575e-05
go_gc_duration_seconds{quantile="0.25"} 4.399e-05
go_gc_duration_seconds{quantile="0.5"} 8.1792e-05
go_gc_duration_seconds{quantile="0.75"} 0.000124709
go_gc_duration_seconds{quantile="1"} 0.000982723
go_gc_duration_seconds_sum 1.296468151
go_gc_duration_seconds_count 9813
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 10
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 1.361768e+06
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 3.33771926e+10
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 1.537547e+06
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 6.1362912e+07
# HELP go_memstats_gc_sys_bytes Number of bytes used for garbage collection system metadata.
# TYPE go_memstats_gc_sys_bytes gauge
go_memstats_gc_sys_bytes 2.371584e+06
# HELP go_memstats_heap_alloc_bytes Number of heap bytes allocated and still in use.
# TYPE go_memstats_heap_alloc_bytes gauge
go_memstats_heap_alloc_bytes 1.361768e+06

```


## 3、ceph_export安装
>ceph有完善的认证机制，选择简单的方式就是将ceph_exporter安装到ceph的安装主机上。

### 3.1、ceph_exporter下载

>prometheus官方没有提供ceph_export，有个大神用go自己写了一个ceph_exporter的开源项目，下载连接为
https://github.com/digitalocean/ceph_exporter/releases，
下载下来是go的源码，需要安装golang，还要编译，我尝试失败了，从网上找到对应的rpm包来安装。

```
wget http://static.zybuluo.com/zphj1987/jiwx305b8q1hwc5uulo0z7ft/ceph_exporter-2.0.0-1.x86_64.rpm

```

### 3.2、ceph_exporter安装并运行

```
yum -y install ceph_exporter-2.0.0-1.x86_64.rpm
systemctl start ceph-exporter
systemctl status ceph_exporter
systemctl enable ceph_exporter

ps -ef | grep ceph_exporter
root     28144     1  0 11月13 ?      00:01:18 /usr/bin/ceph_exporter
root     29988 29967  0 11:52 pts/0    00:00:00 grep --color=auto ceph_exporter
ss -tnlp | grep 28144
LISTEN     0      128         :::9128                    :::*                   users:(("ceph_exporter",pid=28144,fd=3))
[root@ceph-1 ~]# curl -s http://localhost:9128/metrics | more
# HELP ceph_active_pgs No. of active PGs in the cluster
# TYPE ceph_active_pgs gauge
ceph_active_pgs{cluster="ceph"} 192
# HELP ceph_cache_evict_io_bytes Rate of bytes being evicted from the cache pool per second
# TYPE ceph_cache_evict_io_bytes gauge
ceph_cache_evict_io_bytes{cluster="ceph"} 0
# HELP ceph_cache_flush_io_bytes Rate of bytes being flushed from the cache pool per second
# TYPE ceph_cache_flush_io_bytes gauge
ceph_cache_flush_io_bytes{cluster="ceph"} 0
# HELP ceph_cache_promote_io_ops Total cache promote operations measured per second
# TYPE ceph_cache_promote_io_ops gauge
ceph_cache_promote_io_ops{cluster="ceph"} 0
# HELP ceph_client_io_ops Total client ops on the cluster measured per second
# TYPE ceph_client_io_ops gauge
ceph_client_io_ops{cluster="ceph"} 5
# HELP ceph_client_io_read_bytes Rate of bytes being read by all clients per second
# TYPE ceph_client_io_read_bytes gauge
ceph_client_io_read_bytes{cluster="ceph"} 0
# HELP ceph_client_io_read_ops Total client read I/O ops on the cluster measured per second
# TYPE ceph_client_io_read_ops gauge
ceph_client_io_read_ops{cluster="ceph"} 0
# HELP ceph_client_io_write_bytes Rate of bytes being written by all clients per second
# TYPE ceph_client_io_write_bytes gauge
ceph_client_io_write_bytes{cluster="ceph"} 15541
# HELP ceph_client_io_write_ops Total client write I/O ops on the cluster measured per second
# TYPE ceph_client_io_write_ops gauge
ceph_client_io_write_ops{cluster="ceph"} 5
# HELP ceph_cluster_available_bytes Available space within the cluster
# TYPE ceph_cluster_available_bytes gauge
ceph_cluster_available_bytes{cluster="ceph"} 1.524168155136e+12
# HELP ceph_cluster_capacity_bytes Total capacity of the cluster
# TYPE ceph_cluster_capacity_bytes gauge
ceph_cluster_capacity_bytes{cluster="ceph"} 1.59372484608e+12
# HELP ceph_cluster_objects No. of rados objects within the cluster
# TYPE ceph_cluster_objects gauge
ceph_cluster_objects{cluster="ceph"} 5516
# HELP ceph_cluster_used_bytes Capacity of the cluster currently in use
# TYPE ceph_cluster_used_bytes gauge
ceph_cluster_used_bytes{cluster="ceph"} 6.9556690944e+10
# HELP ceph_deep_scrubbing_pgs No. of deep scrubbing PGs in the cluster
# TYPE ceph_deep_scrubbing_pgs gauge
ceph_deep_scrubbing_pgs{cluster="ceph"} 0
# HELP ceph_degraded_objects No. of degraded objects across all PGs, includes replicas
# TYPE ceph_degraded_objects gauge
ceph_degraded_objects{cluster="ceph"} 0
# HELP ceph_degraded_pgs No. of PGs in a degraded state
# TYPE ceph_degraded_pgs gauge
ceph_degraded_pgs{cluster="ceph"} 0
# HELP ceph_health_status Health status of Cluster, can vary only between 3 states (err:2, warn:1, ok:0)
# TYPE ceph_health_status gauge
ceph_health_status{cluster="ceph"} 0


```


## 4、prometheus安装

>prometheus负责收集所有的exporter暴露出来的metrics，因此只需要将Prometheus部署在监控主机上即可。

### 4.1、prometheus下载

>prometheus的官方github提供了下载，下载链接为https://github.com/prometheus/prometheus/releases,
找到对应的版本下载即可。


### 4.2、prometheus安装并运行

```
tar -zxvf prometheus-2.5.0.linux-amd64.tar.gz
mv prometheus-2.5.0.linux-amd64 prometheus
cd prometheus
./prometheus -h
usage: prometheus [<flags>]

The Prometheus monitoring server

Flags:
  -h, --help                     Show context-sensitive help (also try --help-long and --help-man).
      --version                  Show application version.
      --config.file="prometheus.yml"  
                                 Prometheus configuration file path.
      --web.listen-address="0.0.0.0:9090"  
                                 Address to listen on for UI, API, and telemetry.
      --web.read-timeout=5m      Maximum duration before timing out read of the request, and closing idle connections.
      --web.max-connections=512  Maximum number of simultaneous connections.
      --web.external-url=<URL>   The URL under which Prometheus is externally reachable (for example, if Prometheus is served via a reverse proxy). Used for generating relative and absolute links back to Prometheus itself. If the URL has a path portion, it will be used
                                 to prefix all HTTP endpoints served by Prometheus. If omitted, relevant URL components will be derived automatically.
      --web.route-prefix=<path>  Prefix for the internal routes of web endpoints. Defaults to path of --web.external-url.
      --web.user-assets=<path>   Path to static asset directory, available at /user.
      --web.enable-lifecycle     Enable shutdown and reload via HTTP request.
      --web.enable-admin-api     Enable API endpoints for admin control actions.
      --web.console.templates="consoles"  
                                 Path to the console template directory, available at /consoles.
      --web.console.libraries="console_libraries"  
                                 Path to the console library directory.
      --storage.tsdb.path="data/"  
                                 Base path for metrics storage.
      --storage.tsdb.retention=15d  
                                 How long to retain samples in storage.
      --storage.tsdb.no-lockfile  
                                 Do not create lockfile in data directory.
      --storage.remote.flush-deadline=<duration>  
                                 How long to wait flushing sample on shutdown or config reload.
      --storage.remote.read-sample-limit=5e7  
                                 Maximum overall number of samples to return via the remote read interface, in a single query. 0 means no limit.
      --storage.remote.read-concurrent-limit=10  
                                 Maximum number of concurrent remote read calls. 0 means no limit.
      --rules.alert.for-outage-tolerance=1h  
                                 Max time to tolerate prometheus outage for restoring 'for' state of alert.
      --rules.alert.for-grace-period=10m  
                                 Minimum duration between alert and restored 'for' state. This is maintained only for alerts with configured 'for' time greater than grace period.
      --rules.alert.resend-delay=1m  
                                 Minimum amount of time to wait before resending an alert to Alertmanager.
      --alertmanager.notification-queue-capacity=10000  
                                 The capacity of the queue for pending Alertmanager notifications.
      --alertmanager.timeout=10s  
                                 Timeout for sending alerts to Alertmanager.
      --query.lookback-delta=5m  The delta difference allowed for retrieving metrics during expression evaluations.
      --query.timeout=2m         Maximum time a query may take before being aborted.
      --query.max-concurrency=20  
                                 Maximum number of queries executed concurrently.
      --query.max-samples=50000000  
                                 Maximum number of samples a single query can load into memory. Note that queries will fail if they would load more samples than this into memory, so this also limits the number of samples a query can return.
      --log.level=info           Only log messages with the given severity or above. One of: [debug, info, warn, error]


cp prometheus.yml prometheus.yml.orig

[root@monitor prometheus]# more prometheus.yml
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']


  - job_name: qgs-prod
    static_configs:
      - targets: ['192.168.1.100:9100']
        labels:
          instance: prod1
      - targets: ['192.168.1.110:9100']
        labels:
          instance: prod2
      - targets: ['192.168.1.101:9100']
        labels:
          instance: master1
      - targets: ['192.168.1.111:9100']
        labels:
          instance: master2
      - targets: ['192.168.1.102:9100']
        labels:
          instance: node1
      - targets: ['192.168.1.112:9100']
        labels:
          instance: node2
      - targets: ['192.168.1.103:9100']
        labels:
          instance: node3
      - targets: ['192.168.1.113:9100']
        labels:
          instance: node4
      - targets: ['192.168.1.104:9100']
        labels:
          instance: harbor
      - targets: ['192.168.1.114:9100']
        labels:
          instance: ceph1
      - targets: ['192.168.1.105:9100']
        labels:
          instance: ceph2
      - targets: ['192.168.1.115:9100']
        labels:
          instance: ceph3

  - job_name: redis
    static_configs:
      - targets: ['192.168.1.118:9121']
        labels:
          instance: redis

  - job_name: mysql
    static_configs:
      - targets: ['192.168.1.118:9104']
        labels:
          instance: mysql

  - job_name: ceph
    static_configs:
      - targets: ['192.168.1.114:9128']
        labels:
          instance: ceph

nohup ./prometheus --config.file=prometheus.yml &

备注：每次配置变更后都需要重启prometheus

ps -ef | grep prometheus
root      2737     1  0 11月13 ?      00:08:14 ./prometheus --config.file=prometheus.yml
root      4096  4021  0 12:00 pts/0    00:00:00 grep --color=auto prometheus
ss -tnlp | grep 2737
LISTEN     0      128         :::9090                    :::*                   users:(("prometheus",pid=2737,fd=7))

访问http://ip:9090可以查看prometheus的具体信息

```
