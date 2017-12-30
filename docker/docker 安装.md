## docker 安装

yum install -y docker

#### centos6

```
http://icyleaf.com/2016/12/docker-with-centos/
```





#### 错误问题

##### 1

```
docker: relocation error: docker: symbol dm_task_get_info_with_deferred_remove, version Base not defined in file libdevmapper.so.1.02 with link time reference
```

```
yum upgrade device-mapper-libs
```







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



#### 启动容器后台运行

````
 docker run -d --name mydocker1 centos
````



#### 创建容器

```
docker create -it centos:latest

 docker run --name mydocker -it centos /bin/bash
```





#### 进入容器



##### attach

```
docker attach 6c0deb2bc0b3  # id

docker attach pensive_fermat #name

```



##### exec

```
 docker exec -ti 6c0deb2bc0b3 /bin/bash
```



##### nsenter

* 安装

  ```shell
   cd /tmp;
   
   curl https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz | tar -zxf-; cd util-linux-2.24;
   
   ./configure  --without-ncurses
   
   make nsenter && sudo cp nsenter /usr/local/bin
  ```

  ​

* 进入容器

  ```
   PID=$(docker inspect -f {{.State.Pid}} 62a2e1916544)
    nsenter --tar $PID --mount --uts  --ipc --net --pid
  ```

  解释nsenter指令中进程id之后的参数的含义： 

  * –mount参数是进去到mount namespace中 
  * –uts参数是进入到uts namespace中 
  * –ipc参数是进入到System V IPC namaspace中 
  * –net参数是进入到network namespace中 
  * –pid参数是进入到pid namespace中 
  * –user参数是进入到user namespace中























