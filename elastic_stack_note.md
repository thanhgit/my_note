# Install elasticsearch
```
sudo docker run -d --name elasticsearch \
 --net elastic-net -p 0.0.0.0:9200:9200 -p 0.0.0.0:9300:9300 \
 -v /data/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
 -v /data/elasticsearch/data:/usr/share/elasticsearch/data \
 -e "discovery.type=single-node" \
 docker.elastic.co/elasticsearch/elasticsearch:7.1.1
 ```


# Install kibana
```
sudo docker run -d --name kibana \
 --net elastic-net \
 -v /data/kibana/data:/usr/share/kibana/data \
 -v /data/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml \
 -p 0.0.0.0:5601:5601 \
 docker.elastic.co/kibana/kibana:7.1.1
```

# Install logstash
```
sudo docker run -d --name logstash \
 --net elastic-net -p 0.0.0.0:5044:5044 \
 -v /data/logstash/pipeline/:/usr/share/logstash/pipeline/ \
docker.elastic.co/logstash/logstash:7.1.1
```

