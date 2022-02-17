
Bboss is a good elasticsearch Java rest client. It operates and accesses elasticsearch in a way similar to mybatis.

# BBoss Environmental requirements

JDK requirement: JDK 1.7+

Elasticsearch version requirements: 1.x,2.X,5.X,6.X,8.x,+

Spring booter 1.x,2.x,+
xxl-job 2.3.0以下版本
# bboss elasticsearch 数据导入工具xx job定时任务调度demo
使用本demo所带的应用程序运行容器环境，可以快速编写、打包发布可运行在分布式定时任务引擎xx job的数据同步任务。

支持的数据库：
mysql,maridb，postgress,oracle ,sqlserver,db2，hive等

支持的Elasticsearch版本：
1.x,2.x,5.x,6.x,7.x,+

支持海量PB级数据同步导入功能

[使用参考文档](https://esdoc.bbossgroups.com/#/db-es-tool)


# 开发作业
## 准备工作
通过gradle构建发布作业，gradle安装配置文档：



gradle安装配置参考文档：

https://esdoc.bbossgroups.com/#/bboss-build

## 实现作业

1. 下载源码工程-基于gradle

https://github.com/bbossgroups/db-elasticsearch-xxjob

从github下载源码工程，然后导入idea或者eclipse

2. 定义同步作业配置逻辑，参考实现类：

org.frameworkset.elasticsearch.imp.jobhandler.XXJobImportTask

任务处理类必须继承抽象类org.frameworkset.elasticsearch.client.schedule.xxjob.AbstractDB2ESXXJobHandler

3. 修改es和数据库配置-db-elasticsearch-xxjob\src\test\resources\application.properties

4. 将定义的任务添加到application.properties中，并修改其中xxjob参数配置：

```properties
# xxjob分布式作业任务配置

### xxl-job admin address list, such as "http://address" or "http://address01,http://address02"
xxl.job.admin.addresses=http://127.0.0.1:8080/xxl-job-admin

### xxl-job executor address
xxl.job.executor.appname=db-elasticsearch-xxjob
xxl.job.executor.ip=192.168.137.1
xxl.job.executor.port=9995

### xxl-job, access token
xxl.job.accessToken=

### xxl-job log path
xxl.job.executor.logpath=d:/xxl-job/
### xxl-job log retention days
xxl.job.executor.logretentiondays=-1
##
# 作业任务配置
# xxl.job.task为前置配置多个数据同步任务，后缀XXJobImportTask和OtherTask将xxjob执行任务的名称
# 作业程序都需要继承抽象类org.frameworkset.elasticsearch.client.schedule.xxjob.AbstractDB2ESXXJobHandler
# 实现抽象方法init即可：
# public void init(){
#     externalScheduler = new ExternalScheduler();
#     externalScheduler.dataStream(()->{
#         DB2ESImportBuilder importBuilder = DB2ESImportBuilder.newInstance();
#              编写导入作业任务配置逻辑，参考文档：https://esdoc.bbossgroups.com/#/db-es-tool
#         return    importBuilder;
#       }
# }
#

xxl.job.task.XXJobImportTask = org.frameworkset.elasticsearch.imp.jobhandler.XXJobImportTask
## xxl.job.task.otherTask = org.frameworkset.elasticsearch.imp.jobhandler.OtherTask

#配置运行xxl-job作业主程序
mainclass=org.frameworkset.elasticsearch.client.schedule.xxjob.XXJobApplication
```



5. 工程已经内置mysql jdbc驱动，如果有依赖的第三方jdbc包（比如oracle驱动），可以将第三方jdbc依赖包放入db-elasticsearch-xxjob\lib目录下

6. 测试和调试导入功能

   运行db-elasticsearch-xxjob\src\test\java目录下面的ApplicationTest测试类即可：

​       src\test\java\org\frameworkset\elasticsearch\imp\ApplicationTest.java 

7. 测试调试通过后，构建发布可运行的版本

   进入命令行模式，切换到源码工程根目录db-elasticsearch-xxjob下，运行以下指令打包发布版本：

​       release.bat

## 运行作业
版本发布后，在build/distributions目录下会生成可以运行的zip包，解压运行导入程序

linux：

chmod +x restart.sh

./restart.sh

windows: restart.bat

作业启动后，可以在xx-job-admin进行任务的配置，并查看运行日志。

## 作业jvm配置
修改jvm.options，设置内存大小和其他jvm参数

-Xms1g

-Xmx1g

 

# 作业参数配置

在代码中获取参数dropIndice方法：

```java
boolean dropIndice = CommonLauncher.getBooleanAttribute("dropIndice",false);//同时指定了默认值false
```

另外可以在resources/application.properties配置控制作业执行的一些参数，例如工作线程数，等待队列数，批处理size等等：

```
queueSize=50
workThreads=10
batchSize=20
```

在作业执行方法中获取并使用上述参数：

```java
int batchSize = CommonLauncher.getIntProperty("batchSize",10);//同时指定了默认值
int queueSize = CommonLauncher.getIntProperty("queueSize",50);//同时指定了默认值
int workThreads = CommonLauncher.getIntProperty("workThreads",10);//同时指定了默认值
importBuilder.setBatchSize(batchSize);
importBuilder.setQueue(queueSize);//设置批量导入线程池等待队列长度
importBuilder.setThreadCount(workThreads);//设置批量导入线程池工作线程数量
```

 

## elasticsearch技术交流群:166471282 

## elasticsearch微信公众号:bbossgroup   
![GitHub Logo](https://static.oschina.net/uploads/space/2017/0617/094201_QhWs_94045.jpg)


