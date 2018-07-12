## 常见问题

### 使用类
#### Q: 为什么使用 SNAPSHOT 版本拉取不到依赖？
如果需要使用处于研发状态的 SNAPSHOT 版本，有两种方式：1、拉取 Jarslink 仓库代码，本地执行 `mvn install`。2、在本地 maven setting.xml 文件增加如下 profile 配置:
```xml
<profile>
    <id>default</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <repositories>
        <repository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </pluginRepository>
    </pluginRepositories>
</profile>
```
