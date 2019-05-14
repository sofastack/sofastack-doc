# MOSN project

[![Build Status](https://travis-ci.org/alipay/sofa-mosn.svg?branch=master)](https://travis-ci.org/alipay/sofa-mosn)
[![codecov](https://codecov.io/gh/alipay/sofa-mosn/branch/master/graph/badge.svg)](https://codecov.io/gh/alipay/sofa-mosn)
[![Go Report Card](https://goreportcard.com/badge/github.com/sofastack/sofa-mosn)](https://goreportcard.com/report/github.com/sofastack/sofa-mosn)
![license](https://img.shields.io/badge/license-Apache--2.0-green.svg)

MOSN, the short name of Modular Observable Smart Network, is a powerful proxy acting as Service Mesh's data plane like [Envoy](https://www.envoyproxy.io/) but written in GoLang. MOSN supports Envoy and Istio's APIs and can be integrated with [Istio](https://istio.io/), so we use MOSN instead of Envoy in [SOFAMesh](https://github.com/sofastack/sofa-mesh). The initial version of MOSN was jointly contributed by Ant Financial and UC Business Unit of Alibaba, and we look forward to the community to participate in the follow-up development and build an open source excellent project together.

## [MOSN introduction](docs/Introduction.md)

## Core competence

+ Integrated with Istio
	+ Integrated with Istio 1.0 and V4 APIs to run based on full dynamic resource configuration
+ Core forwarding
	+ Self-contained Web server
	+ Support TCP proxy
	+ Support TProxy mode
+ Multi-protocol
	+ Support HTTP/1.1 and HTTP/2
	+ Support SOFARPC
	+ Support Dubbo protocol (under development)
+ Core routing
	+ Support Virtual Host routing
	+ Support Headers/URL/Prefix routing
	+ Support Host Metadata-based Subset routing
	+ Support retry
+ Backend Management and load balancing
	+ Support connection pool
	+ Support throttling
	+ Support active backend health check
	+ Support load balancing strategies, such as Random and RR
	+ Support Host Metadata-based Subset load balancing strategy
+ Observability
	+ Observe network data
	+ Observing protocol data
+ TLS
	+ Support HTTP/1.1 on TLS
	+ Support HTTP/2.0 on TLS
	+ Support SOFARPC on TLS
+ Process management
    + Support smooth reload
    + Support smooth upgrade
+ Extension capability
    + Support custom private protocols
    + Support adding custom extensions in protocol at the TCP IO layer

## Get started
* See [Quick start guide](docs/quickstart/Setup.md).

## Documentation
* [Catalog](docs/Catalog.md)

## Community
* [Issues](https://github.com/sofastack/sofa-mosn/issues)

## Versions
* [Change history](docs/CHANGELOG.md)

## Contribute
+ [Contribute codes](docs/develop/CONTRIBUTING.md)
+ MOSN is still in the primary stage, and there are many capabilities that need to be supplemented and improved. We welcome you to join us to build a better MOSN.

## Acknowledgement
Thank Google, IBM and Lyft for creating the Envoy and Istio systems and enabling many excellent projects open-sourced, which provides good reference for MOSN so that we can quickly implement our own ideas.