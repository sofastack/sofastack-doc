# Quick start guide

This document introduces how to apply SOFARPC for service publishing and reference. This example will simulate a server locally to listen to a port and publish a service, and the client will reference the service for direct call. 

You can get the code sample of this document by clicking [here](https://github.com/sofastack/sofa-rpc/tree/master/example/src/test/java/com/alipay/sofa/rpc/quickstart). 

## Create a project 
You need to install JDK 6 or above and Maven 3 or above. 

Create a new Maven project and introduce SOFARPC dependency. 

```xml 
<dependency> 
    <groupId>com.alipay.sofa</groupId> 
    <artifactId>sofa-rpc-all</artifactId> 
    <version>latest version</version> 
</dependency> 
``` 

Note: The latest version can be found at https://github.com/sofastack/sofa-rpc/releases. 

## Write a server implementation 
Step 1: Create interface 

```java 
/** 
 * Quick Start demo interface 
 */
Public interface HelloService { 
    String sayHello(String string); 
} 
``` 

Step 2: Create interface implementation 

```java 
/** 
 * Quick Start demo implement 
 */ 
public class HelloServiceImpl implements HelloService { 
    @Override 
    public String sayHello(String string { 
        System.out.println("Server receive: " + string); 
        return "hello " + string + " !"; 
    } 
} 
``` 

Step 3: Write the server code 

```java 
/** 
 * Quick Start Server 
 */ 
public class QuickStartServer { 

    public static void main(String[] args) {
        ServerConfig serverConfig = new ServerConfig() 
                .setProtocol("bolt") // Set a protocol, which is bolt by default 
                .setPort(12200) // set a port, which is 12200 by default
                .setDaemon(false); // non-daemon thread 

        ProviderConfig<HelloService> providerConfig = new ProviderConfig<HelloService>() 
                .setInterfaceId(HelloService.class.getName()) // Specify the interface 
                .setRef(new HelloServiceImpl()) // Specify the implementation 
                .setServer(serverConfig); // Specify the server 

        providerConfig.export (); // Publish service 
    } 
} 
``` 

## Write a client implementation 
Step 1: Get the server interface 

In general, the server provides the interface class to the client in the form of `jar`. In this example, this step is skipped since the server and client are in the same project. 

Step 2: Write the client code 

```java 
/**
 * Quick Start client 
 */ 
public class QuickStartClient { 
    public static void main(String[] args) { 
        ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>() 
            .setInterfaceId(HelloService.class.getName()) // Specify the interface 
            . setProtocol("bolt") // Specify the protocol.setDirectUrl 
            ("bolt://127.0.0.1:12200"); // Specify the direct connection address 
        // Generate the proxy class
        HelloService helloService = consumerConfig.refer(); 
        while (true) { 
            System.out.println(helloService.sayHello("world")); 
            try { 
                Thread.sleep(2000); 
            } catch (Exception e) { 
            }
        } 
    } 
} 
``` 

## Run 
Start the server and client separately. 

The server outputs: 

> Server receive: The world 

The client outputs: 

> hello world ! 


## More 
For more examples, please refer to: [example](https://github.com/sofastack/sofa-rpc/tree/master/example) 

For SOFABoot example, please refer to: [sofaboot-sample-with-rpc](https ://github.com/sofastack/sofa-boot/tree/master/sofaboot-samples/sofaboot-sample-with-rpc)