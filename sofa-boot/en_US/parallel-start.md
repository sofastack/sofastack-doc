SOFABoot will calculate the dependency tree based on the Require-Module. For example, the following dependency tree represents that Modules B and C depend on Module A, Module E depends on Module D, and Module F depends on Module E:

![undefined](resources/module-parallel.png) 

The dependency tree guarantees that Module A starts before Modules B and C, Module D before Module E, and Module E before Module F, but without defining the start orders between Modules B and C, or Modules B, C and Modules D, E and F, which can start either in serial or parallel.

SOFABoot will start the modules in parallel by default. During use, if you want to disable parallel start, you can add the following parameter to application.properties:

```java
com.alipay.sofa.boot.module-start-up-parallel=false
```

