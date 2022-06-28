### 容器化MQ监控指标注册指南

1）kubectl exec -it datacornerstoneservice-6fdfc59f5-6zsq4 -n manager -- /bin/bash 

2）cd /opt/sidecar/ir

3) curl --unix-socket http.sock '[http://localhost/rest/nodeagent/v1/pub-files?type=beidou'](http://localhost/rest/nodeagent/v1/pub-files?type=beidou) 

有下面数据，则继续![img](http://image.huawei.com/tiny-lts/v1/images/572464913d484ad82c810886c6ef34f1_1180x109.png@900-0-90-f.png)

4）查数据库账户和密码

curl -X GET -i --unix-socket http.sock -H 'xx-local-forward:true' <http://localhost/rest/plat/secretbox/v1/manager/secrets/zenith.managedbsvr.cornerstonedb.credential> 

5）进入数据库，查询声明的监控指标等表格

【1】kubectl exec -it managedbsvr-0 -n manager /bin/bash 

【2】查找数据库ip值：netstat -anp |grep 32080 

【3】连接数据库，将数据库账户和密码、ip替换掉，导环境变量

export GSDB_HOME=/opt/pkgs/app/zenith

export LD_LIBRARY_PATH=${GSDB_HOME}/add-ons:${GSDB_HOME}/lib:$LD_LIBRARY_PATH

export PATH=${GSDB_HOME}/bin:$PATH

/opt/pkgs/app/zenith/app/bin/zsql cornerstonedb/******@172.19.0.9:32080【4】查询 表t_package，表记录状态码为3，则监控声明注册成功
 ![img](http://image.huawei.com/tiny-lts/v1/images/48390f3c6a098a8a884aa22485c47cd2_792x260.png@900-0-90-f.png)