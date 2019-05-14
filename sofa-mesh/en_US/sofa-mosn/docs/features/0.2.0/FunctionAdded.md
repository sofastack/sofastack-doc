# 0.2.0 Core capability supplementation
## Supplement routing capability
### Support configuring weight on clusters upon routing
+ [Issue](https://github.com/sofastack/sofa-mosn/issues/92)
+ Function description<br>

	In the current route matching logic, when the router match succeeds, the corresponding cluster in the router is selected. This function allows you to configure multiple clusters with weights for one router. When the router is successfully matched, the system randomly returns a cluster according to the weight of the cluster.

+ Configuration example

```json

                              {
                              "weighted_clusters":[
                                {
                                  "cluster":{
                                    "name":"serverCluster1",
                                    "weight":90,
                                    "metadata_match":{
                                      "filter_metadata": {
                                        "mosn.lb": {
                                          "version": "v1"
                                        }
                                      }
                                    }
                                  }
                                },
                                {
                                  "cluster":{
                                    "name":"serverCluster2",
                                    "weight":10,
                                    "metadata_match":{
                                      "filter_metadata": {
                                        "mosn.lb": {
                                          "version": "v2"
                                        }
                                      }
                                    }
                                  }
                                }
                              ]
                              }

```
## Supplement LB capability
+ [issue](https://github.com/sofastack/sofa-mosn/issues/91)
### Support configuring weight on host
+ Support configuring weight on host to perform the weight-based LB algorithm.

+ Configuration example
```json
        {
        "hosts": [
          {
            "address": "11.166.22.163:12200",
            "hostname": "downstream_machine1",
            "weight": 1,
            "metadata": {
              "filter_metadata": {
                "mosn.lb": {
              "stage": "pre-release",
              "version": "1.1",
              "label": "gray"
            }
              }
            }
          }
        ]
        }

```
### Support smooth WRR load balancer
+ Algorithm implementation: 
	`smoothWeightedRRLoadBalancer`

+ Algorithm details<br>

```cgo
/*
SW (smoothWeightedRRLoadBalancer) is a struct that contains weighted items and provides methods to select a weighted item.
It is used for the smooth weighted round-robin balancing algorithm. This algorithm is implemented in Nginx:
https://github.com/phusion/nginx/commit/27e94984486058d73157038f7950a0a36ecc6e35.
Algorithm is as follows: on each peer selection we increase current_weight
of each eligible peer by its weight, select peer with greatest current_weight
and reduce its current_weight by total number of weight points distributed
among peers.
In case of { 5, 1, 1 } weights this gives the following sequence of
current_weight's:
     a  b  c
     0  0  0  (initial state)

     5  1  1  (a selected)
    -2  1  1

     3  2  2  (a selected)
    -4  2  2

     1  3  3  (b selected)
     1 -4  3

     6 -3  4  (a selected)
    -1 -3  4

     4 -2  5  (c selected)
     4 -2 -2

     9 -1 -1  (a selected)
     2 -1 -1

     7  0  0  (a selected)
     0  0  0
*/
```

## Supplement XDS capability

### CDS 
+ [Issue](https://github.com/sofastack/sofa-mosn/issues/116)
+ Provide the capability of adding and updating clusters.
	+ External interface: `TriggerClusterAddOrUpdate`
	+ Internal interface: `AddOrUpdatePrimaryCluster`
+ Provide the capability of deleting clusters.
	+ External interface: `TriggerClusterDel`
	+ Internal interface: `RemovePrimaryCluster`
   
### LDS 
+ [Issue](https://github.com/sofastack/sofa-mosn/issues/117)
+ Provide the capability of adding and updating listeners
	+  External interface: `AddOrUpdateListener`
	+ Internal interface: `AddOrUpdateListener`
+ Provide the capability of deleting listeners.
	+ External interface: `DeleteListener`
	+ Internal interface: `StopListener` and `RemoveListeners`