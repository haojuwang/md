## docker 安装

yum install -y docker



#### 启动

Service docker start



#### 设置阿里镜像

```
vim /etc/sysconfig/docker

添加：
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false --registry-mirror=https://ud17re9w.mirror.aliyuncs.com'
```



重启

```
systemctl daemon-reload 
service docker restart
```



#### 实例

```
docker run centos /bin/echo 'lihao'
```



#### 查看容器

```
docker ps -a
```



#### 进入容器

```
 docker run --name mydocker -it centos /bin/bash
```



#### 启动容器后台运行

````
 docker run -d --name mydocker1 centos
````



