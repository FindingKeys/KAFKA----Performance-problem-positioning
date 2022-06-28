### kafka服务端频繁FullGC问题定位

1、安装JDK，参考博客 

http://3ms.huawei.com/km/blogs/details/9142213

2、查看微服务JVM参数配置

![img](http://image.huawei.com/tiny-lts/v1/images/ba0a57267b293d014e0671c26c4e55ef_1603x107.png@900-0-90-f.png)

3、jps命令查看当前微服务进程号 

​     -m: 虚拟机进程启动时传递给主类main()函数的参数
     -l: 进程执行的JAR包路径
     -v: 虚拟机进程启动时JVM参数

4、查看GC情况

 ![img](http://image.huawei.com/tiny-lts/v1/images/c9dca494dd649bc9d9074f35122c2727_1134x309.png@900-0-90-f.png)

   

发现FGC一直上涨，FullGC频繁，比对OC和OU，发现老年代一直GC，但是一直回收不掉，怀疑有大对象或者内存泄漏

5、查看内存分布情况，执行命令 ，导出堆文件

  sudo -u ossuser ./jmap -dump:format=b,file=70803.phrof 70803

6、MAT内存分析工具，查看对象分布

![img](http://image.huawei.com/tiny-lts/v1/images/936ee2a36ac16c76b3386b2c163ecd3f_1039x373.png@900-0-90-f.png)

![img](http://image.huawei.com/tiny-lts/v1/images/815d2c5ae92f4ce7fce6799a54f0c792_1046x304.png@900-0-90-f.png)

发现服务端日志清除线程有占用超500M的大对象，被直接分配到老年代了

7、分析kafka源码，发现线程有常驻大对象存在

![img](http://image.huawei.com/tiny-lts/v1/images/396ea59e7554abb1c30d7c87402f2922_1201x289.png@900-0-90-f.png)

发现这个默认配置不符合当前的JVM堆分配场景，总共1G,-Xmx1G -Xms1G，占用了一半以上

![img](http://image.huawei.com/tiny-lts/v1/images/1b983fa2f105be1ec81c2729a7039704_916x115.png@900-0-90-f.png)
修改kafka服务端 server.properties文件该配置项，重启微服务进行猜想验证

8、持续监测一段时间，观察FullGC情况，完成验证

![img](http://image.huawei.com/tiny-lts/v1/images/90baae37d06f434f7a1a9aea353785d3_1156x228.png@900-0-90-f.png)