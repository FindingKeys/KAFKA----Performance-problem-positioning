### 微服务间REST通信限制配置

1、基于Tomcat机制的配置：一般超时时间和 maxBodySize 同步修改

![img](http://image.huawei.com/tiny-lts/v1/images/428c82752d9a0b20df80_1035x188.png@900-0-90-f.png)

2、微服务间REST通信，依赖平台封装，底层基于JETTY实现Socket通信

  （1）同步接口 （涉及响应Body体大小的配置，和请求body体大小的配置） 不设置的话，响应body体大小和请求body体大小默认2M，超过2M报错

​            设置响应body体大小，调用平台Rest接口前，初始化RestfulOptions的属性值maxBodySize,最大支持2G    

​      ![img](http://image.huawei.com/tiny-lts/v1/images/ad1c52752da48a4029a1_1459x760.png@900-0-90-f.png)

​           设置请求body体的大小：两种方式；一种是修改平台配置，全局修改；二种是修改本地微服务的特定接口，局部修改

​             1）平台同一方式修改：修改HIROIRService下的startup脚本，增加配置export approveMaxClientBodySize =true，最大支持2G，重启平台微服务生效

![img](http://image.huawei.com/tiny-lts/v1/images/3411f2752da9f4c1a860_874x151.png@900-0-90-f.png)

  ![img](http://image.huawei.com/tiny-lts/v1/images/4e8d72752daa5ff03bd2_639x170.png@900-0-90-f.png)

​            2）局部修改本地微服务的接口

​    ![img](http://image.huawei.com/tiny-lts/v1/images/939712752dad7015bc3b_1166x346.png@900-0-90-f.png)
          具体例子：修改后重新推微服务即可生效，请求body体修改为4M上限

​    ![img](http://image.huawei.com/tiny-lts/v1/images/67b2c2752dae6a013ede_990x209.png@900-0-90-f.png)

   

（2）异步接口，自定义回调实现类

​    ![img](http://image.huawei.com/tiny-lts/v1/images/47e3d2752db16ed60e7a_1278x284.png@900-0-90-f.png)

​      请求Body体大，需要进行切片

   ![img](http://image.huawei.com/tiny-lts/v1/images/11c992752db2ffeb5115_387x21.png@900-0-90-f.png)

3、涉及数据库的大容量接口插入和更新会报错：Sql has too many bind parameters，错误原因：sql绑定变量过多，超过了最大值。大于高斯限定的32768；

涉及数据库的rest接口，如果绑定数据过多，还是需要分批插入或更新

![img](http://image.huawei.com/tiny-lts/v1/images/914a02752db81917f75b_1820x275.png@900-0-90-f.png)

![img](http://image.huawei.com/tiny-lts/v1/images/607252752db4a45da0a3_1826x358.png@900-0-90-f.png)