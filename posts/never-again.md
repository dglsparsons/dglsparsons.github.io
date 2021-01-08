---
title: 3 popular technologies that I will strive to never use again
description: There are a lot of different technologies out there. More than anyone could possibly explore. The de-facto position is to look for technologies that are popular, following the logic that “if enough people like it, it must be good”. In my experience, this isn’t always the case though.
date: 2021-01-08T14:45:00
layout: layouts/post.njk
cover: /img/remote/dark_code.jpg
---

There are a lot of different technologies out there. More than anyone could possibly explore. The de-facto position is to look for technologies that are popular, following the logic that “if enough people like it, it must be good”.

In my experience, this isn’t always the case though.

Throughout my career, I’ve worked across a wide range of technologies. While I’ve grown to love some of them, at times I felt like I was fighting a losing battle and spending more time scrapping with technology rather than solving real-world problems. Whenever this is the case, I make a mental note to avoid that technology in future.

Here are the three I found the most painful to use.

## 1) Django ⏰

Django is a hugely popular framework for developing websites using a model-template-view architectural pattern. It’s amazing for getting something done very quickly. It also comes with a fantastically powerful Object Relational Mapper (ORM). So what’s the problem?

_ Its opinions are too strong!_

If you’re working just with a relational database, and only want model-template-view, and you’re completely fine with containerising and managing your own scaling, then it may be completely fine. When I used it we were updating our architecture though. Our entire workload wasn’t suited to a relational database and we wanted to take more benefit from auto-scaling, lambdas and managed services in AWS. We were also looking to use React to build more complete experiences for our UI. This became a real break-away from the Django core use-cases. As time went on, we began fighting Django’s opinions more and more. Every change became a battle, digging through the documentation and hacking in workarounds. Over time the codebase became scrappier and scrappier.

Django didn’t feel like the right tool for us. It felt messy and difficult to avoid. If used in its particular setting, it’s probably fine, but it’s not for me.

## 2) Scala 🐌

Scala is designed to avoid the pitfalls of Java. It runs on the JVM, allows interop with existing Java code, yet provides a much stronger type system and enables functional paradigms.

Despite being in many ways an obvious improvement over Java, my experience with the language was not great. There were two main pitfalls I encountered: quirky behaviour and tooling.

As Scala derives from Java but mixes in functional paradigms, the language contains a huge amount. While being a strength in some ways, parts of Scala have some bizarre hangovers. We’ve all [mocked JavaScript](https://www.destroyallsoftware.com/talks/wat) for some of its bizarre and unexpected behaviour in code that looks correct. Scala often takes this to another level. It’s easy to slip up when writing code, and unless you know the language incredibly well, need to constantly be on the lookout for [common pitfalls](https://nrinaudo.github.io/scala-best-practices/) (or set up a linter for these).

The second issue I faced with the language was really poor tooling:
Getting support in vim (my editor of choice) was a nightmare,
Automatically formatting code was far more difficult than I expected,
Finding a lightweight logger that didn’t require a thousand line XML file was impossible,
Setting up linting rules for all the common language quirks was an exercise in finding unmaintained repositories and configuring XML,
And, finally, once everything finally worked together, I was stuck battling slow compile times and integration tests that were slower than intercontinental drift.

For a popular language, I was really surprised. There were bits I loved, but overall the experience was akin to pulling teeth. Not one I’d go in for again!

## 3) Segment 💰

Last year, we stumbled across [Segment](https://segment.com/). A fantastic looking tool that drastically reduces the amount of tracking code you need to write. It allows seamless integration into a plethora of different services and seemed to match up precisely with what we wanted. Pricing wise there’s a generous free tier (1,000 Monthly Active Users), and competitive pricing outside of this, or so we thought... Reading around on the internet, it was generally well-received, so we thought we’d give it a try.

Plugging it into our website was a straightforward experience, and it appeared to deliver on all its promises. We soon had all our events being fired into Segment and tracked across a range of different tools. Brilliant. We put it live.

The next day, we had a sudden spike in traffic caused by a third-party accidentally scraping us repeatedly. Oops. While the third-party resolved the issue on their site, I logged back in to find out how our Monthly Active User (MAU) allowance had been affected.

_It had been completely demolished_

Not only had we overshot our MAU limit in a matter of hours, but we’d racked up a substantial bill. It turns out every single anonymous visit to our website was being counted as an additional MAU. Pricing per MAU meant overshooting our limit was expensive. We had to pull the ripcord and tear it out immediately.

One expensive bill later, that’s a mistake I don’t want to make again.


## Closing thoughts 💭

These three technologies have scarred me as a developer. Having had to fight through difficult situations with each one of them, I know I’ll strive to avoid them in the future. In my experience, Django, Scala and Segment just aren't worth the pain.

***

Do you have any technologies you feel the same about? Feel like I’ve missed some out? If so, what are they?

***

Think I’m wrong about any of these? Get in touch and let me know.
