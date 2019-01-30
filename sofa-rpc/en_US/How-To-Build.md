# How to build

> Install JDK7 or later and Maven 3.2.5 or later.

Download the codes directly and execute the following commands:

```
cd sofa-rpc
mvn clean install
```


**Note:** You can not build under a subdirectory (namely the submodule). Because there are too many SOFARPC modules, if every submodule needs to be installed and deployed, there will be much useless records in the repository. This issue is considered in the process of designing the SOFARPC project structure. The current structure saves you the trouble of installing and deploying all submodules, and you just have to install and deploy one module, namely the `sofa-rpc-all` (`all`) module.