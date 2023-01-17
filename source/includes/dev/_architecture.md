# API Architecture

This chapter covers the basic architectural details of the API. If you are new to RW API development, you should start
here, as key concepts explained here will be needed as you go more hands-on with the API code and/or infrastructure.

## Overall architecture

The RW API is built using a [microservices architecture](https://en.wikipedia.org/wiki/Microservices)
using [AWS API Gateway](https://aws.amazon.com/api-gateway/) as the gateway application. The microservices are deployed
in a [Kubernetes](https://kubernetes.io/) cluster, available
as [NodePort Services](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport/)
and deployed as [Pods](https://kubernetes.io/docs/concepts/workloads/pods/).

Data is stored in multiple formats and services, ranging from databases running as Kubernetes Services,
to [SaaS](https://en.wikipedia.org/wiki/Software_as_a_service) solutions like [S3](https://aws.amazon.com/s3/)
or [RDS](https://aws.amazon.com/rds/).

## Routing

API Gateway receives all incoming requests to the RW API, and matches the HTTP verb and path with a set of preconfigured
values, forwarding it to one of several backend (Kubernetes) services it can reach. Each of these services corresponds
to a RW API Microservice, which is responsible for implementing the functionality for that endpoint.

Microservices that communicate with each other also use API Gateway for the same purpose - this way, microservices don't
need to be able to reach each other directly, they just need to know how to reach the gateway, simplifying
implementation.

Internal communication between the gateway and the microservices is done through HTTP requests, and as such each
microservice is built on top of a web server. These different web servers create a network within the API itself, to
which will refer throughout the rest of the documentation when we mention "internal network" or "internal requests". By
opposition, an "external request" will reference a request from/to the world wide web, and "external network" basically
means "the internet". Note that these are not actual networks (real or virtual), and the origin of a request is fully
transparent to both the gateway as well as the microservices.

## Microservices

A microservice is a small application, typically with running a web server, that implements a subset of the
functionality of the RW API. Said functionality is often exposed as public endpoints on the RW API, through API Gateway
routing rules that map a publicly available URL to a microservice - allowing API Gateway to forward an incoming request
from the www to the microservice itself.

Microservices also communicate with each other, typically through HTTP requests, that use the same routing strategy and
services as described above for public requests. Some microservices communicate with each other using other channels,
like [pub/sub](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern).

### Microservice dependency graphs

![Microservice dependency graph](https://raw.githubusercontent.com/gfw-api/wri-api-dependencies/master/graphs/dependencies_graph.png)

The graph above illustrates the dependencies between different microservices as of July 2020. Most dependencies are
based on endpoint calls: an arrow pointing from `query` to `dataset` means that the `query` microservice makes a call to
one of the endpoints implemented by the `dataset` microservice. The exception to this rule
are `doc-orchestrator`,  `doc-executor` and  `doc-writer`, who instead depend on each other
via [RabbitMQ](https://www.rabbitmq.com/) messages.

![Microservice with no dependencies](https://raw.githubusercontent.com/gfw-api/wri-api-dependencies/master/graphs/no_dependencies_graph.png)

The microservices above do not depend on any of the other microservices.

All microservices with public-facing HTTP endpoints depend on the `authentication` microservice to handle user management.

## Data layer dependencies

![Data layer dependencies](https://raw.githubusercontent.com/gfw-api/wri-api-dependencies/master/graphs/storage_graph.png)

The graph above illustrates the different data layer elements present on the RW API, and the microservices or sites that
depend on each of these.

## HTTP Caching

In the production environment, end user requests to the RW API are initially intercepted by an HTTP cache, implemented using
[Fastly](https://www.fastly.com). This is mostly transparent to both users and developers, except in certain scenarios 
that will be detailed as part of the development guide below.

## Lifecycle of a request

A typical API call will go through the following steps, between the request being received, and the response being
returned to the client application:

- An HTTP request from the www is issued to the RW API
- The DNS resolves to an AWS API Gateway instances (in the production environment, there is a
  prior [Fastly](https://www.fastly.com) cache step)
- Based on its internal configuration, API Gateway will route this request to one of several nodes (EC2 instances) that
  make up the Kubernetes (AWS EKS) cluster, and to a specific Kubernetes Service - a RW API Microservice.
- This service, implemented by Kubernetes Pods, will handle the HTTP request and generate the corresponding HTTP
  response. It may also, optionally:
  - If a [JWT token](https://jwt.io/) is present, it will send that token to the Authorization service, which handles
    user data validation and storage.
  - Depending on the logic of the endpoint being accessed, each microservice may reach out to other microservices, using
    HTTP requests, to load additional information. These requests are routed through API Gateway, like the original
    request from the www.
- The response is returned to API Gateway, and from it to the original requester.

## Infrastructure as code

The above described infrastructure is manages using Terraform, an [Infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_code)
solution that allows capturing all that complexity in a [Github repository](https://github.com/resource-watch/api-infrastructure).
