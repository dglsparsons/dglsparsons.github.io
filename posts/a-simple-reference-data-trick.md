---
title: A Simple Reference Data Management Trick
description: I've continually made this same mistake with reference data. It's a mistake that's incredibly common, and easy to avoid. Here's the simple trick I've found.
date: 2020-09-30
layout: layouts/post.njk
image:  img/remote/refdata.webp
---

Nearly all enterprise systems have a concept of reference data. From website
configuration, lookup tables, ID-to-ID conversions or domain-specific data, reference data is extremely prevalent and can
take many different forms. For years I made the same mistake over and over in managing this reference data. Let's have
a look at this common reference data pitfall, why it's so easy to fall into, and finally a nice trick to keep
reference data simple.

# The mistake

A couple of years ago I worked on a data processing system for an internal product the company I worked for was
developing. This product, among other things, performed a series of derivations from submitted data. The most notable
of these was deriving a county from a given postcode (in the UK).

When initially given the requirement to do this lookup, I envisaged a whole reference data system. Counties in the UK are not
completely static, and their boundaries change gradually over time. Also, new postcodes get added fairly
frequently. This meant the reference data would periodically need updating. My brain immediately jumped to a solution: there would be a tool or
system that pulled reference data from the source (ons.gov.uk); this reference data would be written into a database (this
could be a SQL database or a document store like DynamoDB. We could then simply query the database to perform any
lookups we needed and updating the reference data would just be running a tool. Simple enough?

Despite initially sounding simple, there's a fair amount of hidden complexity within a system like this though. First of
all, we need to think about how a database will be provisioned and set up - it needs deploying somewhere with an
appropriate amount of memory and storage. This should also be isolated from the internet but not from our application
to prevent anyone with malicious intent from being able to access the database directly. We'd then need to consider
database Schemas, and how to handle migrations to setup or update these. We then need to consider database users, making
sure we can update the reference data with sufficient write permissions, but that the application only connects with
read-only permissions (for the principle of least privilege). Once we have users, we need to think about how to make the
application aware of the user without exposing credentials...

The whole system rapidly becomes very complicated.

# The solution

This overcomplicating is an easy trap to fall into, and especially easy as there doesn't appear to be a simple solution that
fits our criteria. Where else could we store the reference data if not in a database? A file on
disk? This would still have many of the same problems as the database solution, plus loading the file contents for a
single lookup would be amazingly inefficient. We could load it into memory and keep the lookups there while the application
runs? This one is pretty close, but maintaining and updating the reference data is still unclear, and would be
a bit of overhead on startup.

The solution is simple: **treat reference data as part of your code**.

The trap that's easy to fall into is thinking that your reference data has to be a part of its own, separate
system. This used to be true where reference data updates needed to be deployed and a slow software development life
cycle meant applications would only get deployed every several months, or, in some extreme cases, once a year (as was
the case for one project I worked on). However, thanks to modern development practices such as Continuous Delivery, and
the reduced friction and risk through containerisation and infrastructure as code, deployments can now happen as
frequently as we like. In the case of Shamaazi, where I currently work, we often deploy our code several times per day
and every single change usually marks its own deployment.

As a result of this, the simplest thing to version, manage, and keep up to date with the code that is running, is to
simply commit the reference data as part of the code. This could be `YAML` files that are loaded as the application
starts, or, even simpler, simply converting the reference data into the language you use.
This has a twofold benefit: the application does not have _any_ overhead in accessing reference data as we've removed all
the complexity around deploying, managing, migrating and accessing databases; but also, our reference data is now
version controlled and we can easily review changes and track them over time.

You could argue that this would bloat your application, adding a significant amount of data or extra code that
would impact the application size. However, this argument would fail to accommodate how much complexity already
exists in running any application. At Shamaazi, we use Go for its simplicity, fast build times and static linking.
A compiled binary without any reference data comes in typically between 10 and 15M. Using the example of the UK
postcodes from earlier, there are around 1.7 million postcodes in the UK. Each of these is mapped to a County name.
As a raw file, this seems pretty large - it's around 43M. Compressed, however, it's much smaller (~8M). Adding it to the
Go source code results in a barely noticeable increase in the application size - and this is for a _massive_ reference
data set. If the reference data set is 500,000 records (still really big), we see around a 3M difference in Go binary size.

# In Conclusion

Reference data can be tricky to manage in a way that's easy to update and easy to manage. I've fallen victim to
overengineering systems in the past to deal with reference data. Often the simplest solutions are best, and that seems
to be the case here. Keeping reference data as part of your code doesn't have a significant impact on the code size, it
allows reference data to be updated much more easily, and it removes an incredible amount of complexity from
infrastructure. This isn't a one-size-fits-all solution though. If reference data is updated as part of the system, then this
does not work. However, for read-only reference data, this is a useful tool to add to your arsenal.
