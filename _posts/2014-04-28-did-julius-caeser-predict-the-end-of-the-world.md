---
layout: post
published: true
title: Did Julius Caeser Predict the World Would End in CE?
category: news
---

One of the nice things about dynamic languages like ruby is the
REPL. The Read-Evalueate-Print-Loop.  Also known as the interactive
console.  In ruby you fire it up with `irb`.  Sometimes it's easier to
fire this up to mess around with a class then to actually *ugh* read
the documentation.

I was messing around with dates, so I typed this out to get an idea of
how dates were formatted.

    grant@john-icicleboy:~$ irb
    2.1.1 :001 > require 'date'
     => true 
    2.1.1 :002 > puts Date.new
    -4712-01-01
     => nil 

I was really surprised to see that the date given with no arguments
provided was 4712 BC.  Now I suspected that `Date.new` was really
shorthand for `Date.new(0)` and that this value was actually the epoch
for ruby's Date class, similar to the way Unix uses an epoch of
1970-Jan-01 and stores dates as the number of seconds from this.  But
why 4712? That seems suspiciously as if ruby assumes the world is only
6 or 7 thousand years old! Instead of using this to troll people on
twitter, I decided to dig in and
[RTFM](http://www.ruby-doc.org/stdlib-2.1.1/libdoc/date/rdoc/Date.html#method-c-new).
This does indicate that this year was intentionally and specifically
chosen, and talks about various calendar systems throughout the ages,
but isn't useful in answering the question at hand.  What is so
important about 4712?

For this we have to turn to wikipedia.  The article on the Gregorian
Calendar isn't particularly useful.  Neither is the one on the Julian
Calendar.  But I add 4712 to my google searches, and I finally get to
the page I'm looking for.  It's about the [Julian
Day](https://en.wikipedia.org/wiki/Julian_day).  It explains that the
Julian Day Number 0 is assigned to 4713 BC.  It also goes on to
explain that the Julian Period has a interval of 7980 years.

The first thing an attentive reader will notice is that the Julian
Period begins in 4713 BC, and I've been spouting off about 4712 BC.
How could the ruby implementation know about all these details and
then get the year off by one? It didn't.  So why is it different?
Because there's no year zero in the calendar system.  We go from 1 BC
to 1 AD.  However, we can specify a the number zero as an offset in
the Date class that ends up representing 1 BC.  So we need to subtract
another year to represent these early dates, and year -4712 becomes
4713 BC.

Now think about all the hype about the Mayans predicting the end of
the world on December 21st, 2012?  It was the same scenario.  This
date was actually the date when the 5,126 year long calendar looped
around and started over.  It wasn't considered the end of time any
more than the end of one year and the start of the next.  And yet a
bunch of people were still saying the Mayans thought the world was
going to end!

I think it's interesting that the Julian Period also has an end.  The
period ends in 3268 AD.  That's over a millennium away from today's
date.  By then we could be using some new calendar system.  Star
Date.  Metric Time.  Who knows?  Today's religions could seem ancient
and silly.  The Roman Empire itself could seem as distant culturally
as the Mayan Empire does to us now.  Will someone stumble upon these
old articles about the Julian Period.  Will they interpret the end of
the cycle as the end of the world? Will the headlines read:

> Julius Caeser Predicted It! The End Is Near!

?

We'll see.

And why did I learn all this today? Because of the REPL.