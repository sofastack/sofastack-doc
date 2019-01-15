# 框架准备

## 编码说明

请确保 ACTS 的编码与系统代码的编码一致，即确定以下的编码保持一致：生成脚本选择的编码、workspace 的编码应该都与应用代码编码保持一致，不一致时会出现乱码问题。

生成脚本选择的编码，如下图设置：

![us_1](./resources/us_1.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图1</div>
</div>

Idea workspace 的编码

![us_2](./resources/us_2.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2</div>
</div>

## 数据源配置

ACTS 配置数据源的目的，是为了在数据准备、数据清理、数据校验阶段，能够使用系统的数据源正确的进行 DB 增删改查。
#### 数据源配置 

在 `src/test/resource/config/acts-config.properties` 中配置 dal 层的 ModuleName、数据源以及表的对应关系，以 ds_ 开头，如下：
```plain
datasource_bundle_name =com.alipay.testapp.common.dal
ds_数据源bean1=逻辑表名1,逻辑表名2
ds_数据源bean2=逻辑表名3,逻辑表名4
```
其中数据源 bean1、数据源 bean2 是应用代码中 dal 层的数据源 bean 的名称，支持多个数据源。表名支持正则表达式，无需带分库分表后缀，若有多个数据源时请注意，某张表只能属于一个数据源，如下图：

![us_3](./resources/us_3.png)
<div data-type="alignment" data-value="center" style="text-align:center">
    <div data-type="p">图3</div>
</div>

#### 数据库直连
数据库直连，用于 DB 数据模型的生成。在 `src/test/resource/config/dbConf/` 下的 devdb.conf 或 testdb.conf 中配置如下：
```plain
xxx_url = jdbc:oracle:thin:@localhost:1521:cifdb
xxx_username = myname
xxx_password = mypswd
```
    
## 一键配置的说明
一键配置测试框架主要生成包含两部分，一部分是基础 Java 类，另一类是必须的配置文件，具体生成内容如下：
#### Java 类
 + AppNameActsBaseUtils.java
 
    测试脚本编写过程中常用的从框架中获取各种数据的工具类，初始化搭建只提供了常用的方法，可自行添加。

 + AppNameActsTestBase.java
 
    封装后的应用测试基类，业务系统如有特殊需求可在其上自行封装，如果没有则可以忽略此文件。

#### 配置文件

![us_2](./resources/ur_1.png)
<div data-type="alignment" data-value="center" style="text-align:center">
    <div data-type="p">图4</div>
</div>