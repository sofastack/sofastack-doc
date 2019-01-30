# Introduction

SOFALookout is a lightweight and open source middleware service of Ant Financial that solves the metrics and monitoring issues of the system. The services it provides include: Event logging, collecting, processing, storing, and querying of Metrics. The open source project consists of two separate parts, the client and server side services.

## Client-side service

SOFALookout Client is a Java SDK that helps developers log events of metrics in project code. It also allows you to view real-time status information for the Java application.

```
       +------------------+                      Reg:
 API:  | dimension meters +--------+
       +------------------+        |   flatmap   +---------------------------+
                                   +-----------> |  Default/DropwizardMetrics|
                                   |             +---------------------------+
                                   |
                                   |    http     +--------------+
                                   +-----------> |Lookout server|
                                   |             +--------------+
       +----------------------+    |  add common tags dimension
EXTS:  |   JVM,OS,GC...       +----+
       +----------------------+

```

## Server-side services

SOFALookout Server helps you solve system state metrics in a distributed environment. Its data sources include, but not limited to the projects that use the lookout-client package. The server will be available in the next release, so stay tuned.