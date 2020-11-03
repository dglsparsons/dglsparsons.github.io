---
title: Why effective communication is the key skill for being an awesome programmer
description: Programming is all about communication. And yet, communication is a difficult thing to get right. Here's why it's so important, and how to communicate better.
date: 2020-11-03T12:20:00
layout: layouts/post.njk
cover: /img/remote/writing.jpg
---

Programming is all about communication. We communicate with computers to tell them what we want them to do. We instruct them to carry out tasks to the finest degree of accuracy. We communicate with our peers, sharing ideas and technology. We communicate with business owners, gathering requirements and context on problems. And yet, communication is a difficult thing to get right.

Have you ever tried to write a blog, teach someone about a topic, or write an email on a difficult topic?

If you have, then you will know how challenging precise communication can be.

## 🗣️ Why is communication so hard

When programming, all our communication immediately becomes at least twice as difficult. We write programs that have to be understood by the computer so they can run. But, they also have to be understood by future readers: people editing or maintaining them. They also have to capture exact requirements with little or no room for error. These requirements typically come from communication with non-programmers.

At the very least this means all programs are acting on around four levels of communication:
1. 🗣️ A non-technical individual communicating to a developer.
2. 💻 A developer communicating his intent to an application.
3. 🤖 The application running and communicating to the computer.
4. 🐛 Another developer looking to extend or debug the application.

Bugs, misunderstandings and issues in translation can occur at any, or all, of these layers. Writing software becomes a game of Chinese whispers.

## 🙊 Chinese whispers

![Whispering](/img/remote/whisper.jpg)

This game becomes even bigger and more error-prone as you scale up teams, or have open-source contributors. The more programmers working on a codebase, the more miscommunication and misunderstanding there can be.

This communication is even more challenging because of the huge variation in what programs can do. A web application is concerned with attractively arranging pixels on a screen. A finance application might need complex calculations to be exact to the penny/cent. A device driver might care about manipulating bespoke hardware to certain voltages. The range is huge, and this complexity is part of the reason programming can be so overwhelming.

## 🤷 Ambiguity

Communication is also inherently hard because of how language works. In everyday speech, there are multiple ways to interpret any sentence. These ways are often dependent on context or our environments. And then, there are multiple ways to represent everything as code. There's an old joke that illustrates this well.

![Milk](/img/remote/milk.jpg)

> A software engineer gets sent to the shops by his wife.
> She tells him "Go and get a pint of milk, and if they have eggs get six".
> The engineer disappears and returns an hour later with six pints of milk.
> “Why on earth did you get six pints of milk!?” His wife asks, furious.
> He confidently replies "they had eggs!".


## 💥 So what can we do?

We've identified that communication can act as Chinese whispers. Language can be ambiguous, and we have many different points of communication in our day-to-day working. As a result, the quality of any communication is of critical importance. An individual who is capable of communicating effectively with non-technical people, other programmers and with a computer is invaluable.

It's important to remember how we communicate. When thinking of communication my mind always jumps straight to two people talking, or a board room full of people wearing suits.

![Meeting](/img/remote/meeting.jpg)

However, this is rarely the case. We communicate far more frequently in our work, and talking isn't the only way we communicate. Talking often isn't even the best way to communicate.

There are four topics we communicate about and four ways we usually communicate these:
1. Code - we communicate through reading and writing code
2. Architecture - typically represented both in code and in diagrams.
3. Requirements - these are usually gathered through conversations or user stories.
4. Deadlines - usually through conversations or calendar events.

For code. Having clear code, that is easy to understand and digest makes life much easier. It frees up our brains to solve problems, rather than focusing on the minutiae of how an application works. Just like Donald Knuth, I believe that "programs are meant to be read by humans and only incidentally for computers to execute". When writing code, optimising for readability should *always* be our main focus. A well-structured, well-written codebase is far less prone to misinterpretation. Far easier to digest, and much more enjoyable to work with.

For architecture, we must clearly define and delineate any systems. Everything should have a clear purpose and a single responsibility. It should also be clear how these systems communicate. This should all be easy to digest (via diagrams, sensible naming, clear purpose and sensible code structure).

For requirements, we must extract every degree of detail possible. Clarify on everything, even points that seem obvious. Make sure they are all written down and that a common understanding is reached across the team.

Finally, deadlines. You might think these are unambiguous: "have X done by Y date". It seems straightforward, but remember that many "Agile" teams have to define what 'done' really means. Does it mean code complete? Tested? Deployed? Handed over to a different team? All the above and defects fixed?


## ✨ In Conclusion

Communication is challenging. Everyday language is ambiguous: we can often interpret very different meanings from the same sentence. As teams grow, our points of communication grow with them, and, even in small teams, several layers of communication take place. Mis-interpretation or a poor understanding can sneak in at any of these layers.

As a result, we have to focus continually on communicating effectively. Having good quality code that is clear in purpose and easy to read and understand is essential. We need to keep our systems organised as neatly as possible, with clear diagrams and purpose. Requirements and deadlines need constant questioning, and every assumption challenged. Through doing all this, we can communicate more effectively, and be awesome programmers as a result.
