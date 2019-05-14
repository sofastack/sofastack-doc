通过 SOFABoot，我们可以直接在浏览器中就可以查看 SOFA 中间件的版本等详细信息。

## 引入 SOFABoot Infra 依赖

要在 SOFABoot 中直接通过浏览器查看 SOFA 中间件的版本信息，只需要在 Maven 依赖中增加如下的内容即可：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>infra-sofa-boot-starter</artifactId>
</dependency>
```

## 版本信息查看

在应用启动成功后，可以在浏览器中输入 [http://localhost:8080/sofaboot/versions](http://localhost:8080/sofaboot/versions) 查看 SOFA 中间件的版本信息，如：

```json
[
  {
    GroupId: "com.alipay.sofa",
    Doc-Url: "https://github.com/sofastack/sofa-boot",
    ArtifactId: "infra-sofa-boot-starter",
    Built-Time: "2018-04-05T20:55:22+0800",
    Commit-Time: "2018-04-05T20:54:26+0800",
    Commit-Id: "049bf890bb468aafe6a3e07b77df45c831076996",
    Version: "2.4.0"
  }
]
```
**注: 在 SOFABoot 3.x 中调整了 endpoint 路径，sofaboot/versions 更改为 actuator/versions**