## Product description
SOFAArk is a light-weight，java based classloader isolation framework 
open sourced by Ant Financial. Based on  [Fat Jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html#executable-jar-jar-file-structure) technology, the container can pack simple single-module Java applications or Spring Boot applications into a self-contained executable Fat Jar, known as an Ark package. When the `java -jar` command is used to start an Ark package embedded with the SOFAArk class isolation container, the SOFAArk container will start, and it then starts each Ark plugin and application.

## Background
In Java world, dependency is always a problem, and can cause various errors, such as `LinkageError`, `NoSuchMethodError` etc. There are many ways to solve the dependency problems, the Spring Boot's way is using a dependency management to manage all the dependencies, make sure that all the dependencies in the dependency management will not conflict and can work pretty well. This is quite a simple and efficient way, it can cover most scenario, but there is some exceptions.

For example, there is a project that need protobuf version 2 and protobuf version 3, and because protobuf version 3 is not compatible with version 2, so the project can not simply upgrade the protobuf to version 3 to solve the problem. There is same problem for hessian version 3 and version 4.

To cover those exceptions, we need to introduce a classloader isolation way, make different version of a framework loaded by different classloader. There are many framework that can do classloader isolation, perhaps the most famous one is OSGi, but OSGi classloader schema is too complex, beside classloader isolation, it also has ability to do hot deploy and a lot of other functionalities that we actually don't want.

So this is the origin of SOFAArk, it's goal is to use a light-weight classloader isolation mechanism to solve the problem that Spring Boot did not solve. And just a remind that SOFAArk is not bind to Spring Boot, actually it is a more general classloader isolation framework that can be used with any other frameworks too.

## How SOFAArk Works

There are three concepts in SOFAArk: `Ark Container`, `Ark-Plugin` and `Ark-Biz`; they are organized as what the following graph shows:

![image.png | center | 1310x1178](https://cdn.yuque.com/lark/2018/png/590/1523868989241-f50695ed-dca0-4bf7-a6a9-afe07c2ade76.png)

First of all, we explain what roles these concepts play;

+ `Ark Container`: It's the runtime manager of total framework; it will startup in the first place, then it resolves `Ark Plugin` and `Ark Biz` in classpath and deploys them.

+ `Ark Plugin`: A fat jar packaged by `sofa-ark-plugin-maven-plugin`, generally it would bring with a class-index configuration which describes what class would be exported and imported. `Ark Plugin` can resolve classes from each other.

+ `Ark Biz`: A fat jar packaged by `sofa-ark-maven-plugin`, it mainly contains all staff what a project need in runtime. `Ark Biz` can resolve classes form `Ark Plugin`, but not inverse.

In runtime, `Ark Container` would automatically recognize `Ark-Plugin` and `Ark-Biz` in classpath, and load them with the independent classloader. According to configurations brought by `Ark Plugin` and `Ark Biz`, `Ark Container` would build a class-index table, so they can be

isolated well. For example, if a project has two dependencies of A and B, but A depends on C (version = 0.1) and B depends on C (version = 0.2), so conflicts maybe emerge.


## Scenario
SOFAArk was originally designed to resolve package conflicts, but when and how can you use SOFAArk? Assume that a project needs to introduce two third-party packages A and B; package A needs to depend on package C with the version number 0.1, whereas package B needs to depend on package C with the version number 0.2; the two versions of package C are incompatible, illustrated as follows:

![conflict](https://cdn.yuque.com/lark/2018/png/590/1523868150329-41ea3982-4783-49b0-a1e6-ffffddbe0886.png)

You can use SOFAArk to resolve the dependency conflict in this scenario. You only need to pack package A and package C with the version number 0.1 into an Ark plugin, and then make the application engineering introduce the plugin to depend on.



