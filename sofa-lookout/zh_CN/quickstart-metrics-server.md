# Lookout 服务端快速开始

执行 all-in-one-bootstrap 编译后的 fat-jar 包（文章后面说明如何获得）：

```
java -Dcom.alipay.sofa.ark.master.biz=lookoutall \
-Dlookoutall.config-file=abc.properties \
-jar lookout-all-in-one-bootstrap-1.6.0-executable-ark.jar
```
- 注意 `-Dcom.alipay.sofa.ark.master.biz=lookoutall` 是必须的, 用于设置 sofa-ark 的master biz.  
- "-Dlookoutall.config-file" 目前只能引用文件系统上的 properties 文件(没有像 spring-boot 支持那么丰富), 配置项必须以应用名开头, 从而提供隔离能力.
在fat-jar同目录下创建一个`abc.properties`配置文件, 用于存放存放配置文件(下面列出了必须的配置项，用于指向使用的 ES 服务地址):

```properties
gateway.metrics.exporter.es.host=localhost
gateway.metrics.exporter.es.port=9200
metrics-server.spring.data.jest.uri=http://localhost:9200
```


## 如何获得  all-in-one-bootstrap 编译后的 fat-jar

- 方式1：编译
```
./boot/all-in-one-bootstrap/build.sh
```
