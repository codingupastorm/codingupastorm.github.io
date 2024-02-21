---
layout:     post
title:      "Prodzilla: From Zero to Prod with Rust and Shuttle"
date:       2024-02-21
summary:    Why I chose Rust to build Prodzilla, how you can host it on Shuttle for free, and some problems that we need more testing tools for.  
categories: 
---

I’ve been working on [Prodzilla](https://github.com/prodzilla/prodzilla), a modern synthetic monitoring tool built in Rust. I wanted to share how it's different to existing tools, why I’ve built it in Rust and how you can host it for free on Shuttle, and what I hope to achieve in the long-term.

## What-zilla?

Synthetic monitoring is a staple component in the testing and observability of SaaS products - probing services in production to ensure they’re up and working as expected. Many synthetic monitoring tools exist, which generally shallowly check connectivity to an endpoint - they rarely support the testing of real system behavior.

As a synthetic monitoring tool Prodzilla is unique in a couple of key ways. Firstly, it focuses on testing complex user flows in the way a real user would, by allowing chained requests, user authentication, and use of response body values in subsequent requests.

Secondly, it optimizes for human readability, allowing the creation and verification of these complex flows with just yaml, rather than requiring written code. This yaml file is all that’s needed to have a multi-step user flow scheduled to be tested every 60 seconds, assert that the system behavior is as expected, and notify you if it’s not. I hope to make this more human readable over time, both in yaml and via UI.

```yaml
stories:
  - name: get-ip-info
    steps:
      - name: get-ip
        url: https://api.ipify.org/?format=json
        http_method: GET
      - name: get-location
        url: https://ipinfo.io/${{steps.get-ip.response.body.ip}}/geo
        http_method: GET
        expectations:
          - field: Body
            operation: Contains 
            value: "Australia"
    schedule:
      initial_delay: 10
      interval: 60
    alerts:
      - url: https://my.site/notify
```

As for why I’ve built this: We should all be testing more in production. Once an engineering faux-pas, testing in production is now more of a sign of sophistication than a sin. I wrote more about this in another article, ['Everyone’s Talking About Shifting Left - Here’s Why I’m Shifting Right'](https://codingupastorm.dev/2023/11/07/why-im-shifting-left/). 

Prodzilla is a synthetic monitoring tool right now. Over time I want to deliver a range of tools designed to help everyone test in production. More on that below.

## Why Rust?

Using Rust for Prodzilla was a very deliberate decision - for the following reasons.

### Performance and Predictability

The primary reason for choosing Rust was that I wanted to be proud of Prodzilla performance-wise. A synthetic checker is a nice, compartmentalized project, it shouldn’t need significant overhead. It should be able to run on virtually anything, and it should be cheap. Rust is of course known for its low-memory and high-speed. What really won me over is this oft-cited table from a [2017 paper](https://greenlab.di.uminho.pt/wp-content/uploads/2017/10/sleFinal.pdf) that describes the relative energy efficiency of programming languages, as measured by a function of execution time and memory consumption:

![A table listing Rust as the most energy-efficient programming languae](/images/language-table.png){:style="display:block; margin-left:auto; margin-right:auto"}

Despite such strength in performance, Rust behaves like a high level language for general http client / server functionality. In some ways it feels like getting a performance boost “for free”.

Given I was building a monitoring tool, and also one that I’m going to release publicly and tie my name to, I also wanted it to be maximally predictable and reliable itself. Generally this is all referenced under the umbrella of “memory safety”, but I have continually found the compiler is protecting me from doing dumb things - for example catching iterator invalidation, or ensuring that Prodzilla’s execution state can be shared across threads.

### Community

Alongside the technical reasons though, I’ve been following and learning Rust for a few years, and I really like the Rust community. It’s hard to ignore Rust repeatedly [topping the StackOverflow developer survey](https://survey.stackoverflow.co/2023/) in languages developers want to use more, or [spicy articles from Discord](https://discord.com/blog/why-discord-is-switching-from-go-to-rust) about the benefits of porting a service from Go to Rust. 

Moreover, I especially like where Rust is right now in the web space. It really feels like there’s a lot of smart people working on the next generation of web development tools - it feels like the place to be. There are a range of great open-source web dev tools that are just reaching critical levels of maturity. [Axum](https://github.com/tokio-rs/axum), which I used to build Prodzilla, feels ready for out of the box web dev, and is crazy-performant, as I write about later. More recently available is [Loco](https://github.com/loco-rs/loco), a Rails-like framework for building web applications Rust that's picking up steam. And in dev-tooling and hosting there’s [Shuttle](https://github.com/shuttle-hq/shuttle), a 1-line hosting solution for Rust backends.

I saw all of this happening and wanted to contribute.

It’s not just open-source tools either; it feels like there is real industry traction here too, with [Microsoft looking for Rust engineers](https://www.theregister.com/2024/01/31/microsoft_seeks_rust_developers/) to rewrite parts of Office 365, and [Meta endorsing Rust internally](https://engineering.fb.com/2022/07/27/developer-tools/programming-languages-endorsed-for-server-side-use-at-meta/) for backend engineering.

## The Learning Curve

Before Prodzilla, I’d read ['The Book'](https://doc.rust-lang.org/book/) a couple of times, and had made my way through Rustlings, but hadn’t yet built a serious project in Rust.

### Faster Than Expected
Knowing the performance and safety benefits of using Rust, I expected to find it much slower than languages like to build a real project in. Surprisingly, across the board I found that I was generally moving fast. Some small syntactic sugar goes a long way - coming from Golang and its verbose error handling, using `?` for error propagation feels amazing.

### Fighting the Borrow Checker - and Calling in an Ally
A sentiment I’ve heard over the years is of developers ‘fighting the borrow checker’. ‘Fighting’ seems too strong a word for my experience. But maybe that’s because - one thing that I haven’t heard enough people mention enough - ChatGPT is pretty good at fighting back! 

On the occasions when I’ve been stuck, I’ve copy-pasted the offending code into ChatGPT 4, along with the error I’ve been getting, and asking for help. And every single time I’ve received thoughtful suggestions for how to navigate around or correctly achieve the desired outcome for cases totally specific to my situation. 
A specific example that came up recently whilst trying to return some probe results through the Prodzilla API. The values were nested in a hashmap, in a read write lock, in application state. I tried to access them like so:

```rust
state.probe_results.read().unwrap().last().cloned()
```
But got back the error `temporary value dropped while borrowed`. Given the complexity of the multiple wrapping types, not getting an instant answer from Google, and that I was still learning Rust, I dropped the offending code into ChatGPT and it corrected it with an explanation right away:

```rust
let lock = state.probe_results.read().unwrap();
lock.last().cloned()
```
I now know that in a situation like this we need to explicitly assign the variable so that the lock lives for the lifetime or scope of that variable - in this case for the function block - otherwise we’re creating a situation where the lock is closed before the result can be returned.

### Fighting the Borrow Checker is a Good Thing

It’s kind of cool, looking at those two lines, I have a much better intrinsic mental sense of how long the memory lives, without allocating anything myself (still not a perfect sense - still learning!).

Given all of this, I’m glad the borrow checker’s been fighting me. I’ve had to be more thoughtful about what memory should live where, and for how long, I’ve had AI help, and as you’ll see in the next section the performance benefits are real!

## The Outcome - Prodzilla
A couple months in of spending time on Prodzilla after work, I’m proud to that Prodzilla currently supports the following features:
- Probing individual endpoints and Stories: chained requests to multiple endpoints, emulating real user flows
- Verifying that response bodies, headers, and status codes meet expectations using operations such as Equals, Contains, etc.
- Passing variables from one step of a Story to another, Github Actions-style, e.g. `${{steps.authenticate.response.body.token}}`
- Automated notifications for probe or story failures via webhooks
- Manually triggering a probe or story via a `/trigger` endpoint
- Retrieving a history of previous behavior for all probes and stories
- OpenTelemetry integration - trace IDs for all probes
- Configuring all of this via yaml - **without writing code**!

### Small Footprint, Lightning Fast
Whilst running, Prodzilla consumes about 9MB of memory. Note that currently probe and story history is stored in memory rather than in a database, so this can inflate when calling sites with large response bodies. In the future I’ll introduce persistent storage.

When testing with 10 multi-step probes being triggered every second (more than I expect anyone is reasonably going to be probing), memory barely moved from that 9MB mark, and at max consumed around 1.5% of my CPU, a crusty old i7-8550U @ 1.80GHz.

The size of the production binary is 7.8MB.

As someone who has worked as a backend engineer for their whole career, across Golang, Kotlin, C# - these numbers are pretty crazy. An empty spring boot application - a point of reference as a very common backend framework - is going to run with somewhere between 100-200MB of RAM. To have a web server and a synthetic monitoring agent running under 10MB - this feels like magic, but also a step in the right direction.

I’d like to spend some time benchmarking Prodzilla properly, and especially comparing it to some of the existing tools, such as Grafana’s synthetic monitoring agent - hopefully a story for another article. 

## Hosting
When building Prodzilla, I had a couple of hosting problems to overcome:
- How can I host a Rust app cheaply for my own use?
- How can my users cheaply host a Rust app that I’ve built?

One of the hardest parts of encouraging usage of open-source tools is the burden users have of hosting it themselves. To get a real backend service up and running, connected to a database, and exposed at some nice url - despite being the backbone of every production application ever - is not a streamlined process. When building and hosting any proof-of-concept or side project, these tasks are a huge time-sink.

### Enter Shuttle
Trying to work out how to overcome this, somewhere along the way I found [Shuttle](https://www.shuttle.rs/), which allows free hosting of Rust applications - great. What surprised me about Shuttle was that without provisioning anything in AWS or GCP, I could deploy an application for free with just slight tweaks to my application code. I ported Prodzilla to deploy on Shuttle in about 5 minutes. [Here’s the diff](https://github.com/prodzilla/prodzilla/compare/main...shuttle).

The core of the change is essentially adding an attribute and a return type to the main function:

```rust
#[shuttle_runtime::main]
async fn main() -> shuttle_axum::ShuttleAxum {
```

I’ve also started experimenting with integrating a database to store probe and story results in Prodzilla. Again, all the local setup is just gone, and with slight tweaks Prodzilla runs locally with a containerized database, or can be deployed to the cloud with a provisioned database:


```rust
#[shuttle_runtime::main]
async fn main(#[shuttle_shared_db::Postgres] pool: PgPool) -> shuttle_axum::ShuttleAxum {
```

This is kind of bizarre for me to acknowledge, but with Rust and Shuttle I could get an app with a database up and running at some real url, and into prod faster than in any other language. It feels like the future. This is what backend development should feel like. Products like Vercel have made frontend development and hosting considerably easier - but on the backend I feel like we’re used to kind of wrestling through cloud platforms, hence why Shuttle is such a nice surprise.

I came into Prodzilla thinking that I’d get some awesome benefits from Rust, but would be slowed down by the language and hosting maturity. I was definitely wrong!

## Vision and Goals

My ultimate goal with Prodzilla is to help fix production observability and testing, in the process helping everyone write more reliable software. 

I don’t think that observability and quality testing should be as divergent as they are, with different parts of the development lifecycle, different codebases, different methodologies. By allowing Prodzilla to easily run in CI/CD pipelines and against different environments it can help tackle that.

I don’t think that system behavior documentation, both internal and customer-facing, should go out of date as quickly as it does. By leveraging Prodzilla’s history of observed behavior, building a feedback loop between documentation and observed behavior, with a bit of LLM magic, I think we can fix that.

I think that we should test in production more, even for cases where it feels too hard. Creating test users alongside production data, supporting canary releasing, flagging test requests to avoid calling specific downstreams - these are all fairly opaque and difficult things at the moment that I think we can make easier.

I know these are big goals - maybe too ambitious, but at least that’s the direction Prodzilla is heading in.

## Conclusion

All up, I've been working on a new synthetic monitoring tool, and I'm glad I decided to build it in Rust. I've found that between the language itself and tools like Shuttle it's a joy to write really performant backend services very quickly!

More than anything else I'm looking forward to feedback from everyone that tries out Prodzilla, and especially from anyone that would love to use it but needs *just that one more specific feature*.

Please get in touch at any of the below:

- [prodzilla.io](https://www.prodzilla.io)
- [Discord](https://discord.gg/ud55NhraUm)
- [Twitter / X](https://x.com/codingupastorm)