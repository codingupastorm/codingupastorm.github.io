---
layout:     post
title:      How I'm Getting Free Synthetic Monitoring
date:       2023-11-07
summary:    A tutorial for using open-source Rust - Prodzilla and Shuttle - to get synthetic checks for free!
categories: 
---

I'm using a couple of open-source and Rusty tools to get free synthetic monitoring for my side projects. It's really easy - maybe it will help you!

## Why Synthetic Monitoring?

Synthetic monitoring is a proactive approach to web application monitoring, whereby we fire real requests at real endpoints to simulate user interactions. It allows for consistent testing of website availability and behaviour, and is particularly useful for identifying and resolving issues before they impact real users!

## The Tools

[Prodzilla](https://github.com/prodzilla/prodzilla) is a new open-source synthetic monitoring application built in Rust. At present it allows defining probes that will call defined endpoints on a defined schedule, and assert that the responses meet expectations. When they don't, Prodzilla currently has the ability to alert by sending a webhook to a given URL. No need for any special setup to use it, so long as you have Rust installed. **Full disclosure**: _I built it!_

[Shuttle](https://www.shuttle.rs) is a great, quick, hosting service for Rust applications. Importantly it's also currently free, and guarantees a forever free tier. Shuttle makes it extremely easy to deploy a live Rust application fast. For this tutorial you'll need to create an account at [shuttle.rs](https://www.shuttle.rs) and then install the Shuttle CLI with:

```
cargo install cargo-shuttle
```

## The Process

### Configuring Prodzilla

Prodzilla has [a specific branch ready to launch with Shuttle](https://github.com/Prodzilla/prodzilla/tree/shuttle), so clone that. If you're curious about the structural differences between this and a standard Rust application, the specific changes required to migrate are outlined in '[Migrating to Shuttle](https://docs.shuttle.rs/migration/migrating-to-shuttle)'.

To get your local prodzilla ready to deploy, we'll need to customise a couple of things. 

Firstly, you'll want to customise your *prodzilla.yml*. You likely want something like the below, substituting in the site you want to probe. This will probe a site is up and returning a HTTP 200 OK every 5 minutes (300 seconds), starting from 5 seconds after Prodzilla starts up. 

```yml
probes:
  - name: Your Probe
    url: https://yourwebsite.com/endpoint
    http_method: GET
    expectations:
      - field: StatusCode
        operation: Equals 
        value: "200"
    schedule:
      initial_delay: 5
      interval: 300
```

The other fields are mostly self-explanatory, but you can also include expectations about *Body*, and can also use the operations *Contains* or *IsOneOf* (using pipe separated values).

If you need to add auth, Prodzilla currently supports auth using Bearer tokens in headers, though you would need a long-lived token with the current version of Prodzilla.

```yml
probes:
  - name: Your Probe
    ...
    headers:
        Authorization: Bearer <token>
```

If you'd like some system to receive a signal when one of your probes fails, you can add a webhook receiver URL to your probe as well:

```yml
probes:
  - name: Your Probe
    ...
    alerts:
      - url: https://alertme.site/webhook
```

### Deploying to Shuttle

The last thing we need to do in code before deploying is to set the Shuttle project name, in *Shuttle.toml*. Choose a unique name:

```
name = "my-prodzilla"

```

And now we just need to send it! Which we can do by executing the following commands. Note that shuttle will complain if you haven't committed in git, so do that or bypass it by adding the *--allow-dirty* flag.

```
cargo shuttle project start
cargo shuttle project deploy
```

And voila! If you navigate to *https://{name}.shuttleapp.rs* you should see a Roar! And if you navigate to *https://{name}.shuttleapp.rs/probe_results* you should see a json output of the results of your probes, which are stored in memory.

## Get in Touch

I hope that's been helpful - would love to hear any feedback. Happy roaring!

[X / Twitter: @codingupastorm](https://x.com/codingupastorm).

[LinkedIn](https://www.linkedin.com/in/jordandrews/).

[prodzilla.io](https://prodzilla.io).


