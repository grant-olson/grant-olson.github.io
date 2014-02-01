---
layout: post
published: true
title: Using an OpenPGP Smartcard on Ubuntu 12.10
---

Using an OpenPGP Smartcard on Ubuntu 12.10
==========================================

I'm currently adding a key continuity feature to rubygems-openpgp.  It
works similar to the way that ssh stores copies of known host keys,
and warns you if the key has changed.

This is the first time I'm trying to store any changes locally, and
was a bit worried about the directories being created properly on
Windows.  So I decided to setup a VirtualBox install of Windows 8.  My
current hard drive was out of space, so that gave me an excuse to buy
a nice new SSD drive.  And that led to installing the latest version
of Ubuntu.  And now my Saturday is almost gone.

I had a little trouble getting my OpenPGP smartcard setup, so I
thought I'd write about it here.

Problem 1 - scdaemon is in the Wrong Package
--------------------------------------------

This is actually a problem on the Debian packages that has existed for
many years.  If you want to use gpg2, the scdaemon won't get installed
unless you install the gpgsm package:

    sudo apt-get install gpgsm

That one I was expecting.  But I thought I'd document it here anyway.

Problem 2 - Can't Access the Card
---------------------------------

This one I hadn't seen before:

I got the following error with gpg2:

    grant@johnicicleboy:~$ gpg2 --card-status
    gpg: selecting openpgp failed: Unsupported certificate
    gpg: OpenPGP card not available: Unsupported certificate

gpg fails as well:

    grant@johnicicleboy:~$ gpg --card-status
    gpg: selecting openpgp failed: unknown command
    gpg: OpenPGP card not available: general error


There were a few areas where this same issue was reported, but I
couldn't find any resolution to the problem.

After some extensive googling, I was able to find out that the
`gnome-keyring-daemon` now decides to grab control of your smartcard
reader.  Sure enough, I killed the process and `gpg2 --card-status`
started working:

    grant@johnicicleboy:~$ gpg2 --card-status
    Application ID ...: D2760001240102000005000009200000
    Version ..........: 2.0
    Manufacturer .....: ZeitControl
    
    General key info..: pub  2048R/A18A54D6 2010-03-01 Grant T. Olson (Personal email) <kgo@grant-olson.net>
    sec#  2048R/E3B5806F  created: 2010-01-11  expires: 2014-01-03
    ssb>  2048R/6A8F7CF6  created: 2010-01-11  expires: 2014-01-03
                          card-no: 0005 00000920
    ssb>  2048R/A18A54D6  created: 2010-03-01  expires: 2014-01-03
                          card-no: 0005 00000920
    ssb>  2048R/D53982CE  created: 2010-08-31  expires: 2014-01-03
                          card-no: 0005 00000920

Now I began the search for ways to disable the smartcard functionality
on `gnome-keyring-daemon`.  Couldn't find anything.  There were ways
to switch off its ssh-agent replacement, which I wanted to do anyway
since I ssh authenticate via my smartcard.  There were some other
settings about pkcs11 and secrets that seemed promising.  So I ran the
following commands to disable these features:


    gconftool-2 --type bool --set /apps/gnome-keyring/daemon-components/ssh false
    gconftool-2 --type bool --set /apps/gnome-keyring/daemon-components/secrets false
    gconftool-2 --type bool --set /apps/gnome-keyring/daemon-components/pkcs11 false


But disabling them didn't do the trick.

Next I went with a hack fix and basically nuked the gnome-keyring-daemon:

    sudo mv /usr/bin/gnome-keyring-daemon /usr/bin/gnome-keyring-daemon.bak

This didn't *seem* to have broken anything too horribly, and I never
liked the gnome keyring or seahorse to begin with.  So I decided to write a blog post for the sake of the interwebz.

But Then, A Complication
------------------------

After all that I went to write things up.  I decided to re-break
things so I could obtain the error message that `gpg --card-status`
threw.  So I moved the `gnome-keyring-daemon` back into place.

Lo and behold, everything worked!  Both `gpg` and `gpg2` were able to
access the card just fine.

I thought that maybe after I configured gpg-agent to act as the
ssh-agent, it was grabbing my smart-card before gnome-keyring-daemon
could.  So I commented out the entries for that, and sure enough card
reading was broken again.

The Proper Fix (or is it?)
--------------------------

Add this to ~/.gnupg-agent.conf to enable ssh support:

    enable-ssh-support

Add this to ~/.bashrc to use gpg-agent for ssh instead of
gnome-keyring-daemon, substituting your host name:

if [ -f "${HOME}/.gnupg/gpg-agent-info-HOSTNAME" ]; then
           . "${HOME}/.gnupg/gpg-agent-info-HOSTNAME"
           export GPG_AGENT_INFO
           export SSH_AUTH_SOCK
         fi


Another Complication!
---------------------

Everything seemed to be working, but then I got this generic error
message from Enigmail:

    No SmartCard 
    could not be found in your reader 
    Please insert your SmartCard and repeat the operation.

After enabling a debug log, it turned out the error was the same
unsupported certificate error I was getting before, even though
signing still worked from the command line.  Killing the
gnome-keyring-daemon process allowed me to sign emails again.

So, I went back to:

    sudo mv /usr/bin/gnome-keyring-daemon /usr/bin/gnome-keyring-daemon.bak

And everything seems to be working... for now.

That's All for Now
------------------

If you've encountered the same problem, hopefully this will help.

-Grant
