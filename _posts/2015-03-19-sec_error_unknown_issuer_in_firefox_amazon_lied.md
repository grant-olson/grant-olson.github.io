---
layout: post
published: true
title: Https sec_error_unknown_issuer Error in Firefox? Adding https at your AWS Load Balancer? Amazon Lied To You!
category: news
---

We just dealt with a really frustrating error over at webkite. Our
site was suddenly broken with a bad ssl certificate, but only on
firefox. To make matters more confusing, things worked fine on all our
firefox installs in the office, but only blew up on clean installs.

If you're reading this, and you're seeing a `(Error code:
sec_error_unknown_issuer)` in firefox, then I can hopefully help you
out.

<a href='/files/firefox_error.png'>
<img src='/files/firefox_error.png' width="600" />
</a>


## TLDR: Amazon Lied to You When They Said the Certificate Chain Was Optional

Well I don't know if I'd say they lied exactly, but at the time we set
up the new certificate in the EC2 dashboard, Amazon showed us this:

<a href='/files/aws_console.png'>
<img src='/files/aws_console.png' width='600' />
</a>

So we didn't add the allegedly optional certificate chain. On newer
version of firefox, we now get the above error. Creating a new
certificate for the load balancer that included a valid certificate
chain fixed the problem.

## So why didn't this break on company computers?

This was particularly annoying because I use firefox every day, but it
wasn't broken on my machine. If it had been a chrome or safari issue another
coworker would have caught the error as well. But we were all working
blissfully unaware of the problem until I fired up my backup laptop to
do deploy a quick hotfix at home.

(And I'll save the fact that pingdom doesn't care about invalid or
expired certificates for another post. I've been meaning to send them
a support ticket on that. Shouldn't complain without giving them a
chance first.)

Apparently older versions of Firefox didn't complain, just like
current versions of Chrome (and presumably other browsers don't
complain). At the time they probably somehow obtained the certificates
and installed them in the certificate store.

I won't pretend to know the exact details, but this bothered us enough
that we decided to reproduce the error in staging. We:

1. Moved back to the old certificate.
2. Deleted the existing certificate store per [this article](https://support.mozilla.org/en-US/kb/couldnt-initialize-applications-security-component?redirectlocale=en-US&redirectslug=Could+not+initialize+the+browser+security+component#w_corrupted-file).

Boom! Things were broken on previously working machines. So the site
would work for people using firefox, but only if they had previously
accessed the site (1) before a certain unknown firefox update came
out, and (2) after we did our certificate updates for the year. But
that's still ~11% of web users. Ugh!

## Some warning signs in retrospect

Here are some things that should have tipped us off in advance. They
seem like obvious warning signs after the fact, but you know what they
say about hindsight.

1.  The directions to get [SSL set up on Cloudfront](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/SecureConnections.html#CNAMEsAndHTTPS) for our static
    assets in s3 provided a sequence of instructions that required a
    certificate chain. It wouldn't let us treat that component as optional.

    **ProTip™:** This provided us with a really quick fix on our end, as
    the certificates we build for that were now in the drop-downs for
    the EC2 load balancers, so we didn't need to figure out how to
    rebuild new certificates. If you did the same setup for Cloudfront
    with the same certificate, just use that one in EC2.

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
    certificates installed should have made us think more about how
    new the main, or intermediate signing certificates were, and why
    they weren't part of the default package on Ubuntu. But we wrote
    that off as openssl being really flaky and fragile when you're
    doing stuff from the command line, so we just added the certs to
    prod boxes and went along our merry way.

## Why didn't the other browsers complain?

Good question. Firefox has been getting much more strict on these
sorts of validations. In fact, not only did Chrome refuse to complain
here, it refused to complain when our ssl certificates on staging
expired unexpectedly.

As some of my previous blog posts and work have explained, I'm not a
huge proponent of the Certificate Authority model, but if you're going
to do it you should do it right. If not, you might as well start
trusting self-signed certificates in the browser. A certificate is
only valid if you can validate all the certifications all the way up
to one that you trust.

I don't know if chrome found another out-of-band way to get the
certificates in the chain, or if it just didn't care about doing full
validation. If anyone has insights on the differences in what happened
between browsers, I'd appreciate some information.

## Hope that helped

I'm a bit curious if our problem was unique, or if a lot of sites are
blowing up because they weren't configured in a way that worked with
the newest versions of firefox. If you encountered this problem, or a
similar one that wasn't directly related to EC2, please leave me a
comment or shoot me an email.

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
> are missing the certificate chain and Firefox, you get a pretty scary
> warning page.

Doh!
