---
title: 'Tolerances: How Formula 1 pistons can teach us to be better developers'
description: Why do Formula 1 car pistons cost around 1,000 times the amount of a typical road car piston and what can this teach us about programming?
date: 2021-01-18T11:00:00
layout: layouts/post.njk
cover: /img/remote/pistons.jpg
---

Recently, while procrastinating from work, I found myself watching a youtube video on [Formula 1 pistons](https://www.youtube.com/watch?v=lCEKJxHiEIM).

This video seeks to answer the question: why does a Formula 1 car piston cost around 1,000 times the amount of a typical road car (£50,000 vs £50), despite appearing very similar and serving the same purpose?

![Whispering](/img/remote/pistons.jpg)

*Left: piston from an American muscle car. Right: piston from a formula 1 car.*

So, what does this teach us about programming, and how to be a better developer?

## The cost of immaculate tolerances 💰

Manufacturing a car piston is a game of playing with tolerances.

The piston itself has to fit into a cylinder block snugly, with a small gap around the sides to allow it to move freely. If the gap between a piston and the cylinder is too big, then burning fuel can escape, and the piston is far less efficient. If the gap is too small, then the piston can seize up, blocking the engine completely.

![Piston Cycle](/img/remote/piston-cycle.jpg)
*The process a car piston goes through in a single cycle*

This adds some serious complexity for building these parts. Not only do they have to be designed to an incredible specification, but the margin for error when manufacturing these parts is so small that they require specialised machinery and special processes to make sure they are fit for purpose.

Rather than immaculate tolerances, what if we wanted to be able to guarantee the quality of code we write as programmers? Imagine if a single bug in your code would be a catastrophic failure or even a risk to life. Would that change the way you write code? Would it change the way you review and test code? And how do you think that would affect the speed at which you develop?

No doubt development would be much slower. Just like the creation of pistons for Formula 1, the process would have to become far more thorough. You would need specific tools and processes to guarantee the right results. You would have to change the way you approach writing software to ensure it behaved exactly as you wanted in every possible situation. And, no doubt, the overall cost of developing software would become much greater as a result of these changes.

When we write software, we have to bear this situation in mind. It’s easy to claim that everything should be of the highest possible quality, but there is a cost for achieving that. Instead, the bar for quality needs to appropriate for the task at hand. Set your tolerances too small, and development becomes slow and expensive. Set them too low, and you end up with inefficient results.

## Regular oiling and refactoring 🛢️

In a typical road car, pistons require a bit of help to slide up and down inside their cylinder block: metal doesn’t slide particularly well over other metal, so a layer of oil makes the whole process much easier. In new cars, this oil is a very thin, watery liquid thanks to a solid manufacturing process of car engines. In classic cars, oil can be thick, black gunge - the difference being the gap between the piston and the cylinder block is much larger, so the oil needs to be much thicker to work across the gap. As Formula 1 cars have such minuscule tolerances, their pistons require no oil at all.

Now, imagine oiling a car as being like refactoring code.

What does that mean? Take an old banger of a codebase, and you’ll find yourself having to change the oil regularly. Changing the oil is a messy process that really involves rolling your sleeves up and diving in. It can take time to do, and by the time you’ve managed to do a big refactor, you’re sure everything will be running much smoother as a result, but you find you’re worn out and covered in bits of greasy old oil.

What about a modern codebase? The oil here is much thinner: development practices have got a lot better and technology has really come a long way. As a result, we don’t have to spend nearly as much time under the bonnet. This is still an important part of our work, though. Much as a car needs a regular oil change, codebases need a regular touch up to keep them running. Don’t keep on top of this maintenance, and you never know when it’ll catch up to you with a bang.

Finally, imagine our immaculate codebase from earlier, where quality and lack of bugs trumps all. This is the Formula 1 car of the software world. There’s no need for oil, so there’s no need for oil changes. If all the code here is perfect the first time around, why would we ever need to refactor?

## Closing thoughts 💭

If we think of the two practises of managing tolerances or quality, and refactoring code as linked, then we draw some nice conclusions.

Focussing on tolerances and quality the first time around can lead to a huge increase in upfront cost, both in terms of time and effort. This can net you some neat benefits though. Not only can you guarantee the codebase as bug-free and brilliant, but you end up with code that needs no further work. There’s little to no maintenance cost, and you’ll never find yourself refactoring.

Conversely, a focus on the low-cost, fast-paced development necessitates a lower tolerance for quality. This can be useful for getting a project into the world, but comes with a cost later down the line: you need regular oil changes and refactoring to keep everything neat, or you risk blowing up with a bang.

In reality, most codebases lie between these two extremes. Rather than focussing solely on one extreme or the other, we need to be mindful of a balance. Insisting on quality over all else is a tradeoff, and at times isn’t the right one to make. Equally, we need to be careful of the costs down the line in cutting upfront quality. Next time you review a pull-request or submit a new feature, think of this tradeoff: do we mind oiling this a bit later, or do we want an immaculate Formula 1 piston right now?

