---
layout:     post
title:      Reducing Exploitation of Migrant Workers via the Blockchain — Handshake.tech
date:       2017-09-26
summary:    Using smart contracts to protect migrant workers from exploitation.
categories: 
---

![A migrant worker](/images/handshake.png)

Everyone loves a good discussion about how the [blockchain](https://hackernoon.com/tagged/blockchain) is going to disrupt industries such as finance, how it will overhaul citizens’ interactions with governments, and how you should invest in their ICO to give money to their newly-formed corporate. Interestingly however, blockchains also have the ability to improve the lives of vulnerable populations, through various means such as giving them a [tangible identity](http://fortune.com/2017/06/19/id2020-blockchain-microsoft/), and [empowering entrepreneurs in poorer countries](https://hbr.org/2017/05/how-blockchain-could-help-emerging-markets-leap-ahead). Having recognized the potential for significant social changes, [ConsenSys](undefined) have organised the [Blockchain for Social Impact Hackathon](https://www.blockchainforsocialimpact.com/hackathon/).

Our team, [Handshake](http://handshake.tech/), has entered and is working to improve the transparency of the recruitment process for migrant workers, by enabling migrant employees to sign verified labor contracts from reputable recruitment agencies and employers on the Ethereum blockchain. This article provides a brief introduction to some of the issues faced by migrant workers, and discusses why blockchains provide a great platform to solve them.

## The Problem

The economic systems of countries like the Philippines are reliant upon [significant portions of their population travelling overseas for work](https://migranteinternational.org/2017/06/30/suma-2017-a-year-of-big-talk-band-aids-and-business-as-usual-for-ofws-and-families/) and sending money home. For workers in the Philippines in particular, the majority of this work is in low-medium skilled roles such as domestic service, nursing and electrical work, and primarily in a few countries including Saudi Arabia, UAE, Singapore, Hong Kong and Qatar ([source](https://centerformigrantadvocacy.com/history-of-philippine-migration/)).

None of this is problematic in itself, but the recruitment process for these migrant workers is extremely convoluted and often workers end up in the destination country, working in conditions and for wages [very different to what they initially agreed upon](https://www.usnews.com/news/best-countries/articles/2017-07-10/uae-is-no-paradise-for-migrant-workers). They don’t have access to the contract or conditions that they signed, are unsure how to get help, and [may be coerced into signing new contracts with less favorable terms](https://centerformigrantadvocacy.com/history-of-philippine-migration/).

## Enter Blockchain

This is an introduction to how blockchain can improve transparency and accountability of corporate entities in just one section of the recruitment process. Ultimately, the end-to-end process that Handshake is working on will begin with the work order at the employer-level, and protect the worker all the way until the end of the process when they’re ready to start sending remittances home.

When contracts are on paper, or even communicated only verbally, there is a high risk of forgery and exploitation of workers. What Handshake will allow is is the storage of legal documents such as labor contracts in a tokenized form on a decentralized service: [IPFS](https://ipfs.io/). For example, this contract:

![1st page of an actual contract given to the team by StaffHouse.](https://cdn-images-1.medium.com/max/2480/1*HzcBsyXtR80fmsL0ju3UHA.jpeg)

Can be transformed into JSON (a manual process for now):

    {
      employer: {
        name: "Saudi Arabia Employment Company",
        uPortId: "0x99618818fd5cc2d9ed6543f973ee54859bb79df7"
      },
      location: {
        country: "Saudi Arabia",
        city: "Riyadh",
        streetAddress: "123 Main St"
      },
      laborContract: {
        durationMonths: 24,
        hoursPerDay: 8,
        daysPerWeek: 5,
        position: "Domestic assistant"
        description: "A brief description of what the role will entail.",
        monthlySalary: 2300,
        currency: "USD",
        overtimeMultiplier: 1.5,
        vacationLeaveDays: 21,
        sickLeaveDays: 10,
        terminationNoticeDays: 15,
        employerPaysVisa: true,
        transportToWorkIncluded: true,
        transportFromWorkIncluded: true,
        housingIncluded: false,
        foodIncluded: false,
        emergencyMedicalIncluded: true
      }
    }

This simple,tokenized structure will be stored on IPFS and its hash will be pushed to an Ethereum smart contract. The end result of this process is that the labor contract is now immutable— its details can’t be changed (as would otherwise happen sometimes in the movement of the contract).

When potential employees go to sign the contract via Handshake, they will be able to view the terms and conditions, and rest assured that the details of what they sign can’t be changed after the fact. They can also view the identities of the agency and employer, see their accreditation, their previous reputation for handling employees, and know with confidence that they will arrive at the employer specified in the contract.

To handle the tracking and verification of the corporate and individual identities of all the actors in the process, Handshake will use [uPort](https://www.uport.me/).

This will allow for several important features:

* Regulatory bodies can attest that recruitment agencies have the credentials they proclaim to have.

* Potential employees can research the companies they’re planning to work for, via uPort’s 2 way binding of social media profiles.

* A reliable reputation-based system for tracking the ethical behavior of recruitment agencies or employers.

Ultimately, this small interaction between recruiter and employee is just one piece of the puzzle. As mentioned earlier, Handshake will utilise properties of the blockchain to increase transparency all the way along the recruitment process.

## Get In Touch

We’re already working with some prevalent organisations in the industry, including the WHO and StaffHouse in the Philippines. If you’re a forward-thinking, [technology](https://hackernoon.com/tagged/technology)-first, ethical recruitment agency or employer, and you want to be able to verify your compliance publicly, and ensure a safer employment experience for your workers, hit us up at [handshake.tech](http://handshake.tech/index.html) or contact me directly here or on Twitter: @codingupastorm!
