## Fault tolerance

Usually, a service have multiple service providers in a cluster. Some of the service providers may have persistent connections still survived due to network, configuration, long-term fullgc, full thread pool, hardware failure and others, but the program cannot respond properly. The stand-alone fault tolerance function can degrade the exceptional service providers so that the client requests can be pointed to the healthy node. When the exceptional nodes become normal, the standalone fault tolerance function will restore the nodes, so that the client requests can gradually distribute traffic to the nodes. The standalone fault tolerance function solves the problem that service failures continue to affect the business, avoids the avalanche effect, reduces the long response time required for manual intervention and increases system availability.

Running mechanism:

* Standalone fault tolerance counts the number of calls and the number of exceptions in a time range, and calculates the abnormal rate of IP for each service and the average abnormal rate of the service.
* When the IP abnormal rate is greater than the service average abnormal rate to a certain ratio, the dimension of the service + ip is degraded.
* If the weight of the service + ip dimension is not degraded to 0, then when the call of the service + ip dimension is normal, the weight will be restored.
* The entire calculation and control process proceeds asynchronously, thus not blocking the call.

The standalone fault tolerance is used as follows:
```java
FaultToleranceConfig faultToleranceConfig = new FaultToleranceConfig();
        faultToleranceConfig.setRegulationEffective(true);
        faultToleranceConfig.setDegradeEffective(true);
        faultToleranceConfig.setTimeWindow(20);
        faultToleranceConfig.setWeightDegradeRate(0.5);

FaultToleranceConfigManager.putAppConfig("appName", faultToleranceConfig);
```
As above, after the standalone fault tolerance switch is turned on, the application will calculate the exceptions every 20s time window. If a service + IP calling dimension is determined to be a faulty node, the weight of the service + IP will be degraded to 0.5 times.

For more detailed parameters, please refer to [Standalone troubleshooting](./Configuration-Fault-Tolerance.md)