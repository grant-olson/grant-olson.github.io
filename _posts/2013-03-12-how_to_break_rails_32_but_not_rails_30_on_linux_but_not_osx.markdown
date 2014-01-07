---
layout: post
published: true
title: How to Break Rails 3.2 But Not 3.0 on Linux But Not OSX
category: news
---

How to Break Rails 3.2 But Not 3.0 on Linux But Not OSX
=======================================================

Over at [WebKite](http://webkite.com/), we finally got around to
updating our rails stack to 3.2 last week.  This was of course long
overdue.  But it's one of those things that takes a non-zero amount of
time, and doesn't provide any immediately visible features, so we've
been pushing it back.

Testing went fine.  Everything looked good.  The code got merged to
mainline.  And our CI server decided it couldn't run tests anymore.
I'll spare you the full traceback, but the basic error was:

    cannot load such file -- multi_json/engines/Yajl

Soon after that, a developer pushed a feature branch to our staging
server so team members could review it.  Or tried to.  That server
blew up with the same error.

We found ourselves facing a problem that would only manifest itself on
Linux boxes, but none of the developer's MacBooks.  And one of those
nasty ones that doesn't include any of our application code in the
traceback.

And when a search on an error message doesn't even give a single
stackoverflow page, you know you're in for a long day.

multi_json Looks Suspicious
---------------------------

The traceback did involve the multi_json gem, which dispatches your
json calls to whatever library you want to use.  A little research on
that shows that it prefers the oj gem over the yajl-ruby gem.

I check to see if we've explicitly specified yajl-ruby in the Gemfile,
or if it's just a dependency of another gem.  We actually have
explicitly chosen that gem version, which seems a little suspicious.

I go to the commit for that.  Sure enough, this was done after we were
forced to upgrade to 3.0.20 for a security issue.

Upgrading to 3.0.20 Broke Our App
---------------------------------

The initial upgrade from 3.0.19 to 3.0.20 broke our test suite.  I
didn't think that minor-minor releases were supposed to do that.  It
turns out that to deal with some yaml exploits, the fix swapped out
the JSON back end to one that didn't work with our app.

The replacement back end didn't properly serialize and then
deserialize a string, as demonstrated by the following:

    1.9.3p392 :001 > ActiveSupport::JSON.decode(ActiveSupport::JSON.encode("foo"))
    ActiveSupport::OkJson::Error: unexpected "foo"
        from /Users/grant/.rvm/gems/ruby-1.9.3-p392@webkite/gems/activesupport-3.0.20/lib/active_support/json/backends/okjson.rb:69:in `textparse'
        from /Users/grant/.rvm/gems/ruby-1.9.3-p392@webkite/gems/activesupport-3.0.20/lib/active_support/json/backends/okjson.rb:47:in `decode'
        ...
	
Apparently there's some disagreement about whether a bare string is
valid json.  It seems clear to me that it is when looking at
[json.org](http://json.org/).  And if it's not, then shouldn't
`ActiveSupport::JSON.encode` throw an error? But I digress...

The short story is that only
[yajl-ruby](https://github.com/brianmario/yajl-ruby) parsed json in a
way that was compatible with our app.  So we added the following line
of code to our initialization:

    ActiveSupport::JSON.backend = "Yajl"

And went on our merry way.  Now that seems to be causing problems
on rails 3.2.

But Clearly Other People Are Using yaji-ruby
--------------------------------------------

This is a widely used gem.  And no one is reporting errors.  So what's
up?

Well first I need to talk to the developer who did the original fix.
He refreshes my memory on why we needed to use a specific json parser
to begin with.  Then he notes that the capital Y in
`ActiveSupport::JSON.backend = "Yajl"` looks suspicious.

OSX Sorta Kinda Has a Case-Insensitive Filesystem
-------------------------------------------------

If you want to drive yourself crazy, start using caps for directories
and file names:

    johnmudhead:pikimal grant$ pwd
    /Users/grant/src/pikimal
    johnmudhead:pikimal grant$ cd /USERS/GRANT/SRC/PIKIMAL
    johnmudhead:PIKIMAL grant$ cd ..
    johnmudhead:SRC grant$ cd ..
    johnmudhead:GRANT grant$ cd ..
    johnmudhead:USERS grant$ cd ..
    johnmudhead:/ grant$ 

If you want to drive rvm crazy, mix it up a bit:

    johnmudhead:pikimal grant$ pwd
    /Users/grant/src/pikimal
    johnmudhead:pikimal grant$ cd ../Pikimal
    ==============================================================================
    = NOTICE                                                                     =
    ==============================================================================
    = RVM has encountered a new or modified .rvmrc file in the current directory =
    = This is a shell script and therefore may contain any shell commands.       =
    =                                                                            =
    = Examine the contents of this file carefully to be sure the contents are    =
    = safe before trusting it! ( Choose v[iew] below to view the contents )      =
    ==============================================================================
    Do you wish to trust this .rvmrc file? (/Users/grant/src/Pikimal/.rvmrc)
    y[es], n[o], v[iew], c[ancel]> y
    johnmudhead:Pikimal grant$ cd ../PiKiMaL
    ==============================================================================
    = NOTICE                                                                     =
    ==============================================================================
    = RVM has encountered a new or modified .rvmrc file in the current directory =
    = This is a shell script and therefore may contain any shell commands.       =
    =                                                                            =
    = Examine the contents of this file carefully to be sure the contents are    =
    = safe before trusting it! ( Choose v[iew] below to view the contents )      =
    ==============================================================================
    Do you wish to trust this .rvmrc file? (/Users/grant/src/PiKiMaL/.rvmrc)
    y[es], n[o], v[iew], c[ancel]> 

And that was the problem!
-------------------------

OSX could require 'Yaml' because it doesn't think it's any different than 'yaml'.  However, linux thinks they're totally different names.  A one letter fix magically restored all of our linux boxes to good health:

    johnmudhead:pikimal grant$ git log -p 0464fd2acc8d4c38f212dc8376dd0e80795b1cc5
    commit 0464fd2acc8d4c38f212dc8376dd0e80795b1cc5
    Author: Grant Olson <grant@pikimal.com>
    Date:   Mon Mar 11 13:01:14 2013 -0400
    
        Don't break rails on 3.2 but not 3.0 and linux but not OSX
    
    diff --git a/config/initializers/yajl_as_json_backend.rb b/config/initializers/yajl_as_json_backend.rb
    index 3f75b9d..b204999 100644
    --- a/config/initializers/yajl_as_json_backend.rb
    +++ b/config/initializers/yajl_as_json_backend.rb
    @@ -5,4 +5,4 @@
     #
     # See http://weblog.rubyonrails.org/2013/1/28/Rails-3-0-20-and-2-3-16-have-been-released/ 
     # for details as to why the JSON backend was changed.
    -ActiveSupport::JSON.backend = "Yajl"
    +ActiveSupport::JSON.backend = "yajl"

Now the only question I'm left with: Why did this work correctly on
rails 3.0?  If you have any ideas I'd love to hear them.




