---
title: "Testing is important: three ways to easily improve test quality"
description: It’s no secret that testing is important. We rely on tests to describe intended behaviour, catch any subtle bugs and prevent regressions in our code. But why are tests always such a pain to write well?
date: 2020-12-18T14:30:00
layout: layouts/post.njk
cover: /img/remote/dark_code.jpg
---

It’s no secret that testing is important. We rely on tests to describe intended behaviour, catch any subtle bugs and prevent regressions in our code. But why are tests always such a pain to write well? In mature codebases tests quickly become convoluted and, in my experience, testing is one of the most challenging aspects of software engineering.

This is because we don’t hold our tests – unit tests, integration tests, end-to-end tests or smoke-tests – to the same standards production code. Poor testing can make a codebase even more difficult to maintain than having no tests at all. Despite this, good testing practice flies under the radar and is easily neglected.

Let's challenge this and look at three qualities we expect of good production code, and apply this same thinking to test code – where such quality control is often absent.


## 1) Don’t Repeat Yourself (DRY) 🔁

People are obsessed with DRY when it comes to production code, often taking it [too far](https://dev.to/wuz/stop-trying-to-be-so-dry-instead-write-everything-twice-wet-5g33). This same anti-repeating is rarely applied to tests. Instead, testing becomes a haven for duplication, with information copied all-over the place. This is most prevalent in two forms.
- **Asserting** – Often there are a tonne of very similar tests, copy pasted with minor tweaks. In reality, they often cover the same test case, with the rationale that it’s “making extra sure”.
- **Setup** – Some tests require laborious setup. Creating mock users, seeding test-data and making sure any dependencies are stubbed out. This setup often gets duplicated between tests or test-suites, with only minor tweaks.

Duplicating assertions and setup both have the same knock-on impact. Bug-fixes, feature tweaks or refactoring quickly becomes a headache. Instead of being able to make a simple modification, a change becomes a game of whack-a-mole, wading through duplicated logic with seemingly unrelated tests starting to fail. You then notice some mocks are wrong, some tests don’t even work. We end up feeling like we need a sledgehammer rather than a scalpel.

Dave Cheney published a brilliant micro-blog on this very topic - you should definitely [check it out](https://dave.cheney.net/2020/12/15/the-story-of-the-one-line-fix). It summarises the mentality behind most duplication far better than I can.

## 2) Scrutinize tests the same as any other code 🔍

I recently [wrote a post on](https://dgls.dev/posts/avoid-utils/) one of the larger projects I’ve worked on during my career. This project, despite having some talented engineers working on it, was a complete mess. In particular, let's talk about code reviews and tests.

We all worked in the same physical office, so pull-requests were usually reviewed face-to-face.

This was great and worked really well – it was much easier to have open discussions, loop in people who should be involved, or to get answers to questions. I once overheard a discussion over a pull-request between two experienced developers. Their conversation bounced around discussing sensible topics – the high-level approach to solving a problem, justifying the design and making sure it was efficient. They then delved into the low-level, technical details – making suggestions for improving variables names, neatening up some abstractions, adhering to best practices and agreed standards.

Then it came to ~~reviewing~~ the tests.

*"Yeah, it has tests"* said one engineer to the other. *"Do they pass?"*, the second questioned. *"Yes"*, replied the first. *"That's good"*, confirmed the second, as both engineers sat nodding to each other as they absent-mindedly scrolled through several hundred lines of tests.

Let's look at the real problem here: the measure of quality had nothing to do with the tests, beyond them simply existing and passing. There was no discussion around edge cases. Were they testing the right things? Was the generation of the test data suitable? Did they take the right approach to mocking? Did the tests even accurately describe what they're doing.

It came to no surprise to anyone, certainly myself, that the majority of tests on the project were useless. Needlessly so, too. By asking simple questions and caring enough to review the tests properly, they could have saved hours of work later down the line, for the sake of five minutes now.

## 3) Avoid mocking integrations in integration tests 🔗

It sounds obvious when it’s written out like that, right? But you’d be amazed how often this happens. Consider that we are writing a function responsible for adding new users to a mailing list for a product. A test for this might look like the following:

```javascript
describe("mailing list list", () => {
  beforeEach(() => {
    jest.spyOn(emailStorage, "save")
    jest.spyOn(emailStorage, "rollback")
  })

  it("should add an email to a mailing list", async () => {
    const email = mockEmail()

    const response = await mailingList.addEmail(email)

    expect(response).toEqual({
      email: email,
      subscribed: true
    })
    expect(emailStorage.save).toHaveBeenCalledTimes(1)
    expect(emailStorage.rollback).toNotHaveBeenCalled()
  })
})
```

This looks fairly typical, right? Although you could say that’s a lot of mocking for one test. It prompts the question:

*“What are we actually testing here?”*

Are we *unit* testing the logic the function contains? Or are we testing that it *integrates* properly with the email storage?

If it's a unit test, you’d argue to mock as much as you can so you are just testing the logic.  We seem to be asserting on the mocks a lot though, which wouldn’t be the case if we weren’t also testing the integration.

In this case, how useful really is this test? It’s attempting to test an integration by integrating with a mock. This test looks a lot like it’s not really testing any behaviour at all - it’s just checking that the code does what the code does, at the same level of abstraction.

Say for example, that the email storage didn’t behave the way we expected it to. Would this test fail? Should this test fail? If we rewrote the test to use the real email storage, and then tested it worked in reality, would this be more valuable?


## Closing Remarks 💬

Tests are just more code. More code that you should treat with the same level of respect as any other code. Write them well, and they can be a powerful asset that help you safely refactor and add new features. Write them poorly, and they will quickly become a burden. Every change you make becomes wading through mud, sledgehammer in hand. We must think carefully how to write our tests, and be as meticulous with testing as we are with the rest of our code. Unless we do this, tests are a nightmare.
