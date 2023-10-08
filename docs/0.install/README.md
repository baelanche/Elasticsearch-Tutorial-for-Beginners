# java

설치

```
sudo apt-get update
sudo apt install default-jre
sudo apt install default-jdk
```

버전 확인

```
java -version
```

# curl

설치

```
sudo apt install curl
```

# elasticsearch

설치

```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update
sudo apt install elasticsearch
```

실행

```
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
curl -X GET localhost:9200
```

# 트러블슈팅

### curl: (52) Empty reply from server

```
sudo vi /etc/elasticsearch/elasticsearch.yml
```

```
# Enable security features
xpack.security.enabled: false

xpack.security.enrollment.enabled: false

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
# Create a new cluster with the current node only
# Additional nodes can still join the cluster later
cluster.initial_master_nodes: ["USER"]
```

true 로 되어있는 4개 부분을 false 로 바꾼다.
