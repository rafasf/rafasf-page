+++ 
draft = false
date = 2020-05-02T09:01:51-05:00
title = "Microservice Principles"
description = "Principles to build a microservice"
slug = "microservice-principles" 
tags = ["architecture", "microservice", "principles"]
externalLink = ""
series = []
+++

{{<mermaid>}}
graph LR;
  p[Principles]
  business_domain[Modelled Around Business Domain]
  automation[Culture of Automation]
  hide_impl[Hide Implementation Details]
  decentralise[Decentralise All Things]
  independent[Independent Deploy]
  isolated_failure[Isolate Failure]
  observable[Highly Observable]

  p --> business_domain
  p --> automation
  p --> hide_impl
  p --> decentralise
  p --> independent
  p --> isolated_failure
  p --> observable
{{</mermaid>}}

### Modelled Around Business Domain

A _Business Domain_ represents part of the organisation, a capability which the
business provides (be it supporting or customer facing). The boundaries are more
clear, they usually don't change.

For a business domain to change, the organisation itself would be going to a
shift in the industry.

Teams will get to understand better the business since they directly own part of
it.

This principle benefits quite a bit of Domain Driven Design.

### Culture of Automation

Breaking down the business domains into services will increase the number of
deployables, therefore increase the operational overhead to deliver and maintain
all the services.

In order to get into microservices, the organisation _needs_ to automate things,
all the steps to get a service working should be easy and reproducible.

### Hide Implementation Details

This is about focusing on modelling the services correctly, using the bounded
contexts to understand what is important for each of the services.

With a solid internal model, the team knows what **should** be exposed to
consumers.

The same thought applies when being the **consumer**, with a solid internal
model, it should be easy to find what level of coupling exist between the
upstream service and yours.

> If two or more services have access to the same database, **nothing is
> hidden**.

### Decentralise All Things

Allow teams to have autonomy, freedom to do what they need to do. The team
should be able to go from code to production by themselves.

Focus on self-service capabilities, shared governance.

The autonomy brings more responsibility to the team. They will need to think
about the lifecycle of the application, support models and so on.

Besides autonomy, this also brings to attention the concept of **smart
endpoints, dumb pipes**. No logic while communicating to other services.

The _smart pipes_ are things like API Gateways which have business logics on
them or the famous ESB (Enterprise Service Bus) that (usually) contains most of
the business logic in older systems.

### Independent Deploy

A team should be able to deploy their service without the need of coordinate
with others.

As a provider you need to ensure that new deployments don't break anybody else.
One way to guarantee this is using [contract tests][contract_tests].

Besides ensuring that new deployments won't break any of the existing consumers,
there are couple other things to keep in mind:

- One service per host

  Traditionally multiple services would be deployed in a single host, share the
  configuration and resources.

- Co-existing endpoints

  The need to be backwards compatible or maintain multiple versions of an
  endpoint are very common and teams should build services with that in mind.

The traditional way of checking that a change in one service doesn't break
others is the "end-to-end" test. In a microservice architecture that model
doesn't work. In an organisation with 50 microservices, do you really want to
run an "end-to-end" test every time any of those change?

### Consumer First

Following the first principle, the service will provide certain capabilities and
the exposed API will be called by someone. The team should make it easier for
any consumer to use the service.

For HTTP APIs, OpenAPI is a good option to capture and share what is possible to
be done. Besides allowing consumers to easily interact with the service, they
also need to find the service. This is where **service discovery** comes in.

Service Discovery makes it easier for services to register themselves as well as
creating a place for consumers to find what is available.

### Isolate Failure

Failures will happen. And no team wants one microservice to bring the whole
ecosystem down. Each team building a service need to understand the impact they
would have as well as know the impact it would caused when the service fails.

After understanding the failure points, the team should evaluate what are tools
which will help them on handling such scenarios.

A few buzzwords around this would be [circuit breaker][circuit_breaker],
graceful degradation, back-pressure.

### High Observability

A few years ago it was probably fine to login to the **server** to check the
application logs but if you try to do this today you will be facing a much
higher number of _servers_ to check.

> The times of _sshing_ into a machine to check its state and logs are gone!

Everyone should focus in making it very easy to see what is happening in a
service **without** the need of having access to the service itself.

Having a microservice architecture means that you will have a big number of
services at some point. Understanding how each of them is doing is a must.

And the services are only one piece of the puzzle, besides those we should also
ensure that the cluster, databases, _kubernetes_ and so are healthy too.

## Other Resources

1. [Building Microservices](http://shop.oreilly.com/product/0636920033158.do)

[contract_tests]: https://docs.pact.io/#contract-testing
[circuit_breaker]: https://martinfowler.com/bliki/CircuitBreaker.html
