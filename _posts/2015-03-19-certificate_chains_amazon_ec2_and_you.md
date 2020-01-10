---
layout: post
published: true
title: Certificate Chains, Amazon EC2, and You!
category: news
---

Are you getting https sec_error_unknown_issuer Error in Firefox? Did
you add https at your EC2 Load Balancer? Well then Amazon lied to you.

We just dealt with a really frustrating error over at WebKite. Our
site was suddenly broken with a bad ssl certificate, but only on
Firefox. To make matters more confusing, things worked fine on all our
Firefox installs in the office, but only blew up on clean installs.

If you're reading this, and you're seeing a `(Error code:
sec_error_unknown_issuer)` in Firefox, and you're hosting your site on
EC2, then I can hopefully help you out.

<a href='/files/Firefox_error.png'>
<img src='/files/Firefox_error.png' width="600" />
</a>


## TLDR: Amazon Lied to You When They Said the Certificate Chain Was Optional

Well I don't know if I'd say they lied exactly, but at the time we set
up the new certificate in the EC2 dashboard, Amazon showed us this:

<a href='/files/aws_console.png'>
<img src='/files/aws_console.png' width='600' />
</a>

We didn't add the allegedly optional certificate chain. On some
installs of Firefox, we now get the above error. Creating a new
certificate for the load balancer that included a valid certificate
chain fixed the problem.

## What is the certificate chain?

I've said it before and I'll say it again: *Crypto is easy,
authentication is hard.*

It's easy enough to encrypt browser connections so that nobody can
read the traffic going on between you and a server when you, for
example, send them your credit card number or social security number
or mothers maiden name. But you need some method of authenticating the
encryption keys so you know that they belong to the server you're
talking to, and not a hacker or government agency trying to snoop on
you. That is: You need to trust the encryption keys you're using to be
able to trust the safety of your encrypted communication, by
authenticating them as valid and from a trusted source. If you can't
do that, the green lock on your browser means nothing.

https solves this problem with **Certificate Authorities**. These are
authorities who are trusted to *vouch* for other certificates by
signing them. A browser or other consumer of https decides which
authorities it trusts. This initial trust is written in
stone. Although some certifications are performed, from the
perspective of you the user sitting on your computer, these CAs are
trusted because Firefox says they're trusted, and that's all that you
need to know. In that sense that really makes your browser the
ultimate authority, and then it delegates that authority to the
various **root certificates** of Certificate Authorities that it trusts.

These root certificates then vouch for other sub-authorities. There
are several reasons to do this, but as a security concern it allows
you to compartmentalize damage if part of the system is
compromised. That brings us to the real-world analogy I like to use to
explain the system of trust I've already hinted at by calling it
*vouching*:

**Organized crime**. A mob boss has lieutenants who work for him. These
lieutenants have their crews. These crews might have people working
for them. Each step along the way, introductions are made by vouching
for someone. You might tell your boss, "I know a guy. He's a good
guy. We can trust him." and your buddy joins the crew. The mob
boss doesn't need to know about your buddy at all, but if the system
has integrity, starting with the mob boss and working the way down,
you've established a chain of trust that leads all the way down to you
from an undisputed authoritative source. Now if the system doesn't
have integrity, and your boss is a rat, you all get killed, but the
rest of the system and trust is still in place. And the big boss
doesn't need to know about the intimate details of things going on 3
levels underneath him to maintain the integrity of the system.

So back to the browser, it has several (actually hundreds) of
lieutenants who vouch for encryption keys. Someone else has vouched
for your encryption key. You're just not important enough to get to
meet the big bad CAs themselves. An indeterminate number of layers
between the root certificate and your certificate create a chain of
trust that can be followed all the way from the big boss to little old
you. This is the **certificate chain**.

## Why didn't this break on company computers?

Without that chain of trust, things should have blown up
everywhere. But they didn't. They were working just fine on our
machines.  This was particularly annoying because I use Firefox every
day, but it wasn't broken on my machine. If it had been a Chrome or
Safari issue another coworker would have caught the error as well. But
we were all working blissfully unaware of the problem until I fired up
my backup laptop to do deploy a quick hotfix at home.

(And I'll save the fact that pingdom doesn't care about invalid or
expired certificates for another post. I've been meaning to send them
a support ticket on that. Shouldn't complain without giving them a
chance first.)

This bothered us enough that we decided to reproduce the error in
staging. We:

1. Moved back to the old certificate.
2. Deleted the existing certificate store per [this article](https://support.mozilla.org/en-US/kb/couldnt-initialize-applications-security-component?redirectlocale=en-US&redirectslug=Could+not+initialize+the+browser+security+component#w_corrupted-file).

Boom! Things were broken on previously working machines. So the site
would work for people using Firefox, but only if they had previously
accessed the site for the first time after we updated our
certificate. But that's still ~11% of web users. Ugh!

I originally thought that a new version of Firefox had locked down SSL
security settings. But now I don't believe that. I was making things
more complicated than they were.  My current unproven working theory:

1.  Last year's certificates on EC2 had the proper certificate chain.
2.  When we accessed the site in the past, the certificate chain was
    stored in Firefox.
3.  When we requested new certificates from the same provider, it had
    the same chain of signing certificates.
4.  Our installations of Firefox were able to perform validation
    because they had access to the pre-existing signing certificates in
    the certificate db.
5.  Installations of Firefox that had never visited the site before did
    not, and complained.

Back to the organized crime example: Someone tells you, "Hey this is
my buddy Vladimir. You guys should talk. I think you could do some
good for each other." You reply, "Vladimir? We go way back. Remember
that thing? No the other thing. Yeah, yeah, good times!" There's no
need to re-establish trust for someone you've already decided is
trustworthy.

## Some warning signs in retrospect

Here are some things that should have tipped us off to the problem in
advance. They seem like obvious warning signs after the fact, but you
know what they say about hindsight.

1.  The directions to get [SSL set up on Cloudfront](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/SecureConnections.html#CNAMEsAndHTTPS) for our static
    assets in s3 provided a sequence of instructions that required a
    certificate chain. It wouldn't let us treat that component as optional.

    **ProTip™:** This provided us with a really quick fix on our end, as
    the certificates we build for that were now in the drop-downs for
    the EC2 load balancers, so we didn't need to figure out how to
    rebuild new certificates. If you did the same setup for Cloudfront
    with the same certificate, just use that one in EC2.

    In addition, it seems our static assets loaded just fine in
    versions of firefox where the main site was breaking.

2.  Someone experienced problems accessing the site on a phone. For
    some reason the errors read like an expired certificate, so we
    developed elaborate hypothesis' about their cell phone network
    caching old pages to save bandwidth. A bad certificate is a more
    reasonable explanation.

3.  We needed to manually install the certificates on our linux boxes
    so our RPC calls wouldn't fail. Our back end services requests
    between boxes started failing after we got new certificates. We
    identified this on staging and figured out how to manually install
    the certificates, and then we no longer got ssl errors as the
    boxes talked to each other.

    The fact that reasonably up to date machines didn't have the
    certificates installed should have made us think more about why
    they weren't part of the default certificate store on Ubuntu. But
    we wrote that off as openssl being really flaky and fragile when
    you're doing stuff from the command line, so we just added the
    certs to prod boxes and went along our merry way.

    In reality this was the same problem as described in the section
    above. If we would have had the full certificate chain in our
    certificate, then our RPC calls would have been able to provide
    full authentication up to the root certificate that was already in
    the system's certificate store. But in this case the system won't
    cache intermediate certificates as it gets them, because you need
    root access to store them in `/etc` and to run the
    `update-ca-certificates` command that generates the system's master
    list.

## Why didn't the other browsers complain?

Good question. Firefox has been getting much more strict on these
sorts of validations and the process of vouching. In fact, not only
did Chrome refuse to complain here, it refused to complain when our
ssl certificates on staging expired unexpectedly! That's right. The
certificates were expired and invalid and Chrome kept loading the site
without complaining.

As some of my previous blog posts and work have explained, I'm not a
huge proponent of the Certificate Authority model, but if you're going
to do it you should do it right. If not, you might as well start
trusting self-signed certificates in the browser. A certificate is
only valid if you can validate all the certifications all the way up
to one that you trust, including things like expiration date and the
general authenticity of each signing certificate.

Back to the (growing tired and old) analogy, a stranger walks up to
you on the street and tells you his friend Eve is a great
safecracker. Why would you trust Eve? You wouldn't. (Unknown chain.)
Or what about the same from your friend Pedro you haven't seen in 15
years? Even though he's never done you wrong, some time might cause
your trust to expire. (Expired signing certificate.)

## Hope that helped

I'm a bit curious if our problem was unique, or if a lot of sites are
blowing up because they weren't configured in a way that worked with
the newest versions of Firefox. If you encountered this problem, or a
similar one that wasn't directly related to EC2, please shoot me an
email.

- Grant

## Addendum

A co-worker found [this article from 2012](http://www.nczonline.net/blog/2012/08/15/setting-up-ssl-on-an-amazon-elastic-load-balancer/), or almost 2 and 1/2 years
ago!

Pertinent lines:

> Don’t be fooled by the AWS dialog, the certificate chain isn’t really
> optional when your ELB is talking directly to a browser. The
> certificate chain is the part that verifies that fully verifies which
> certificate authority issued the certificate and therefore whether or
> not the browser can trust that the domain certificate is
> valid. Different browsers handle things in different ways, but if you
> are missing the certificate chain and firefox, you get a pretty scary
> warning page.

Doh!
