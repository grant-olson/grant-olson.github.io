---
layout: post
published: false
title: Process Management, Virtualization, Religion and God
category: news
---

Sometimes I think about things a little too much.

In this particular case, I was configuring [god](http://godrb.com/) to
watch the components of our new software stack.  God is process
management software that lets you start, stop, and restart programs,
and most importantly it will automatically restart a dead process so I
don't get paged at 3:17 am on a Sunday.  Software developers are known
for coming up with overly clever names for software, and god is no
exception.  It sits there watching over the world, bringing it's
children to life, keeping an eye on them benevolently for the most
part, sometimes killing them dead in their tracks, and sometimes
resurrecting and healing them when misfortune befalls them.  Pretty
clever, right? Yes, at least until you try to search for technical
information and Richard Dawkins keeps showing up in your results.

I had god set up and working, but there was one problem.  God cannot
monitor and restart itself when it dies due to some misfortune.  For
example, a server reboot.  For this, I needed to configure upstart
since we're running [ubuntu](http://upstart.ubuntu.com/).  This turned
out to be surprisingly time consuming (ever use rvm and bundler on a
server?), but like a lot of dev ops stuff it wasn't particularly
intellectually challenging.  Tweak one setting, reboot the server, see
if it works.

And this is where my mind started to wander.

God only thinks he's all powerful and almighty.  But he's not.  He's
just another program.  Maybe a little more powerful than most, still
just a userland program.  He's not the One True Transcendent God,
creator of all, timeless, formless, boundless.  He's the demiurge!  I
managed to blow my own mind.

I imagine most readers are unimpressed and asking, "What the hell is
the demiurge?"  The demiurge is a deity in Gnostic cosmology.  Okay,
that probably doesn't help unless I explain what the Gnostics.

Lets start a Brief History of Christianity:

1.  Christ was born, Christ died, Christ rose again.

2. Three hundred years later Constantine established Christianity as
   the official religion of Rome, and the First Council of Nicaea
   defined what exactly Christianity meant.

3.  Seven hundred years after that, the East and West Split into two
    different churches, and five hundred years after that Martin
    Luther ushered in Protestantism.

