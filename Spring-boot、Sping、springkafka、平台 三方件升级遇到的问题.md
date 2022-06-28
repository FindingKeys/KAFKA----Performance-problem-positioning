### Spring-boot、Sping、springkafka、平台 三方件升级遇到的问题

由于三方件安全漏洞，需要升级Spring三方件版本

升级Spring三方件版本，从5.1.13 升级到5.2.9，升级完之后，重启VNFMSchedulingService微服务的 tomcat的localhost.log 中初始化spring对象报错

![img](http://image.huawei.com/tiny-lts/v1/images/7a678272c67b91a472e7_1590x165.png@900-0-90-f.png)
平台版本依赖旧的spring版本，需要升级平台版本

平台版本升级完成后，LCM中心侧启动没有问题，边缘侧MEC重启报错

![img](http://image.huawei.com/tiny-lts/v1/images/f214e272c68214c561bb_1613x487.png@900-0-90-f.png)
![img](http://image.huawei.com/tiny-lts/v1/images/11422272c68355b45324_1612x190.png@900-0-90-f.png)

spring-boot版本不兼容，需要升级，spring-boot-autoconfigure   2.2.5.RELEASE到2.2.10.RELEASE

升级完spring-boot之后，边缘侧重启依然有问题，报错如下：

![img](http://image.huawei.com/tiny-lts/v1/images/3b936272c6892ab14c89_1612x333.png@900-0-90-f.png)

通过查找 spring-boot版本和spring-kafka版本是强依赖的，<https://spring.io/projects/spring-kafka#overview>

![img](http://image.huawei.com/tiny-lts/v1/images/8b272272c68b76e8d3c3_924x549.png@900-0-90-f.png)

现在环境上spring-kafka版本太低，升级spring-kafka版本，替换掉环境上就版本的jar包，在进行重启，重启正常

本次spring三方件升级，不能只升级spring，spring-boot、spring-kafka、平台版本都要进行相应的升级