#### dashboards 安装

```
curl -L -O http://download.elastic.co/beats/dashboards/beats-dashboards-1.3.1.zip
unzip beats-dashboards-1.3.1.zip
cd beats-dashboards-1.3.1/
./load.sh -url "211.167.232.2:9200" -user "elastic:changeme"

```
#### topbeat 安装

```
curl -L -O https://download.elastic.co/beats/topbeat/topbeat-1.3.1-x86_64.rpm
rpm -vi topbeat-1.3.1-x86_64.rpm


```
1,修改 /etc/topbeat/topbeat.yml
43行 211.167.232.2:9200

47行 48行 55行

2，执行curl
```
	curl -XPUT 'http://211.167.232.2:9200/_template/topbeat' -u -d@/etc/topbeat/topbeat.template.json
```
3,开启 
	/etc/init.d/topbeat start