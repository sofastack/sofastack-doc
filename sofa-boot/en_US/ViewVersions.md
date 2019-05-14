With SOFABoot, we can directly view the version of SOFA middleware and other detailed information in the browser.

## Introducing SOFABoot Infra Dependency

To view the version information of the SOFA middleware directly in the browser in SOFABoot, all you need to do is add the following to the Maven dependency:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>infra-sofa-boot-starter</artifactId>
</dependency>
```

## Version Information Viewing

After an application started successfully, you can visit [http://localhost:8080/sofaboot/versions](http://localhost:8080/sofaboot/versions) in the browser to view the version information of the SOFA middleware, the response such as:

```json
[
  {
    GroupId: "com.alipay.sofa",
    Doc-Url: "https://github.com/sofastack/sofa-boot",
    ArtifactId: "infra-sofa-boot-starter",
    Build-Time: "2018-04-05T20:55:22+0800",
    Commit-Time: "2018-04-05T20:54:26+0800",
    Commit-Id: "049bf890bb468aafe6a3e07b77df45c831076996",
    Version: "2.4.0"
  }
]
```
** Note: In SOFABoot 3.x, the endpoint path has been changed from sofaboot/versions to actuator/versions**.
