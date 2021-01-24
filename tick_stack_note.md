

## Install telegraf
```
docker run -d --name=telegraf \
      --net tick-net \
      -v /data/telegraf/telegraf.conf:/etc/telegraf/telegraf.conf \
      telegraf
```

## Install influxdb
```
docker run -p 0.0.0.0:8086:8086 --name=influxdb-test1 \
      --net tick-net \
      -v /data/influxdb/influxdb.conf:/etc/influxdb/influxdb.conf \
      -v /etc/localtime:/etc/localtime:ro \
      -v /etc/timezone:/etc/timezone:ro \
      influxdb
```
### Fix error timezone
```
export TZ=Asia/Ho_Chi_Minh
```

## Install chronograf
```
docker run -p 0.0.0.0:8888:8888 --name=chronograf \
      --net tick-net \
      -v /data/chronograf:/var/lib/chronograf \
      chronograf
```

## Install kapacitor
```
docker run -p 0.0.0.0:9092:9092 --name=kapacitor \
      --net tick-net \
      -v /data/kapacitor/kapacitor.conf:/etc/kapacitor/kapacitor.conf \
      kapacitor
```

#### !uery data influxdb
```
curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=telegraf" --data-urlencode "q=SELECT \"usage_user\" FROM \"cpu\""

localhost:8086/query?pretty=true&db=telegraf&q=SELECT  "usage_user" FROM  "cpu" where time > now() - 2m tz('Asia/Ho_Chi_Minh')

localhost:8086/query?pretty=true&db=telegraf&q=SELECT mean( "usage_user") FROM  "cpu" where time > now() - 15m GROUP BY time(2500ms) tz('Asia/Ho_Chi_Minh') 

```

## Install prometheus
```
docker run -d -p 0.0.0.0:9090:9090 prom/prometheus
```

## Install grafana
```
docker run -d --name=grafana --net tick-net -p 3000:3000 grafana/grafana
```

## Jenkins node path
```
/var/jenkins_home/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/node/bin/node /var/jenkins_home/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/node/bin/npm install
```

## Install reporter
```
http://localhost:3000/d/hwJPm7-Wz/apache-overview?orgId=1&var-datasource=InfluxDB&var-host=8aa02fe27b46&from=1575255341172&to=1575258941172
```

## iInstall haproxy
```
docker run -d --name my-haproxy --net tick-net -v /data/haproxy/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro haproxy:1.7
```













