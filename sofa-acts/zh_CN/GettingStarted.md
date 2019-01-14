# 快速开始

本文档共分为四部分：

第一部分：指引你如何在Intellij Idea上安装ACTS IDE；

第二部分：向你介绍如何在多模块工程中引入ACTS依赖；

第三部分：test模块下一键搭建ACTS框架管理后续ACTS测试用例；

第四部分：一键生成ACTS测试脚本；

## 1.安装ACTS IDE

__推荐Intellij Idea 2017版本__。本地磁盘安装：Preference->Plugins->Install plugin from disk->Restart Idea

__为了您的安全，请仅从该下载源获取ACTS IDE安装包：__[点击下载ACTS IDE](https://gw.alipayobjects.com/os/basement_prod/c09f3a6a-b49a-4ab9-a56e-4994e033ae7b.zip)。

![qk_1.png](./resources/qk_1.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图1.1</div>
</div>

## 2.引入ACTS依赖
在引入ACTS依赖之前，需要你的应用是一个多模块工程（包含test模块），后续ACTS会将全部的测试代码放置在test模块，便于管理测试用例。
你可以依据应用的具体情况，选择性的阅读以下内容：

应用已经是完整的多模块工程，可参考[文档2.1部分](#21多模块应用（有test模块）)，帮助你引入acts依赖；

应用是多模块工程但无test模块，可参考[文档2.2部分](#22多模块应用（无test模块）)，帮助你快速添加test模块；

应用不是一个多模块工程，可参考[文档2.3部分](#23非多模块应用)，帮助你快速构建多模块工程。

如果还没有创建工程，可参考 [SOFABoot快速开始](http://www.sofastack.tech/sofa-boot/docs/QuickStart) 搭建应用。

### 2.1多模块应用（有test模块）

只需在test模块的pom.xml中引入acts-bom即可。
```
<dependency>
    <groupId>com.alipay.sofa.acts</groupId>
    <artifactId>acts-bom</artifactId>
    <version>${acts.version}</version>
    <type>pom</type>
<dependency>
```


### 2.2多模块应用（无test模块）

这里是使用IDEA来创建子模块。

对着父工程右键 -> New -> Module -> 输入test模块名字（一般是appname-test），分步示例图如下：

#### 第一步：新建test模块

![qk_2.png](./resources/qk_2.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.1</div>
</div>


![qk_3.png](./resources/qk_3.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.2</div>
</div>

![qk_4.png](./resources/qk_4.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.3</div>
</div>


![qk_5.png](./resources/qk_5.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.4</div>
</div>

#### 第二步：管理test模块
在父工程的pom.xml中管理刚刚新建的test模块。

![qk_6.png](./resources/qk_6.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.5</div>
</div>


#### 第三步：依赖ACTS引入
最后，找到刚刚新建的test模块，并在其pom.xml中引入acts-bom即可。
```
<​!-- 引入包含SOFABootApplication的pom -->
<dependency>
    <groupId>com.example</groupId>
    <artifactId>example-service</artifactId>
</dependency>

<!-- 引入ACTS依赖 -->
<dependency>
    <groupId>com.alipay.sofa.acts</groupId>
    <artifactId>acts-bom</artifactId>
    <version>${acts.version}</version>
    <type>pom</type>
<dependency>
```

### 2.3非多模块应用
如果你已经有了一个不错的sofaboot应用，但它不是一个多模块应用，下面的内容将帮助你快速地将现有工程构建为多模块工程。
__注：本部分同样适用于从零创建多模块工程。__

#### 第一步：新建父工程
创建好一个sofaboot工程，然后删除无关的文件，只需保留pom.xml 文件。

![qk_7.png](./resources/qk_7.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.6</div>
</div>

#### 第二步：新建子模块

新建子工程模块，将原有应用作为子工程并入父工程下，相关依赖管理提到父工程中。以新建service模块和test模块为例。

![qk_8.png](./resources/qk_8.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.7</div>
</div>

![qk_9.png](./resources/qk_9.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.8</div>
</div>


![qk_10.png](./resources/qk_10.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.9</div>
</div>

#### 第三步：管理子模块

![qk_11.png](./resources/qk_11.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图2.10</div>
</div>

#### 第四步：依赖引入
最后，在test模块的pom.xml文件中引入acts-bom即可。
```
<dependency>
    <groupId>com.alipay.sofa.acts</groupId>
    <artifactId>acts-bom</artifactId>
    <version>${acts.version}</version>
    <type>pom</type>
<dependency>
```

## 3.一键初始化ACTS测试框架
下面只需要你轻轻动动手指即可完成初始化工作。在图3.2中，你需要正确填写应用名称并选择适合应用的编码格式。有关一键初始化做了什么，生成的文件有何作用，可以参考ACTS使用手册的__框架准备__。

![qk_12.png](./resources/qk_12.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图3.1</div>
</div>

![qk_13.png](./resources/qk_13.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图3.2</div>
</div>

## 4.一键生成测试脚本

### 4.1启动类
将service模块中的启动类，如SOFABootApplication拷贝到test模块，并增加需要加载的配置文件：
```
classpath*:META-INF/spring/acts-core.xml
```

![qk_14.png](./resources/qk_14.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图4.1</div>
</div>

### 4.2测试脚本

前提条件：__务必事先生成对象模型，即方法的入参、返回结果等模型生成，否则会造成ACTS IDE不可预料的错误，如无法编辑、编辑数据不正确等。__

接口定义的方法上点击，选择ACTS功能->生成测试用例。

![qk_15.png](./resources/qk_15.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图4.2</div>
</div>


![qk_16.png](./resources/qk_16.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图4.3</div>
</div>


![qk_17.png](./resources/qk_17.png)
<div data-type="alignment" data-value="center" style="text-align:center">
  <div data-type="p">图4.4</div>
</div>

感谢您的关注！如有任何问题，您可以通过github issue向我们留言反馈。
