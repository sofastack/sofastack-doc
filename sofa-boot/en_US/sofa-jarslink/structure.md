## Architecture diagram
Jarslink 2.0 is an Ark plugin and needs to depend on the SOFAArk container at runtime. Jarslink 2.0 is in the middle layer between the applications and the containers at runtime. Boundary interaction mode:
+ 1. Application boundaries: Jarslink 2.0 configures export classes that can be directly used by the applications. Such classes are loaded by Jarslink at runtime.
+ 2. Container boundaries: The Ark plugin can interact with the SOFAArk container by using the exposed extension points and services. Jarslink expanded the `BizDeployer` implementation and referenced `BizManagerService` and `BizFactoryService` container services.

![jarslink-structure](../resources/jarslink-structure.png)

## Module division
The implementation classes of each module appear only in their own modules and are generally not cross-dependent. Required cross-dependencies will be moved into the core module. Detailed module descriptions can be found in the following table:

| Module name | Sub-module name | Description | Dependency relationship |
|---|---|---|---|
|bom| |Dependent on version control|None|
|core|common|Common module with log classes|None|
|core|spi|SPI module, defining basic interfaces and commands|None| 
|core-impl|runtime|Jarslink runtime management, processing commands|Core|
|integration| |Ark plugin packaging module, implementing SOFAArk container service extension point and referencing container services | All|


