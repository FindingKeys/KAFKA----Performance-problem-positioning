### kafka服务端手动停掉，业务进程CPU高问题定位

1、手动停掉Kafka服务端
su - ossadm -c ". /opt/oss/manager/bin/engr_profile.sh; ipmc_adm -cmd startapp -app *****";

2、查看top信息

![img](http://image.huawei.com/tiny-lts/v1/images/c39eb285815c3b9974e8a00b26ef0e46_810x517.png@900-0-90-f.png)

3、查看具体进程pid 250591 下线程cpu使用情况

![img](http://image.huawei.com/tiny-lts/v1/images/8c5659c5d9a7bb1cb47b8130090df3c3_769x620.png@900-0-90-f.png)

4、使用arthas工具，导出进程250591 火焰图

![img](http://image.huawei.com/tiny-lts/v1/images/e8fbcf0a8736cfcf7de0273910bb203a_1081x286.png@900-0-90-f.png)

5、下载采集结果，打开svg文件

![img](http://image.huawei.com/tiny-lts/v1/images/acd034d29e98665e6b403236e1965455_1796x364.png@900-0-90-f.png)

6、

客户端使用KafkaConsumer.poll() 方法时，kafka源码启动心跳线程startHeartbeatThreadIfNeeded();

客户端使用KafkaConsumer.close() 方法时，关掉心跳线程，org.apache.kafka.clients.consumer.internals.AbstractCoordinator#close(org.apache.kafka.common.utils.Timer)