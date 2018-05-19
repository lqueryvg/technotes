# InfluxDB

## CLI

```
influx -precision rfc3339
show databases
use telegraf
show series
show series from cpu
show measurements
show tag keys from cpu
show field keys from cpu


SELECT * FROM k8s_deployments ORDER BY time DESC LIMIT 10
SELECT * FROM k8s_deployments ORDER BY time DESC WHERE time > now() - 1m
SELECT mean("gauge") FROM "kube_deployment_status_replicas" WHERE "env" = "dev1" 

drop series from mem where host='dc35f399feef'

select field,tag from measurement ORDER BY time DESC LIMIT 10
select usage_user,cpu from cpu ORDER BY time DESC LIMIT 10

curl -G 'http://localhost:8086/query?pretty=false' --data-urlencode "db=telegraf" \
  --data-urlencode "q=SELECT * FROM k8s_deployments ORDER BY time DESC LIMIT 10"

curl -G 'http://localhost:8086/query?pretty=false' --data-urlencode "db=telegraf"   \
  --data-urlencode "q=SELECT unavailable_replicas FROM k8s_deployments where time > '2017-07-14T19:00:09.000Z' and time < '2017-07-14T19:00:54.000Z' ORDER BY time DESC LIMIT 10"

curl -G 'http://localhost:8086/query?pretty=false' --data-urlencode "db=telegraf"   \
  --data-urlencode "q=SELECT unavailable_replicas FROM k8s_deployments where time > '2017-07-14T19:00:09.000Z' and time < '2017-07-14T19:00:54.000Z' ORDER BY time DESC LIMIT 10"
```

# Tick notes

```
kapacitor stats general
kapacitor stats ingress
kapacitor define {name} -type batch -tick {script} -dbrp telegraf.autogen
kapacitor define {task-name} -type stream \
  -tick {task-name}.tick -dbrp telegraf.autogen
kapacitor enable {name} 
kapacitor list tasks
kapacitor show {task-name}
kapacitor delete tasks {task-name}
kapacitor -url http://blah:9092 {command}

alias influx="docker exec -it {container} influx -precision rfc3339 -database telegraf"
influx -execute 'show measurements'
SELECT mean(total) AS "total", mean(used) as "used" FROM "disk"
  WHERE "host" =~ /$server$/ AND "path" =~ /^$mountpoint$/ AND
  $timeFilter GROUP BY time($interval), "host", "path"
DROP SERIES FROM cpu where host = 'fred'

> SELECT gauge, image FROM "telegraf"."autogen"."kube_pod_container_info" WHERE time > now() - 14d AND "env"='sit' AND "image" =~ /vshare/ group by image limit 1

```

# Telegraf
See https://github.com/influxdata/telegraf/blob/master/docs/CONFIGURATION.md
```
telegraf config    # display default telegraf config file

# Run a single telegraf collection, outputing metrics to stdout:
telegraf --config /etc/telegraf/telegraf.conf --test

# Exclude specific tags from a point (for an input plugin):
tagexclude = ["fstype"]
```

# Quotes

- Wrap double quotes around identifiers (e.g. measurement names, tag names, key names),
  to prevent clashes with keywords.
- Wrap single quotes around string literals.

Example:
```
DROP SERIES FROM "telegraf"."autogen"."cpu" WHERE cpu = 'cpu8'
```
