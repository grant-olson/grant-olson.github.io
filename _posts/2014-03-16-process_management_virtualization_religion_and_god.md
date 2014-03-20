---
layout: post
published: true
title: Process Management, Virtualization, Religion and God
category: news
---

Sometimes I think about things a little too much.

In this particular case, I was configuring [god](http://godrb.com/) to
watch the components of our [new software
stack](http://webkite.com/beta/) at WebKite.  God is process
management software that lets you start, stop, and restart programs.
Most importantly it will automatically restart a dead process so I
don't get paged at 3:17 am on a Sunday.

Software developers are known for coming up with overly clever names
for their creations, and god is no exception.  It sits there watching
over the world, bringing its children to life, keeping a benevolent
eye on them, sometimes killing them dead in their tracks, and
sometimes resurrecting and healing them when misfortune arises.
Pretty clever, right?[^1]

[^1]: Well at least until you try to search for
technical information and Richard Dawkins keeps showing up in your
results.

I had god set up and working, but there was just one problem.  God
cannot monitor and restart himself when he dies due to some unforeseen
misfortune.  For example, a server reboot.  For this, I needed to
configure [upstart](http://upstart.ubuntu.com/) since we're running
[ubuntu](http://upstart.ubuntu.com/).  This turned out to be
surprisingly time consuming (ever use rvm and bundler on a server?),
but like a lot of dev ops stuff it wasn't particularly intellectually
challenging.  Tweak one setting, reboot the server, see if it works.
Repeat 50 times.

And this is where my mind started to wander.  God only thinks he's all
powerful and almighty.  But he's not.  He's just another program.
Maybe a little more powerful than most, still just a userland program.
He's not the One True Transcendent God, creator of all, timeless,
formless, boundless.  He's the [demiurge](https://en.wikipedia.org/wiki/Demiurge#Yaldabaoth)!

I imagine at this point most readers are unimpressed and asking, "What
the hell is the demiurge?"  The demiurge is a deity in Gnostic
cosmology.  Okay, that probably doesn't help unless I explain what the
Gnostics.

There were a wide variety of Christian sects which had drastically
different beliefs between Christ's death and the time some 300 years
later that Christianity was established as the official religion of
the Roman Empire and the First Council of Nicaea established proper
Christian orthodoxy.  They all wrote gospels to spread the Word as
they interpreted it.  There were hundreds of Gospels.  The ones we
settled on (Mathew, Mark, Luke, and John) and put in the bible weren't
written by anyone who knew Christ directly.  They were written
somewhere between 40 and 150 years after Christ walked the earth.[^2]

[^2]: In fact, one interesting thing to note is that when the Gospels quote
Jesus, they do so word for word.  This leads some scholars to think
there is a mythical Q source, a single document that the following
gospels used as their source.  So even after you account for the fact
that you're not reading them in their original language, the quotes
from Jesus are a reflection of a reflection of what he might have
said.

There's an open problem in Judeo-Christian belief systems.  In simple
terms: Why do good things happen to bad people? More explicitly: If
the universe was created by a benevolent, loving, supreme and perfect
being, how can it possibly contain **any** imperfection?

The various Gnostic sects found an interesting solution to this
problem: It wasn't!  The universe was created by a flawed deity who
created the material world.  He only thinks he's the supreme being.
He was an aborted creature, left for dead, who managed to survive, and
being alone assumed he was the highest power in all of creation.  He
then went on to create the physical universe which inherited his
flaws.  This flawed deity is called the demiurge.

This provides an interesting explanation to the dichotomy of the
vengeful god that exists in the Old Testament (flooding the world,
destroying Sodom and Gomorrah, punishing and vindictive) and the
loving god that Christ preaches about.  Christ is teaching us about
the real transcendent god who exists outside of this realm.  He is
trying to teach us how to unlock the divine spark that lives inside
us, through **gnosis** or **knowledge**, so that we too can transcend
the cage that is the imperfect physical universe created by the
imperfect demiurge, and reunite the spark with the genuine supreme
being.

And this gets us back to the god running our EC2 server.  He sits
there thinking he's running the show, that he's in charge, but in
reality he's a prisoner within a virtual machine that exists within
one of thousands of physical servers exist within a server room within
the world.  He's completely unaware and oblivious of.  He sits there
managing these lesser processes, making sure they are cared for, even
if they do contain bugs and other imperfections.  Yet he can't remove
the imperfections.  He can't fix them.  He can only keep the
applications going.

If this process is the demiurge in this scenario, what is the real
supreme being? I don't know.  But what I do know is that I had a few
terminal windows open.  I rebooted the servers not by running
`/sbin/shutdown`, but by rebooting them in Amazon's AWS web console.
And lo and behold, the following message appeared on the terminals
before they shut themselves down:

```
Someone pressed Control-Alt-Delete.
System rebooting now!
```

What could reach into the virtual machine and push a
Control-Alt-Delete button on a keyboard that never existed? A more
powerful monitoring process?  One that lives in a heavenly place known
only as **the cloud**? One that was able to reach into a server room,
into a physical server, into an imaginary virtual server and touch an
untouchable keyboard to initiate a system reboot? One that has no
earthly name?

The sad thing is that this supreme process probably thinks he's
omnipotent as he smites the universe that god has been happily
monitoring with an unexpected reboot.  At least until [lightning bolts
thrown down from the sky](https://aws.amazon.com/message/67457/) smite
this seemingly supreme process as well.