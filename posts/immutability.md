---
title: How to write IMMUTABLE code and never get stuck debugging again
description: Lets look how walruses eating cheese can explain how immutability works, why you should care, and why the counterarguments against immutable code aren't worth considering.
date: 2020-11-17T15:20:00
layout: layouts/post.njk
cover: /img/remote/walrus.jpg
---

I've written production code in a variety of different languages throughout my career, including Haskell, Scala, Go, Python, Java or JavaScript. While each language has its own clear benefits, working as a polyglot across a range of different paradigms has changed the way I write code. Certain skills and concepts are transferable regardless of the language being written. I believe immutability is one of these key concepts. By writing immutable code it is possible to make programs easier to reason about, easier to write and easier to debug.

Here, we’ll look at three things:
- how walruses eating cheese can explain how immutability works,
- why you should care, and
- why the counterarguments against immutable code aren’t worth considering.

## What is immutability?

> “unchanging over time or unable to be changed.” - Oxford Languages definition.

Immutability is the idea that once an object or variable has been created, its value should never change or be updated by anything. For objects or classes, this also includes any fields; literally, nothing should change! The object is effectively read-only.

Writing code in this style requires a mindset shift at times though. The first time I came across the idea, it made absolutely no sense to me and seemed insane. I was confused and wanted to immediately unpick it all, writing it in a way I was familiar with. Gary Bernhardt, in his [talk on boundaries](https://www.destroyallsoftware.com/talks/boundaries), gives a fantastic example of why it feels so wrong.

He talks about feeding walruses cheese.

![Walrus](/img/remote/walrus.jpg)

In a mutable version, we might instruct each walrus to eat some cheese. This cheese then gets added to the contents of their stomach. Makes a lot of sense, right?

In an immutable version, we have to perform a mind-bending operation. To feed the walruses we would have to:
- create a brand new stomach that’s the same as the old stomach, but with some cheese in it.
- Then, create a new walrus that’s the same as the old walrus, except, with the stomach replaced.
- Then, throw away all the old walrus.

At first glance, this sounds bonkers but stay with me - let’s look at what makes writing code like this worthwhile.

## How does it prevent pain when debugging?

Have you ever encountered:
- `undefined is not a function` in JavaScript?
- `NullPointerException`s in Java?
- `SegFault` in C/C++?
- `panic` in Go?
- `NoneType has no attribute foo` in Python?

If you’ve worked in any of these languages, then chances are you probably have. The thing is, all of these errors are caused by the same thing: **missing, or null, data.**

Missing data and null values are definitely among the most difficult types of bugs to track down and fix. I’ve spent countless hours in the past sifting through JavaScript code trying to figure out why the value I thought should be there, wasn’t. Why my application suddenly crashed when everything seemed to be going fine. Sir Tony Hoare even describes null as [“The Billion Dollar Mistake”](https://qconlondon.com/london-2009/qconlondon.com/london-2009/speaker/Tony+Hoare.html) because of the countless bugs, security vulnerabilities and crashes that have resulted from it.

**Let’s just agree: nulls can be evil.**

The reason these bugs are so hard to hunt down and to fix is that the effect (the exception) is far away from the cause (the introduction of null). Actually throwing a null pointer error happens some arbitrary amount of time after we introduce a null, and we get `undefined` errors accessing a property miles away from where we thought the property was set. Debugging becomes a case of reading carefully back through code until we find the cause.

The more state changes that happen in code, the more places these bugs can be introduced. Instead, we can attempt to reduce the surface area of any code. The fewer mutations in a codebase, the less surface area there is for bugs. This leads to fewer bugs.

If you only ever set a value once, there’s only one place that value can be faulty. If you make changes to an object as it gets passed around, any one of those places could introduce potential issues. If one of our walruses is faulty, we know it can only have happened when we made the latest walrus, complete with the new stomach. It can’t be an issue with an earlier walrus - they are long gone.

So really, immutability, or, never changing a value, really saves us from getting stuck debugging.

## Why performance isn’t a concern

Some eagle-eyed people might be thinking “those walruses earlier… isn’t throwing them all in the bin and making new ones pretty expensive? Won’t it make my code slow?”.

The answer isn’t simple.

You’re right in saying that throwing away walruses all the time isn’t totally necessary, and it can make things the tiniest amount slower sometimes. The keyword being sometimes here though. Sometimes compilers are clever enough to optimise this behaviour with something more efficient. Some languages even [prefer immutability by default](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html). Immutability also has great benefits when it comes to multi-threading or parallelisation, as it allows lock-free sharing, knowing that values [won’t be changed](https://softwareengineering.stackexchange.com/questions/171253/does-immutability-entirely-eliminate-the-need-for-locks-in-multi-processor-progr).

Despite all this, even if creating new walruses is slower in the language you use, the cost of allocating a new object is almost certainly minuscule compared to anything else within an application. Unless you are benchmarking and actively measuring performance, then you almost certainly shouldn’t care.

## Conclusion

Immutability is a powerful tool when programming. It allows us to write code that is easier to debug and reason about. It requires a bit of a mindset shift, but in my experience, it’s definitely worth making the mental leap.

Give it a go, and let me know what you think :).

---

Looking for other ways to improve the clarity of your code? Why not check out my [post on never using else statements](https://dgls.dev/posts/else-statement/).

---

Enjoyed this post? Want to share your thoughts on the matter? Found this article helpful? Disagree with me? Let me know by [messaging me on Twitter](twitter.com/dglsparsons).
