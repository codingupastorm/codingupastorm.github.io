---
layout:     post
title:      The Importance of Modularity in a Blockchain Platform
date:       2019-01-16
summary:    Why modularity is important when building a framework for an emerging technology.
categories: 
---

![Project Ara: an idea for a modular smartphone.](/images/modularity.png)

Modularity refers to a system’s ability to be broken down into components which can be separated and recombined. The best software development tools are often highly modular, and allow the reuse of “assemblies” or “modules”. This principle is at the core of development frameworks like Java, C# and Go.

Modularity promotes innovation because new ideas become trivial to implement. When creating a new web app, the majority of the code running is really inside of externally-developed packages; whether they be database connections, page rendering engines, input validators etc. As such, it’s extremely easy for developers to spin up a prototype of a new website idea.

## Enter: Crypto

However, if you were to go and look at the most prized crypto codebase of all, the [Bitcoin Core](https://github.com/bitcoin/bitcoin) repository, you’d notice that it’s extremely inflexible. Unmodular code is generally characterised by long files and classes, and individual implementations of classes often depend on other concrete implementations. For an example see [Bitcoin](https://hackernoon.com/tagged/bitcoin) Core’s [validation.cpp](https://github.com/bitcoin/bitcoin/blob/82ffd4d91832c275f791a17f697534cc677c89fd/src/validation.cpp). This isn’t just the case with Bitcoin Core. Most of the biggest [blockchain](https://hackernoon.com/tagged/blockchain) node repositories — another example is [Go Ethereum](https://github.com/ethereum/go-ethereum)- are absolutely not built with flexibility in mind.

Now it’s important to note that in the case of Bitcoin, modularity isn’t a priority. If you believe you’re building the *singular* “currency” of the future, why make it easier for others to build with your code? Bitcoin Core is highly integrated, but it has stood the test of time and it *works*. This is not a criticism of Bitcoin Core’s codebase.

## Experimenting with Blockchains

With blockchain being cited as the answer to everything, many projects are starting to experiment with different node implementations. Especially in the case of sensitive data and private chains, or cases where external data is core to a chain’s operation, projects are finding that a smart contract based approach does not allow them enough flexibility. Unfortunately until recently this has meant that in order to innovate, developers are pulling apart integrated nodes like Bitcoin’s, or starting new nodes from scratch.

Both of these avenues are painful, time-consuming, and error-prone (insecure).

This space is really going to thrive when a blockchain network with a completely new set of features can be spun up *clicks fingers* *like that*.

## Stratis — A Modular Platform

The most exciting thing about Stratis for me is we’re thinking about building blockchains radically differently. Different consensus algorithms, smart contract executors, wallets, and two-way peg implementations are all features in the one codebase.

Currently you can run — all from the one codebase:

* A Bitcoin (PoW) node

* A Stratis (PoS) node

* A Cirrus (PoA + smart contract) node.

Moreover, because of how flexible the node is, I’m confident we could build out full node integrations for Bitcoin Cash, Gold, Private, Doge, Litecoin, etc. in *days *each. Down the road we could extend this to support even more chain types easily, after some foray into the account model and other features.

When building a new website in .NET Core, you can preload a template and adjust the components to your needs rapidly, punching out a new prototype web app in under a day. We hope that with Stratis developers will be able to do the same but for their own blockchain networks.

    IFullNode node = new FullNodeBuilder()

        .UseNodeSettings(nodeSettings)

        .UseBlockStore()

        .UseMempool()

        .AddRPC()

        .AddSmartContracts()

        .UseCLRExecutor()

        .UseApi()

        .Build();

Isn’t it beautiful? Our [Full Node repo is here](https://github.com/stratisproject/StratisBitcoinFullNode).

## In Conclusion

Modularity == Innovation.

I don’t think anyone is as far ahead as we are when it comes to a strong composable approach to building blockchain networks.

To find out more follow us on Twitter: [https://twitter.com/stratisplatform](https://twitter.com/stratisplatform)

Or if you just want to wave: [https://twitter.com/codingupastorm](https://twitter.com/codingupastorm)