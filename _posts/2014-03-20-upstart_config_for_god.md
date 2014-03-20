---
layout: post
published: true
title: Upstart Configuration for God
category: news
---

I thought I'd follow up my [completely impractical post on
god](/news/2014/03/16/process_management_virtualization_religion_and_god.html)
with a practical one.  I needed to write an upstart script for god and couldn't find any examples out there.  Here's what I ended up doing.

## Full Script

    # /etc/init/god.conf
    
    start on runlevel [2345]
    stop on runlevel [06]
    
    setuid webkite
    setgid webkite
    
    respawn
    respawn limit 10 60
    
    env HOME=/home/webkite
    
    exec bash -l -c 'cd /opt/node/apps/god && exec bundle exec god -c my.god.rb -l /opt/node/log/god.log -P /opt/node/pids/god.pid -D'


## The Breakdown

    start on runlevel [2345]
    stop on runlevel [06]

Run all the time, unless we go into single-user mode or shut down.

    setuid webkite
    setgid webkite

Run as an unprivileged user.  Don't run as root.

On the negative side: We can't take advantage of the event driven
conditions in god, such as 'kill process if memory exceeds a half a
gig'.

On the positive side: We don't run as root.  We don't need a system rvm.
And we don't need to run rvm as root.

    respawn
    respawn limit 10 60

Have upstart respawn the process if it dies unexpectedly, but don't
let it go into death throes and overwhelm the server if it's just
plain broken.

    env HOME=/home/webkite

We end up running a bash login shell to load rvm functions, but even
that assumes that you have a decent `$HOME` variable.  We don't
without this.

    exec bash -l -c 'cd /opt/node/apps/god && exec bundle exec god -c my.god.rb -l /opt/node/log/god.log -P /opt/node/pids/god.pid -D'

Actually start god.  This was the really tricky part.

1.  We need `bash -l -c` so rvm works.  `rvm use` won't work in sh.

2.  Upstart uses magic to track the process id.  If you fork or
    daemonize, this changes.  Upstart provides the two options `expect
    fork` and `expect daemonize` which works in most cases.  Or so I'm
    told.  But we still lost the proper process id with god for
    unknown reasons.  So we needed to:

    *   Use `exec` so bash doesn't start its own process.

    *   Specify `-D` (no-daemonize) even though we are daemonized,
        so that god doesn't fork on its own and upstart gets the 
        correct process id.

Hope this helps someone.
