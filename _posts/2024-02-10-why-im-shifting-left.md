---
layout:     post
title:      Everyone's Talking About Shifting Left - Here's Why I'm Shifting Right
date:       2023-11-07
summary:    Why Shifting Left isn't a silver bullet, the advantages of testing more in production instead, and how Prodzilla can help achieve it. 
categories: 
---

Every so often in software engineering, new methodologies and frameworks emerge with the promise of increasing the reliability and delivery velocity of software. These "Grand New Ways Of Doing Things" have a period of time during which they are applied universally, touted by consultants and Engineering Managers alike, to solve *all* the current problems. Previous "Grand New Ways Of Doing Things" such as Agile, Test Driven Development, even Microservices, despite starting as cure-alls, have settled into the toolkit of sometimes-useful strategies, contingent upon context.

The latest buzzword that's probably injecting a few too many technical items on your product roadmaps is "Shifting Left," a concept that champions the idea of integrating more testing, development, and planning stages earlier in the software development lifecycle. The principle is simple: the sooner you catch and fix a bug, the less it costs and the faster you can move. This approach encourages developers to run more tests locally, integrate earlier, and generally, speed up the feedback loop on the development process.

However, despite its popularity, I remain skeptical about the effectiveness of Shifting Left, or at least the way that it's generally applied. Here's why:

#### The Downsides of Shifting Left

**Running services locally requires high dependence on mocks / fakes**. To run things locally or in early environments, teams often rely on mocks or fakes to stub out responses from downstream services. These require significant time to create and maintain, obviously don't operate the same way as their real counterparts in production, and risk becoming outdated over time as the real service is updated.

**The Production Paradox**. No matter how sophisticated the local or pre-production environments are, they never fully emulate the complexities of production. Here's a list of some things that are likely to differ in production vs other environments:
- HTTP/ GRPC vs HTTPS / GRPCS, self-signed certs vs TLS, etc
- Connections to vendors and third-parties
- Access issues, networking and permission-wise - what the services and the engineers have access to
- Number and availability of each service - everything pre-prod is usually scaled down for cost
- The test data in pre-prod often looks vastly different to real use
- The patterns of use in each environment

This single gap is the leading cause of overconfidence in the stability and performance of software, and hence production incidents.

**Dev Efficiency** It's 2024. Software engineering is changing. The fastest delivering engineers on any team will at times have multiple pull requests open, relying on automated tests run somewhere in GitHub or GitLab land to check their work for them. Despite knowing how to do it in specific situations, I don't believe great engineers regularly spin up a local environment, as it can be incredibly time-consuming, detracts from dev time, and this work can be rightfully offloaded to the cloud VM farm.

## Embracing the Fear: Shifting Right

![An IQ bell curve suggesting to test in production](/images/iq-bell-curve.jpg)

Shifting Right, on the other hand, is about embracing the once-taboo practice of testing in production. "Testing in Production" used to be synonymous with recklessness. Today, I see it as a sign of sophistication, and I see a lot of potential in this domain for enhancing software reliability and delivery speed.

## The Case for Shifting Right

**Authentic Validation**. Testing in prod offers a level of assurance unmatched by any pre-prod environment. When you observe your software operating successfully in the real world, you know it's genuinely ready.

**Shifting Right unifies testing strategy**. In the current standard development process, each of blackbox and end-to-end tests, synthetic monitoring, and observability occupies their own slice of a development cycle and has own codebase. Yet all of these things are extremely related! Convergence can not only simplify the testing process under one cohesive plan, but allows for less dev time spent on each individual step, and high levels of code reuse. 

**Cost Efficiency**: One thing engineering management everywhere is talking about more than shifting left is reducing cloud costs! Most dev teams are operating with 4-5 environments in various states of disrepair, but get almost all of their value from 1-2 of these (hint: one of these is prod!). Maintaining multiple dev or testing environments is a costly affair. By focusing on production, we can reduce unnecessary expenditure on cloud resources.

## A Fine Balance

All this is to say, as with all things, the correct approach is likely balanced. If your engineers aren't getting any feedback about the quality or behaviour of their code until the end of some pipeline, then yes, please shift left. But don't fret just because your team isn't running anything locally - consider this might actually be a good thing!

## Introducing Prodzilla

It's from this perspective that I'm developing [Prodzilla](https://prodzilla.io).

Right now Prodzilla is an open-source, low-code, synthetic monitoring tool, with a focus on easily testing complex user flows not traditionally tested in prod. The intention is to grow it into a framework that provides everything you need to test in production, and surface the discovered behaviour in useful outputs such as internal or external docs and alerts.

If you like the idea, please give us a star [on our Github](https://github.com/prodzilla/prodzilla)! 


## Conclusion

While Shifting Left has its merits in promoting earlier feedback and bug detection, it is not a silver bullet. The losses associated with an over-reliance on simulated environments are significant. 

Shifting Right offers a pragmatic, reality-grounded approach that aligns testing with actual user experiences. I hope Prodzilla can be one part of this journey, aiming to make testing in production a feasible, efficient, and integral part of the software development lifecycle.