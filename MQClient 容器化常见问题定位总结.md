### MQClient 容器化常见问题定位总结

1、出现下面这种报错

![img](http://image.huawei.com/tiny-lts/v1/images/6a8b17df7c2741adb6cef11cc80cd702_1367x268.png@900-0-90-f.png)
 一般由于Chart包中没有引用mqclient的rtsp，引用方式如下：先在FST上上传mqclient的rtsp镜像包

![img](http://image.huawei.com/tiny-lts/v1/images/9b1b1027ab73f62b26fbb800464c567d_486x279.png@900-0-90-f.png)
2、出现下面报错

![img](http://image.huawei.com/tiny-lts/v1/images/5e47af721169c9f5ec35bef964b9f1a8_1651x104.png@900-0-90-f.png)
由于Chart包中没有配置UDS，或者UDS没生效（找总线定位），导致URL重定向了，配置如下：

![img](http://image.huawei.com/tiny-lts/v1/images/7e46fbbe16e71ef666df86e9422fb1ed_551x411.png@900-0-90-f.png)

查询Appconf去确认UDS是否生效，进自己微服务POD下面，在/opt/sidecar/ir路径下，调curl查询是否生效

![img](http://image.huawei.com/tiny-lts/v1/images/c4351482e1eef04fa680ab3717518dfa_676x18.png@900-0-90-f.png)

正常的UDS数据返回：

![img](http://image.huawei.com/tiny-lts/v1/images/53a81fd785d11a4d99ae999af6b490ad_874x559.png@900-0-90-f.png)

3、appconf中确认能否查询到自己声明的Topic、集群等CR数据信息

![img](http://image.huawei.com/tiny-lts/v1/images/6c7b5a7d31a07bb61289d8f2e92765f3_832x692.png@900-0-90-f.png)
如果没有，首先看集群pod是否运行正常

![img](http://image.huawei.com/tiny-lts/v1/images/be6cfacd2ee8ef2ce8c2dfa17cd1a5b4_837x81.png@900-0-90-f.png)
然后，看下自己声明的topic、集群CR是否格式正确，均正常，然后咨询nodeagent同事或者MQ的同事数据生成问题

4、获取证书失败

![img](http://image.huawei.com/tiny-lts/v1/images/36ea7b94c71118d262a2efef46378b0a_1287x141.png@900-0-90-f.png)

首先看下业务侧声明的证书CR是否生成，查询证书接口是否正常；在业务pod部署节点下，查询secretbox的pod名称，进对应的secretbox的pod，在data/certifact目录下，看是否有自己的证书生成，没有生成找secretbox同事定位，然后执行curl接口查询证书内容是否正常返回，在业务Pod目录/opt/sidecar/ir下，调用curl :

curl -i --unix-socket http.sock -H 'xx-from-hiro: {"identity":"secretbox"}' -H "xx-local-forward:true" http://localhost/rest/plat/secretbox/v1/manager/secrets/mqcenter.cert     

把变量1、2改为自己的namespace和证书名

![img](http://image.huawei.com/tiny-lts/v1/images/9c98166b16ab57dc96029216b94f525c_1225x103.png@900-0-90-f.png)
![img](http://image.huawei.com/tiny-lts/v1/images/2c2f25c950cb2b774cc92314c1ed4820_1546x321.png@900-0-90-f.png)

5、接口报错，通信问题，排查自己Chart包中是否配置路由

![img](http://image.huawei.com/tiny-lts/v1/images/d4e9f78a68aeef19c0613f01d50fd3d6_688x196.png@900-0-90-f.png)

6、rest通信问题，排查框架包从虚拟机版本切换到容器化版本

![img](http://image.huawei.com/tiny-lts/v1/images/66324304ec373acbc8e4eb6da661ce22_366x44.png@900-0-90-f.png)

引用方式：

![img](http://image.huawei.com/tiny-lts/v1/images/a24cb37d0d18470d5cbd2a1e983526ef_602x360.png@900-0-90-f.png)

7、流水线构建报错，业务面mqclient已经支持按照红箭头的方式引用，maven引用方式后面会删除（不推荐）

​           排查构建脚本或者相关构建同事，清除codecheck缓存、编译缓存等                                     

​      ![img](http://image.huawei.com/tiny-lts/v1/images/7039732b80688d2ef97befba24e11297_1366x213.png@900-0-90-f.png)

​    