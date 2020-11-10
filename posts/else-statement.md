---
title: Write better code and be a better programmer by NEVER USING ELSE statements
description: "I've written production code in a huge variety of languages, through Haskell, Scala, Go, Python, Java or JavaScript. Across all these, I noticed one significant trend: I pretty much never use the ELSE statement. Here's why."
date: 2020-11-10T13:20:00
layout: layouts/post.njk
cover: /img/remote/thinking.webp
---

I've been a professional programmer for the last several years. During this time I've risen rapidly through the ranks. I started as an intern, but I'm now the Lead Engineer responsible for a suite of products that serve over 160,000 people in over 140 different countries.

Recently, I took a look back across all the code I've written during these years (that I still have access to). I've written production code in a huge variety of languages, through Haskell, Scala, Go, Python, Java or Javascript. Across all these, I noticed one significant trend: I pretty much never use the ELSE statement.

I realised there's a clear rationale behind my aversion to else statements though. I believe that they shouldn't be used, and should be treated as a code smell instead. There are two reasons I think this: else statements break the line-of-sight rule, and they always lack context. I'll explain these two points in detail before showing how you can avoid using else statements.

## Line of Sight rule

I'm a firm believer that code should be optimised to be read by people in the future, rather than being optimised for being executed by machines. In this, I echo Donald Knuth's sentiment:

> “Programs are meant to be read by humans and only incidentally for computers to execute.” - Donald Knuth, The Art of Computer Programming.

The problem is the ability to read code is subjective: it's hard to define exactly what makes code readable. One rule that seeks to clarify this though, is the line-of-sight rule. This is a popular rule in the Go community. [Mat Ryer](https://medium.com/@matryer/line-of-sight-in-code-186dd7cdea88) defines it concisely in his talk and article. Simply stated, this is the idea that the ‘happy path' in code should be indented as little as possible.

![Happy path](/img/remote/happy-path.png)

Contrastingly, any error handling or special case code should be indented further.

![Special case](/img/remote/special-case.png)

Any code that follows this has a unique property: scanning the least indented code is sufficient to understand what any piece of code is doing. Scanning the more indented code shows all the special cases and errors that can occur. This makes it super easy to understand at just a glance.

So how do else statements relate to this?

Else statements are problematic as they force code down a level of indentation. It suddenly becomes unclear what code relates to a ‘happy path', and what a special case really is.

![Unclear](/img/remote/unclear-else.png)

This lack of clarity makes the code harder to scan through, and hurts the readability.

## Lack of Context

The ability to quickly and efficiently scan code is super important. Digesting small sections of code in isolation is a key part of this. We don't want to always have to read every line of code to understand a small part of a codebase.

Else statements make this harder as they space out the `if` condition and the code that is affected by it. This is best explained through two examples. First, can you tell what happens when these three lines of code are run?
```go
if myVariable == nil {
  return “”
}
```

Hopefully, this is fairly obvious. Let's take a contrasting example though:
```go
} else {
  return “”
}
```
We can see that without the `if` statement, we can't determine what this is meant to be doing. Why would it return an empty string? Is this an error, or the ‘normal' behaviour? This code instead relies on us remembering, and having read, the earlier context. This doesn't matter much when the statements are small, but if there's complicated logic within the `if { … }` block or we are scanning quickly, then the separation of context from code can hurt readability massively. It hurts even more when if/else statements are nested, or there are multiple of them in one function (which if statement is this else for?).

## How to remove else statements?

Now we've agreed that else statements are rubbish. But that's not much help by itself. The real trick is how to avoid them. Thankfully, there are two simple ways to do this:
- Inverting the `if` condition and returning early, and,
- Creating helper functions.

## Inverting the condition

This is the most common instance I come across. It can take two forms too - one where the `else` is implicit, one where it is explicit. The explicit version looks like the following:
```go
func doSomething() error {
  if something.OK() {
    err := something.Do()
    if err != nil {
      return err
    }
  } else {
    return nil, errors.New("something isn't ok")
  }
}
```
The implicit is similar, but without containing an `else` statement per se. Instead, the `else` is implied by simply dropping off the end of the function (this one is more common in Python or JavaScript, where `None` or `undefined` are returned if nothing is explicitly stated).
```javascript
function doSomething() {
  if (something.OK()) {
    return something.Do()
  }
}
```

Again, this isn't super clear what the full extent of the behaviour is. Without reading the whole function, the return values aren't clear.

By simply inverting the `if` condition, we can solve all these problems though.

```javascript
function doSomething() {
  if (!something.OK()) {
    // return or throw error
  }
  return something.Do()
}
```

We can now scan this function and clearly see the indented error condition and normal flow, satisfying the line-of-sight rule. The behaviour is fully explicit, and we have no separation of context. This is much better.

## Helper Functions

We also get else statements that don't directly result in a `return`. This is usually through some special-case logic that isn't isolated properly. For example
```javascript
  let charities
  if (country != "") {
    if (tier != "") {
      charities = getCharitiesByCampaignCountryAndTier(campaign, country, tier)
    } else {
      charities = getCharitiesByCampaignAndCountry(campaign, country)
    }
  } else {
    charities = getCharitiesByCampaign(campaign)
  }

  // do something with charities
```

The readability of this can be improved by pulling the charity-getting logic into its own function. This then lets the special cases be handled appropriately, and return early. By inverting some of the if statements, this can be improved further.

For example:
```javascript
function getCharities(campaign, country, tier) {
  if (country == "") {
    return getCharitiesByCampaign(campaign)
  }

  if (tier == "") {
    return getCharitiesByCampaignAndCountry(campaign, country)
  }

  return getCharitiesByCampaignCountryAndTier(campaign, country, tier)
}
```

This helper function neatly encapsulates all the logic we'd need, removes the need for any else statements, and does a much better job of keeping the happy-path code to the left. This is much easier to scan through, and much more readable as a result.

## Conclusion

Else statements are a weird code smell. They harm the readability of any code by forcing equal levels of indents for error handling and for happy paths. They also have the unique ability to separate code from the logic that affects it. They are easy to avoid through the two techniques of returning early and splitting logic into helper functions. As a result, they are unnecessary. You can write better code and be a better programmer by never using them.

Some caveats (to stop the pedants).
- In SQL CASE WHEN … ELSE … isn't really avoidable.
- In Scala, implicit returns (avoiding return statements for referential transparency) means you have to use them - you don't really have the ability to 'return early'.
- Ternary operators are fine.
- In python, the ternary operator uses `else`. This is also fine.

