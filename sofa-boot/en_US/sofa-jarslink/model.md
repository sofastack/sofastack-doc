## Model
### Applications
Jarslink manages the life cycle of multiple applications. During runtime dynamic deployment, it usually converts a Jar file entity into an abstract model Biz.
+ Biz: abstract model of the application at runtime

### Instruction
Currently, Jarslink supports the telnet protocol and accepts the entered instructions. In the future, it will also support instruction execution through APIs. Acceptable instruction types:
+ InstallCommand: install the application
+ UninstallCommand: uninstall the application
+ CheckCommand: check the application state
+ SwitchCommand: switch the application state

### Service
The Jarslink plugin has expanded the SOFAArk container's services of `BizDeployer` and `CommandProvider` and referenced the SOFAArk container's exposed services of `BizManagerService` and `BizFactoryService`.
+ BizDeployer is the application deployment extension point provided by the SOFAArk container, and it is used to control the Biz startup in the Ark package. Jarslink has registered its own implementation with the SOFAArk container.
+ CommandProvider is the command processing extension point provided by the SOFAArk container, and it is used to process the commands received by the SOFAArk container through a telnet link.
+ BizManagerService is the Biz manager exposed by the SOFAArk container, and it is used for registration, deregistration, and other operations.
+ BizFactoryService is the Biz generator exposed by the SOFAArk container, and it is used to abstract static Biz package files into runtime Biz models.

