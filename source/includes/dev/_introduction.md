# Introduction

Welcome to the Resource Watch API Developer Documentation.

## Who is this for?

This section covers the behind-the-scenes details of the RW API, that are relevant for developers trying to build their
own RW API microservice. If you are looking for instructions on how to use the RW API to power your applications,
the [RW API Documentation](index.html) is probably what you are looking for.

The developer documentation is aimed at software developers that are familiar with the RW API from a user perspective,
and want to extend or modify the functionality of the API. From a technical point of view, this section assumes you are
familiar with some technologies, protocols and patterns that are used on the RW API, such as:

- [HTTP and HTTPS](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
- [Microservices architecture](https://en.wikipedia.org/wiki/Microservices)
- [Docker](https://www.docker.com/)
- [Terraform](https://www.terraform.io/)
- [Kubernetes](https://kubernetes.io/)
- [Amazon Web Service](https://aws.amazon.com/), with a stronger focus on EKS, API Gateway and EC2.

This guide also assumes you are comfortable with programming in general. To keep these docs simple, and as most of the
RW API source code is written in [nodejs](https://nodejs.org/en/), that is the language we'll use for examples or when
presenting specific tools and libraries. However, while we recommend using Nodejs, you may use different tools and/or
languages when developing your microservices.

If any of these concepts are new or unfamiliar, we suggest using your favourite search engine to learn more about them
before proceeding.

### A note on Control Tower

Throughout the RW API ecosystem (these docs, microservice README, etc) you may find leftover references to "Control
Tower". Control Tower is the name of an application that was used at the core of the RW API, but has since been replaced
for alternatives:

- Request routing is now handled by [AWS API Gateway](https://aws.amazon.com/api-gateway/) (and [Localstack](https://localstack.cloud/)).
- User management is now handled by the `authorization` microservice.
- [Fastly](https://www.fastly.com) integration is now done by the RW API integration libraries.

If you find references to Control Tower, those are most likely outdated documentation/example configuration bits, that
are either no longer in use, or have been modified to match the new stack.
