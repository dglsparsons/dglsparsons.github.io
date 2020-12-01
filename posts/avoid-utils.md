---
title: "DON'T WRITE UTILS: how to become an amazing programmer by naming carefully"
description: "I hate the word ‘utility’. It says absolutely nothing. And yet, I come across it in so often when programming. It can take different guises too: ‘common’, ‘shared’, ‘lib’, ‘pkg’, ‘tools’, etc. These names can appear both as filenames or as directory names and drive me insane when they do."
date: 2020-12-01T14:44:00
layout: layouts/post.njk
cover: /img/remote/tools.jpg
---

I hate the word ‘utility’. It says absolutely nothing. And yet, I come across it in so often when programming. It can take different guises too: ‘common’, ‘shared’, ‘lib’, ‘pkg’, ‘tools’, etc. These names can appear both as filenames or as directory names and drive me insane when they do.

Why?

They can contain literally anything.

And yet any code that ends up in these places has a clear purpose. It must be useful, otherwise, why would we write this code? I mean, some people do enjoy writing [useless](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition) [code](http://www.muppetlabs.com/~breadbox/bf/)... but most of the time this isn’t the case. So why do utils crop up everywhere? How do we avoid this problem in our own code? Why does the name utils trigger me? Let me tell you my story.

## The intent behind a bad name

When I first started my career in software engineering, I worked on a large project for NHS Digital. The project involved a variety of products that all shared libraries and packages. The source for these products and packages was a single monorepo, supported by around 30 developers. One ticket on the backlog that I picked up was to refactor a database client shared across these products.

I began by researching the database clients the products currently used. I painstakingly mapped out an upgrade path for each product. I worked through all common scenarios for the database client. I deliberately designed the minimal interface this database client would need. In my eyes, it was going to be a work of art. Perfection.

I set about building the database client and after endless weeks of meticulous upgrades, I finally had the finished product. My masterpiece. The code ran like clockwork.

## The downfall

A year later I returned to work on the same codebase to find everything had become a mess. It felt like a rats nest to work in. There was no separation of concerns and every pull-request felt like you were wading through mud. I tried to figure out what the problem was. What had changed? Why had it all gone wrong? After a while, I realised it was entirely my fault.

When I introduced the database client to the codebase, I’d put it into a `/shared/database/` directory. Sounds sensible, right? It was going to be shared across all the products, so that would be the logical place to put it. My flaw though was in creating that `/shared/` directory.

This shared directory triggered a rapid descent into chaos. Any code that could be conceived as potentially reusable was immediately placed into the shared directory without any consideration. More and more of the logic of each product ended up here, and less and less in the product’s directory. Changes risked breaking everything. It wasn’t long before some of the shared code became product-specific, or became a spiral of self-consuming code with no clear interface.

Making a change to the shared code either meant changing every caller, overloading the behaviour, or making slightly different variations of the same code. Searching the codebase became near-impossible, duplication began to run rife and separation of concerns was non-existent. It was a nightmare.

## So what did I learn from this?

In creating a shared directory, I’d opened the floodgates. The problem with the name ‘shared’ is that it does not describe the code I had written. It only describes my intent for that code. Code being reused, or shared, is incredibly common. Utility code, shared code, library code, package code. These names are all to do with intent, rather than describing what the code is. I should have stuck to putting my code in a `database/` directory. Forget the shared part.

So, what have we learned? We shouldn't call shared code shared, or utils, or common. It should just be code: named for what it is.
