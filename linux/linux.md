## 常用命令

#### 同步时间

```shell
rdate -s time-b.nist.gov
```



#### 加大服务器文件描述符

在unix/linux里面，你的服务只要开启一个进程，就要占用文件的描述符。linux 默认是1024，如果描述符少了，你的访问量多了，你的服务器支撑不了，所以要把描述符加大。

```
echo '* - nofile 65535 ' >>/etc/security/limits.conf
ulimit -n //查看当前文件描述符数量
```





#### find

```
Linux find命令用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。
```

* 将目前目录及其子目录下所有延伸档名是 sh的文件列出来。

```
find . -name "*.sh"
```

* 将目前目录及其子目录下所有最近 3天内更新过的文件列出

```
find /Users/lihao/Downloads/ -mtime -3 -type f
```

![linux-find](/Users/lihao/Desktop/md/images/linux-find.jpeg)





* 删除最近3天修改的文件

  ```
  find . -mtime -3 -type f | xargs rm -rf
  ```

  ​



















