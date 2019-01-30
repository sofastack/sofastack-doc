# 0.2.0 architecture extension design

## Extend network filter

### Current capability
To do

### Background
+ Currently, MSON has three types of filters: stream filter, network filter and listener filter.
+ Network filter is designed to support extensions, and it has several built-in extension implementations (proxy, tcpproxy and fault injection), but there is no corresponding extension mechanism to ensure that. This reconstruction is to add network filter extension mechanism. The stream filter extension mechanism already exists. The network filter is reconstructed with the stream filter extension mechanism for reference.

### Concrete reconstruction
+ Change the method of `NetworkFilterChainFactory` from `CreateFilterFactory` to `CreateFilterChain`.
+ Change the `CreateFilterFactory` originally called by `OnNewConnection` to `CreateFilterChain`, and delete `buildFilterChain`.
+ Add `NetWorkFilterChainFactoryCallbacks` and used it to encapsulate `FilterManager`, and clarify the range of methods that can be called by `CreateFilterChain`.
+ Add `NetworkFilterFactoryCreator` and `Register` under the filter.
+ Change `active
+ 
+ Listener` from a single NetworkFilter to multiple NetworkFilters, and modify the corresponding build function.
+ Configuration parsing supports multiple NetworkFilters, and move the logic of the starter to `pkg`.
