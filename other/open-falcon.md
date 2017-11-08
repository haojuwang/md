## Open-Falcon 

* Agent:部署在目标机器上采集机器监控项。
* Transfer: 数据接收端，转发数据到后端Graph和Judge。
* Graph:  操作rrd文件存储监控数据。
* Query：查询各个Graph数据，提供统一http查询接口
* Dashboard: 查询监控历史趋势图的web端
* Task:负责一些定时任务，索引全量更新，垃圾索引清理，自身监控等。



![func_intro_1](../images/func_intro_1.png)





