# Tests

## Unit test
Place the unit test cases in the modules developed by yourself.

If the cases rely on a third-party server (such as ZooKeeper), you must manually add the profile. See the `registry-zookeeper` module code.

If the cases rely on other modules and integration test is required, place them in the `test/test-intergrated` module.

If the cases also rely on a third-party server (such as ZooKeeper), place them in the `test-intergrated-3rd` module.

## Performance test
Close the following projects that are closed by default:

`-Dcontext.attachment.enable=false -Dserialize.blacklist.enable=false -Ddefault.tracer= -Dlogger.impl=com.alipay.sofa.rpc.log.SLF4JLoggerImpl -Dmultiple.classloader.enable=false -Devent.bus .enable=false`

A pressure test on BOLT+hessian has been done.

- Server: 4C8G virtual machine; gigabit network; jdk1.8.0_111;

- Client: 50 concurrent requests

Protocol | Request | Response | Server | TPS | Average RT (ms) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| bolt+hessian | 1KB string | 1KB string | Directly return | 10000 | 1.93 |
| bolt+hessian | 1KB string | 1KB string | Directly return | 20000 | 4.13 |
| bolt+hessian | 1KB string | 1KB string | Directly return| 30000 | 7.32 |
| bolt+hessian | 1KB string | 1KB string | Directly return | 40000 | 15.78 |
| bolt+hessian | 1KB string | 1KB string | Directly return | 50000 (Close to the utmost limit, error rate: 0.3%) | 26.51 |