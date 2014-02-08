---
layout: post
published: true
title: What Does Yahoo BOSS Really Think of Cleveland?
category: news
---

We had an interesting problem at work.  Any time a user did a
location-based search, we returned the same result set: Lakewood,
Ohio.  It didn't matter what the user searched by.  "Beverly Hills
90210" returned Lakewood, Ohio.  "221B Baker Street" returned
Lakewood, Ohio.  Lakewood is part of the Cleveland Metropolitan Area,
and as such we'll just refer to it as "Cleveland" for the purposes of
this essay.

My initial thought: Something is screwed up with caching.  Somehow
someone did a search on Ohio, and the results somehow got stuck in one
of our many caching layers.  Subsequent requests kept returning the
same cached result.  We do have a couple of employees from Ohio, so it
wouldn't be entirely outlandish for a Cleveland-area search the be the
first search performed after a caching bug was introduced.

After digging though the code path, it turned out that Yahoo BOSS, our
geolocation provider, was returning the same results no matter what
location we submitted.  Per the documentation, you perform a search
by submitting a request formatted as either
`placefinder?location=<address>` or `placefinder?q=<address>`.  Our
code was using the `location` parameter.  I tried changing this to the
`q` parameter, and suddenly geolocation requests magically worked.

I made a quick fix to the code, and did something you should never do:
I pushed out an untested release directly to production on 6 PM on a
Friday.  Our test and staging environments don't do real geolocation
since each request costs money.  They simply extract the zip code and
look that up.  It would take a while to get a test environment with
real geolocation setup, and changing the word `location` to `q` in the
bowels of some obscure code didn't seem likely to bring down the
entire site.  At worst, location searches would just still be broken.
This wouldn't be any worse than constantly returning Cleveland.
What's the worst that could happen? So I pushed the code.

And...

Of course...

It worked! Problem solved. I entered the weekend breathing a deep sigh
of relief.  But then I found myself wondering: Why Cleveland?  Why, of
all the places on Earth, would Yahoo BOSS use Cleveland as the default
location for all invalid queries?  There are three options I can think
of:

1. Yahoo BOSS considers Cleveland to be the center of the universe.
When you don't provide an appropriate geolocate-able address, it
decides it only makes sense to return the center of the universe, the
most important place on earth.

2. Yahoo BOSS somehow knows that Cleveland is some sort of nexus point
between dimensions, a.k.a. the Hellmouth.

3. Yahoo BOSS, after stripping invalid parameters, is left with the
impossible job of geolocating *the null address*.  When provided a set
of null inputs, it must return the one area on Earth that most ideally
represents pure and unending emptiness, the absence of anything and
everything, the state of total nothingness.  And in its infinite
wisdom, it returns Cleveland.

There are simply no other options.  Being a Pittsburgher, I have my own
opinions on which of the three options is unlikely, which is possible,
and which is probable. But I'll let you decide for yourself.

(As Yakov Smirnoff once said, "In every country, they make fun of
city. In U.S. you make fun of Cleveland. In Russia, we make fun of
Cleveland.")