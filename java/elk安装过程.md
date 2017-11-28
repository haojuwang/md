elk安装过程
http://admin.ln12320.cn/9200/
http://admin.ln12320.cn/5601/


wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.0.tar.gz

3:在CentOS上安装ES 
(1)解压,然后拷贝到你要放置的位置
 (2)ES在linux上不能用root启动,创建ES的用户和组:
groupadd es
useradd es -g es -p es 
(3)把ES安装的文件夹的所属用户和组修改为上面创建的用户和组:
chown -R es:es elasticsearch-5.5.0 
(4)切换用户到es,然后就可以启动ES了: su es 
(5)如果想要外部能访问,需要修改es绑定的network.host地址,想要后台运行,
￼可以用-d

## elasticsearch 安装遇到的坑


http://blog.csdn.net/xiegh2014/article/details/53771086
http://blog.csdn.net/liangzhao_jay/article/details/56840941

## java 升级
#### 卸载
```
[root@linux ~]# rpm -qa | grep jdk
[root@linux ~]# rpm -qa | grep gcj
libgcj-4.1.2-42.el5
java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
上面先确认jdk的具体版本号，然后使用rpm -e --nodeps命令删除上面查找的内容：

#rpm -e --nodeps java-1.4.2-gcj-compat-1.4.2.0-40jpp.115

```

#### 升级
```
wget --no-check-certificate --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie;" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz

### java 配置
wget --no-check-certificate --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie;" http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.tar.gz


新建/usr/java文件夹，将jdk-8u25-linux-i586.tar.gz放到该文件夹中，并将工作目录切换到/usr/java目录下。
执行命令tar -zxvf jdk-8u25-linux-i586.gz 进行解压，解压后发现/usr/java多了一个jdk1.8.0_25文件夹。

/usr/java/jdk1.8.0_144

```

sysctl -w vm.max_map_count=262144

JAVA_HOME=/usr/java/jdk1.8.0_144
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
export PATH JAVA_HOME CLASSPATH
-------------------------------------------------------------

##etc/profile
JAVA_HOME=/usr/java/jdk1.8.0_151
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
export PATH JAVA_HOME CLASSPATH




## ik
```
DELETE _template/rtf


PUT _template/rtf
{
  "template":   "*", 
  "settings": { "number_of_shards": 1 }, 
  "mappings": {
    "_default_": {
      "_all": { 
        "enabled": true
      },
      "dynamic_templates": [
        {
          "strings": { 
            "match_mapping_type": "string",
            "mapping": {
              "type": "text",
              "analyzer":"ik_max_word",
              "ignore_above": 256,
              "fields": {
                "keyword": {
                  "type":  "keyword"
                }
              }
            }
          }
        }
      ]
    }
  }
}
```


## logstash
wget https://download.elastic.co/logstash/logstash/logstash-2.4.1.tar.gz



output {


 elasticsearch {
        hosts => ["10.10.10.1:9200"]
        index => "%{type}-%{+YYYY.MM.dd}"
        document_type => "%{type}"
        user => "elastic"
        password => "changeme"
    }
      
}

/data/web/cloud_ins/log
output {

  elasticsearch {
        hosts => ["211.167.232.2:9200"]
          index => "%{type}-%{+YYYY.MM.dd}"
          document_type => "%{type}"
          user => "elastic"
          password => "leyue100"
    }
}


  mutate {
        add_field => {
          "alias" => "yunac2"
        }
      }


https://www.elastic.co/guide/en/logstash/5.6/running-logstash.html#running-logstash-upstart

1, wget https://artifacts.elastic.co/downloads/logstash/logstash-5.6.3.rpm
2, rpm -ivh logstash-5.6.3.rpm
3， cd /etc/logstash
4,conf.d 下面写logstash
5，initctl start logstash

---------------------------------------------------

rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

echo '[logstash-5.x]
name=Elastic repository for 5.x packages
baseurl=https://artifacts.elastic.co/packages/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md' >/etc/yum.repos.d/logstash.repo

yum -y install logstash

yum -y install java-1.8.0-openjdk*











验证密码：head
http://www.ywnds.com/?p=9776
http://localhost:9100/?auth_user=elastic&auth_password=changeme
http://admin.ln12320.cn/9200/?auth_user=elastic&auth_password=changeme

## 修改 elseach 密码
curl -XPUT -u elastic 'http://admin.ln12320.cn/9200/_xpack/security/user/elastic/_password' -d '{"password" : "leyue100@!"}'

http://admin.ln12320.cn/9200/?auth_user=elastic&auth_password=leyue100@!



http://elastic:leyue100@!@admin.ln12320.cn/9200/




## kibana
 ./kibana-plugin install x-pack

 wget https://artifacts.elastic.co/downloads/kibana/kibana-5.5.0-linux-x86_64.tar.gz

修改密码
curl -XPUT -u elastic 'http://admin.ln12320.cn/9200/_xpack/security/user/kibana/_password' -d '{"password" : "leyue100@!"}'

关闭
fuser -n tcp 5601


## x-pack 更新证书
http://blog.csdn.net/u011587666/article/details/54755662

curl -XPUT -u elastic:leyue100 'http://10.10.10.1:9200/_xpack/license?acknowledge=true' -d @li-hao-9f3b1787-dfec-48b5-93f4-2e89b5fb27d1-v5.json

systemctl start logstash.service
