### kafka服务端句柄数上涨问题定位

1、鹰眼系统检测到的句柄数

 

2、分析当前进程的句柄情况

![img](http://image.huawei.com/tiny-lts/v1/images/1a97cbba62a557c8ebdd1e9b6b39e627_1604x113.png@900-0-90-f.png)
![img](http://image.huawei.com/tiny-lts/v1/images/80a5e82db80abbcb838d6d398af85954_307x53.png@900-0-90-f.png)

3、执行linux命令，下载命令采集结果文件

比对6.18号和6.19号的句柄增长情况，

![img](http://image.huawei.com/tiny-lts/v1/images/d35652bc06b8de0cef4f707b024143b9_1875x699.png@900-0-90-f.png)

同时比对6.19号和6.22号的句柄增长情况

![img](http://image.huawei.com/tiny-lts/v1/images/6325474370293bea65625aac3beb465a_1827x832.png@900-0-90-f.png)

初步定位kafka服务端的句柄个数和topic和分区有关系

4、自己用kafka维护脚本，创建Topic验证下句柄增长情况

![img](http://image.huawei.com/tiny-lts/v1/images/03f81e2d6a8f56e7afba5854d4371372_726x280.png@900-0-90-f.png)

查看当前kafka服务端句柄情况，且一直存在

![img](http://image.huawei.com/tiny-lts/v1/images/46b5542361dd6d29ffafdef3f5653302_961x359.png@900-0-90-f.png)

日志文件老化，句柄也相应的关闭
![img](http://image.huawei.com/tiny-lts/v1/images/a5f8a57adbad388f43864757ce402029_1903x415.png@900-0-90-f.png)

5、在kafka的broker中，每个分区都会对照着文件系统的一个目录。在kafka的数据日志文件目录中，每个日志数据段都会分配两个文件，一个索引文件和一个数据文件。当前版本的kafka，每个broker会为每个日志段文件打开一个index文件句柄和一个数据文件句柄。因此，随着partition的增多，需要底层操作系统配置更高的文件句柄数量限制，并且需要分析业务Topic使用情况和集群配置情况
