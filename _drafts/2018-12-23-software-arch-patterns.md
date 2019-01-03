---
layout: post
title: "Software Arch Patterns"
---

## Event-Driven Architecture

> The event-driven architecture pattern consists of two main topologies, the mediator and the broker. The mediator topology is commonly used when you need to orchestrate multiple steps within an event through a central mediator, whereas the broker topology is used when you want to chain events together without the use of a central mediator. 

### Mediator Topology

> The mediator topology is useful for events that have multiple steps and require some level of orchestration to process the event. 

> It is important to note that the event mediator doesn’t actually perform the business logic necessary to process the initial event; rather, it knows of the steps required to process the initial event. 

> The event processor components contain the application business logic necessary to process the processing event. Event processors are self-contained, independent, highly decoupled architecture components that perform a specific task in the application or system. While the granularity of the event-processor component can vary from fine-grained (e.g., calculate sales tax on an order) to coarsegrained (e.g., process an insurance claim), it is important to keep in mind that in general, each event-processor component should perform a single business task and not rely on other event processors to complete its specific task.

### Broker Topology

> Perhaps one of the most difficult aspects of the event-driven architecture pattern is the creation, maintenance, and governance of the event-processor component contracts. Each event usually has a specific contract associated with it (e.g., the data values and data format being passed to the event processor). It is vitally important when using this pattern to settle on a standard data format (e.g., XML, JSON, Java Object, etc.) and establish a contract versioning policy right from the start. 

> Testability Rating: Low Analysis: While individual unit testing is not overly difficult, it does require some sort of specialized testing client or testing tool to generate events. Testing is also complicated by the asynchronous nature of this pattern.

## Microservice Architecture
> Software can be like a baby elephant: It is cute and fun when it’s little, but once it gets big, it is difficult to steer and resistant to change. The microservice architecture is designed to help developers avoid letting their babies grow up to be unwieldy, monolithic, and inflexible.

> Designing the right level of service component granularity is one of the biggest challenges within a microservices architecture.

> If you find you need to orchestrate your service components from within the user interface or API layer of the application, then chances are your service components are too fine-grained. Similarly, if you find you need to perform interservice communication between service components to process a single request, chances are your service components are either too fine-grained or they are not partitioned correctly from a business functionality standpoint.

## 引用
[The top 5 software architecture patterns: How to make the right choice](https://techbeacon.com/top-5-software-architecture-patterns-how-make-right-choice)
