# Piran-Framework

## What is it?

Piran is a framework to simplify developing  large scale applications. It's based on a next generation software architecture paradigm to overcome micro-service drawbacks. 

#Chapter 1: What is the problem?

## Monolithic VS Micro-service

let's challenge the definitions. Abstract of most articles promoting micro-services is something like this:

> In ancient world, applications was monolithic, All developers was working in a same code base, In that terror era development process had faced several problems and obstacles, it was hard to develop in a large team, it was hard to deploy, it was hard to scale. but suddenly micro-service idea came to rescue. It gave us the ability to build Google, Twitter, Netflix, Uber and several other devilish capitalist companies. 

There are some intelligent persons who caution about the new problems which comes with micro-services. you should handle message translation, you should care about contracts between services, you should provide fall-backs. clear boundary between services are necessary. if you go async then you should handle timeouts yourself. handle a transaction in a distributed world is catastrophe. Some wise people like Martin Fowler says that if you think you should do micro-service, probably you're wrong.

Micro-service and monolithic, both have their own benefits and drawbacks. To understand them we should go back to origin problems.
##What was the problem?

Bad developers used to blame programming language, framework, tools, their boss, product team, politician, government (by the way, government is always delinquent) and all the world but themselves. there is no difference for monolithic, after raise in micro-service popularity, who is  a better offender than monolithic. every software project need care, if you don't pay enough attention to clean code, unit and integration tests or you have a poor design in terms of modularity finally you end up with a sophisticated code, hard to maintain, hard to develop, full of bugs and full of performance issues software.

Nevertheless, a single runtime application has its limitations. A single application could be scaled definitely, however If a single part of an application needs to scale with a different rate from rest of application, it cannot done without separating it and deploy it separately. imagine you have a e-shop website, and 80% of your backend calls belong to search system, but from the technical point of view, just 10% of you code and runtime objects make up search system, if you decide to scale up your application, you should deploy another instance which 90% of it is useless, because the previous instance easily can handle all but search calls which is 20% of all calls.

propagating failure

slow startup time

prevention from adopting new technologies and language

slow packaging and deployment



## Modularity is a old concept

separated source files, packages, libraries.

too many people work with a same code base: successful project like Linux kernel so not a problem



 In house API design: much cleaner than batching them in remote calls. (use atomic isolated peace of codes)

## Micro-service: solving a problem by making life harder

different scalability rate

partial deployment

organizational problems, need for agile teams

more clear and small parts

distributed transactions

too much communication

everyone wait for another(sync)

what if something does not work(async)

eventual consistency, SAGA, event sourcing, CQRS, store&forward, retry patterns, welcome to micro-service hell.

# Chapter 2: Containerize everything

## Docker: Encapsulate every runtime

## Kubernetes: Orchestrate containers

## The Container orchestration way

narrow the responsibility. Treat every runtime as same. just make sure there are plenty of them on the cluster. recover from failure. auto scaling. 

containers are separated from each other and they communicate each other on standard protocol. they assume each other as a remote service. services need each other. they have a lot to chat with each other

# Chapter 3: Plugin way, do what you want in tiny peace of code

## The hard way: Eclipse and all other OSGi folks

## Keep it simple like Mozilla

## Overcome organizational problems

# Chapter 4: Another way to solve the problem

## Plugin management system: Ganjex

## Services is large, break them down into more small parts. 

every use-case, a service. services can talk to each other easily with low cost.

## There should be a Container: Safir

## API-Gateway? lets do it simple: Darbaan

## Management is necessary: Dastoor

## Finding each other: Geev

## How all these talk together? ZeroMQ of course 

# Chapter 5: different pattern for different people

## Shared DB: migration from monolithic  

use cassandra or HBase. how handle entity models.

do not afraid of shared db.

## Separate DBs: migration from micro-service 

separate data, talk to each one you want.

categorize your services

