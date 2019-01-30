### Ark package
The `Executed Fat Jar` that meets the specific directory format requirements can use the officially provided `Maven` plug-in (`sofa-Ark-maven-plugin`) to package the engineering application into a standard-format `Ark package`. Start the application on top of the SOFAArk container with the `java -jar` command. The `Ark package` usually contains the `Ark Container`, `Ark Plugin` dependency (if any), merged deployed `Ark Biz` (if any), and the `Ark Biz` of the application itself. For details, refer to the [Ark package](./ark-jar)；

### Ark Container
The Ark container (`Ark Plugin` and `Ark Biz`) runs on top of the SOFAArk container. The container has the ability to manage multiple plug-ins and applications. After successful start, the container will resolve the configuration of `Ark Plugin` and `Ark Biz`, complete loading of the isolation and start them in turn based on their priorities. For details, refer to [SOFAArk container startup](./startup)；

### Ark Plugin
The Ark plug-in, which meets the specific fat jar directory format requirements, can use the officially provided `Maven` plug-in (`sofa-Ark-plugin-maven-plugin`) to package one or multiple common `Java Jar` packages into a standard-format `Ark Plugin`. `Ark Plugin` will contain a configuration file that usually contains the import and export configuration of plug-in classes and resources and the priority of plug-in startup. When running, the Ark container will use an independent `PluginClassLoader` to load the plug-ins, and build the index table for class loading according to the plug-in configuration, so that the plug-ins are isolated from each other and from the applications. For details, refer to [Ark Plugin](./ark-plugin)；

### Ark Biz
The Ark module, which meets the specific fat jar directory format requirements, can use the officially provided `Maven` plug-in (`sofa-Ark-maven-plugin`) to package an engineering application into a standard-format `Ark Biz` package. The `Ark Biz` package has two roles: one is to be the organizational unit of the engineering application module and its dependent packages, and the other is to be used as a common jar package dependency by other applications to start multiple `Ark Biz` packages in the same SOFAArk container. Multiple `Ark Biz` packages share the `Ark Container` and `Ark Plugin`. For details, refer to [Ark Biz](./ark-biz)；



