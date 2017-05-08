---
layout: post
published: true
title: Help! I fried my postgres install on homebrew!
category: news
---

Did you:

* Get an error about readline when running psql?
* Quickly do a `brew upgrade postgres`?
* Think everything seemed fine until you rebooted your computer?
* At that point learn that postgres wasn't running because of incompatible file versions?
* Enter a world of hurt when you started reading up on the fact that an upgrade from postgres 9.3 to postgres 9.4 required a manual DB upgrade?
* Experience shock to learn that you couldn't even install postgres 9.3 after brew installed postgres 9.6?
* Curl up in a fetal position, covered in cold sweat, wondering how the hell you're going to have time to rebuild your complicated, er... you mean sophisticated, development environment from scratch when there's so much work to be done?

If so, I feel your pain. Hopefully I can help.

## TLDR FIX

    mv /usr/local/var/postgres/ /usr/local/var/postgres.old

    # Get old versions

    brew tap petere/postgresql
    brew install postgresql@9.3
    brew install postgresql@9.6

    # Install 9.6 db
    initdb /usr/local/var/postgres/


    # Stop the running 9.6 instance
    sudo brew services stop postgres

    # Migrate to new version
    # may need to look in to /usr/local/Cellar to get exact directories
    pg_upgrade -b /usr/local/Cellar/postgresql@9.3/9.3.16/bin/ -B /usr/local/Cellar/postgresql/9.6.2/bin/ -d /usr/local/var/postgres.old/ -D /usr/local/var/postgres
    
    # Drink a coffee (or beer) or three or six while there's a migration

    # Start up the server
    brew services start postgres

    # Verify server is running
    psql

## But now it blows up because of postgis

I had two old databases that used PostGIS. They caused the migration to fail. Attempts to get postgis to install on the 9.3 version of postgres failed. Unfortunately I don't have a fix for that, but can tell you how to at least delete the offending databases if you don't care about them, like I didn't. If you do need the postgis enabled databases, the documentation for the tap indicates that you can use a utility called pex to install things, but I didn't bother figuring that out.

To delete the old offending tables:

    brew unlink postgresql@9.6
    brew link -f postgresql@9.3

    # Need to start manually beacuse the files aren't where we expect
    pg_ctl -D /usr/local/var/postgres.old/ -l /usr/local/var/postgres/server.log start

    psql # Do DROP DATABASE etc

    pg_ctl -D /usr/local/var/postgres.old/ stop

    brew unlink postgresql@9.3
    brew link -f postgresql@9.6

## A little annoyed at brew right now.

Brew has given more than enough I can't be too mad at it for too long, but I'm a little disappointed that:

1. It silently upgraded readline which introduced a bunch of errors to old versions of software. I actually thought this blew up when I did an OS upgrade to OSX.

2. Makes no attempt to warn me that upgrading from 9.3 requires some serious manual intervention, the second time it's silently updated a version of software to a version that's incompatible with everything I have installed.

3. Allows no way out-of-the-box at this point for me to install the 9.3 binaries to do the upgrade from 9.3 to 9.4.

4. Seems to have broken old ways of installing old software by checking out an old commit of a particular brew file. Even though I tracked down the commit for 9.3, we now seem to autoupgrade and always install 9.6.2.

It would be nice if there was some sort of `--force` or `Are you sure?(Y/N)` prompt for these more disruptive upgrades, and wish the 9.3 version of postgres would have floated around a bit more so I could have fixed the problem without resorting to third-party taps.

## And annoyed at myself.

1. For not properly investigating the broken readline stuff I'd been dealing with off and on for a bit and 'fixed' by rebuilding my rubies in rvm.

2. Running brew commands nilly willy.

3. Not having a set up where it wasn't a problem to blow away my dev dbs and start from scratch. I should have either had backups, or been able to work from clean databases without affecting my productivity.
