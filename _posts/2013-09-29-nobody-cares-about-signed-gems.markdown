---
layout: post
published: true
title: Nobody Cares About Signed Gems
category: news
---

> This postmortem originally appeared on the rubygems-openpgp-ca.org
> site that contained a proof-of-concept system to crytographically
> sign ruby software packages so they could be authenticated on
> download.  The system was developed after rubygems.org, the primary
> distribution network was hacked and compromised gems were uploaded.
>
> This was by far the most toxic experience I've ever had trying
> to write Open Source software, but here I just tried to focus on how
> uninterested developers are in software authentication, and how quickly
> a funnel of tens of thousands of programmers visiting the site resulted
> in only a half dozen or so actual test drives of the software.

Nobody Cares About Signed Gems
==============================

The signing key for the CA expired on August 17, 2013.  As an
experiment, I decided to leave the key in an expired state and see if
and when anyone would notice or complain.  Today (Sept 29, 2013)
someone finally asked about it on the mailing list.  Just over 45
days.

Why would I let the key expire?
-------------------------------

I originally wrote rubygems-openpgp a few years ago because I wasn't
happy with the existing signing solution and was looking for a side
project to work on.  No one paid attention.  It was clear that I was
the sole user.  So the project sat there in maintenance mode.

Then... A few years later... rubygems.org got hacked! There was no way
to tell if the gems on the site had been compromised.  Suddenly there
was interest in signing gems.  A few people found my project and
submitted pull requests.  Now that there were a few users, I dived
back in and decided to take the gem from the proof-of-concept stage to
a stable piece of software.  I spent the next month doing so.

Along the way one-too-many people said that OpenPGP wasn't useful
because most end users couldn't get into the strong set in the
Web-of-Trust, ignoring the fact that distributions systems such as
`apt` silently use OpenPGP behind the scenes.  So I created this site
as a proof-of-concept CA.  The implementation was simple.  I didn't
even really need a rails website.  The content is exclusively jekyll.
Basically a user would sign up.  I would manually verify that they had
control of their email, signing key, and had published gems on
rubygems.org.  I would then manually sign off on their key with a
smart-card set aside for that purpose.  I got a MVP up and running,
and started circulating the link.

This brought tens of thousands of users to the site.  Plenty of
upvotes on reddit.  Success, right?

Wrong!
------

One metric to measure success would be the number of people who
requested certification.  That number was less than a dozen.

But we need to keep in mind these are just gem authors, right?  Those
should be orders of magnitude rarer than the actual users, right?

Wrong!
------

I provide a test gem called openpgp-signed-hola.  It is the standard
"Hello World" gem with the addition of a digital signature.  All the
documentation referred users to use this gem to see how things work.
Rubygems.org has nice charts that show how many times a version of gem
has been downloaded.  Of course this number includes bots and other
automated retrievals in addition to actual human users testing out
rubygems-openpgp.  But it does provide an upper bound of the maximum
amount of people who tried to verify the test gem.

No more than two dozen people tried to manually verify the test gem.
To be honest, I think this number is probably high.  I think the
number was much lower.

Honestly, I found this disappointing.  It takes less than 5 minutes to
test gem verification.  You would think that number would be at least
equal to the number of upvotes on reddit.  That people would actually
read the site and try things out, instead of hitting the upvote button
and going away.  You would think the people writing blog posts about
how important signing was would take 5 minutes to try out the
software.  But alas, they didn't.

But it takes time for software adoption, right?
-----------------------------------------------

I had a few interested users.  There were finally signed gems on
rubygems.org signed by people other than me.  I expected that would be
enough that I'd get a trickle of signups over the course of the next
year.  But after the initial burst of interest activity came to a
halt.  After several months without receiving a single sign-up on the
site, inquiries on the mailing lists, or issues in github, I found
myself wondering why I was paying $20 a month to heroku to for https
hosting.  I went ahead and canceled that.  And that's when I decided
to let the signing key expire.

Why would I let the key expire again?
-------------------------------------

The key was setup to expire every 30 days.  This was basically a way
to enforce a revocation policy.  If the key itself was compromised
(unlikely, it's on a smart card), or if I was forced to issue
revocations on the CA's behalf, a periodic expiration would force
users to retrieve updated certificates and hence any revocations.

If there was a small community of people who were using the CA keys, I
would quickly get an email they started noticing that all their
software was expired.  It would at least provide some indication that
I should continue to maintain the CA.

45 days later someone finally noticed.

That doesn't prove nobody cares about signed gems, it just proves nobody cares about rubygems-openpgp
-----------------------------------------------------------------------------------------------------

True.

But I haven't seen any activity on the X.509 front either.  After the
rubygems compromise, things were supposed to change.  That was finally
the kick-in-the-pants the community needed to fix things and take gem authentication seriously.

* The rubygems-trust project was started to setup replacement rubygems
  with CA capabilities.  Activity fizzled out after a month with no
  visible results.

* A few people tried to start signing their gems with X509, but most
  gave up because it was impractical.

* The X509 code in rubygems itself has essentially the same TODO list
  as it did when the code was initially merged in 2007.

The above points, as the rest of this essay, is NOT an attempt to call
anyone out, it's simply what I've observed.  Getting X509 signing and
verification of gems to actually be used isn't any farther along than
it was before the rubygems.org hack either.

In Conclusion
-------------

I'm primarily documenting my experiences with the project so they're
available if/when there is push to start signing gems in the future.

This post is negative, but I hope it doesn't come across as bitter.  I
don't regret any of the time I spent on rubygems-openpgp or the CA.
It was fun! And I'll continue to maintain rubygems-openpgp if it's
needed.  (The CA, on the other hand, will probably go away when the
domain expires and/or I want to use my free heroku hours for another
project.)

I do wish people were more interested in signing their gems one way or
another, but then again I wish more people (especially techies) would
encrypt their damn emails!  Instead they'll write blog posts and tweet
about the importance of doing so, but won't actually change their
habits.

-Grant