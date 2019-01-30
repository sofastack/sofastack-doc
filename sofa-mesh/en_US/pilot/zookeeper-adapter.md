# ZooKeeper Adapter

ZooKeeper Adapter is an Adapter plug-in developed in accordance with the Istio registry center extension mechanism. It is used for docking all microservices frameworks that use ZooKeeper as a registry center. Currently, ZooKeeper Adapter supports SOFARPC and will  be available for Dubbo soon.

ZooKeeper Adapter uses ZooKeeper's watch mechanism to listen to the change events of service registration information, providing better real-time performance than polling.