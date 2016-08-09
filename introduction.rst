Introduction / What is nstack?
************

.. _what_is_nstack: 
**What is nstack?**

nstack turns a piece of code (such as a function, a class, or a libary) into a lightweight microservice that can be bound to an event-source (such as a stream, a message queue, or a scheduler). On an event, nstack intelligently distributes execution of your service and routes messages into your code.

**Why is this useful?**

nstack automates all infrastructure, protocols, and middleware: a simple class or library becomes a fully-fledged cloud service that can be attached to events. As a developer, you can focus exclusively on writing your core business-logic, while nstack makes performant, intelligent decisions about your services, and solves many common problems out of the box (monitoring, logging, scheduling, type-checking, etc.)

**How does this relate to microservices and 'serverless'?**

Although they are similar abstractions, nstack takes microservices and 'serverless' a step further by treating services like objects in a statically-typed programming language. We believe in taking the lessons and research from building languages and applying them to infrastructure.

If you're still confused, please email info@nstack.com; we'd love to chat!