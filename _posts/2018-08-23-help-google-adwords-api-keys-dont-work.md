---
layout: post
published: true
title: Help! Google Adwords API Keys Stopped Working August 22nd!
category: news
---

We just spent two days debugging a problem with our google adwords API
keys and finally got things working. We're not sure if this problem is
affecting people globally, but it was particularly difficult to debug
so I wanted to get this information out there. Let me know if it
helped you out.

We've been using API keys to generate google ads for several years
now. If you're using them, you have a basic understanding of how
things work in ruby-land. There is a configuration file `adwords_api.yml` that you
set up with basic values, then run the `setup_oauth2.rb` script
included in the github repository for Google's gems. This has you do
browser-based authentication, and then the file will have a refresh
token. When this file is re-used, the refresh token is used to request
an access token as needed, and this access token is used to generate
ads.

Yesterday morning, things blew up and we did some basic debugging. We
decided that a three year old refresh token might be the problem, and
regenerated one locally, and things seemed to be working again. But
then, after an hour, all our processes would blow up again. We could
fix the tokens every hour, but this obviously wasn't a full-time
solution.

As we tracked things down and went through many red herrings, we came
to realize that normally Google's code would figure out that the
access token was required, and would request a new one via the refresh
token. As we dug in to the google code, we finally made our way to the
[oauth2_handler.rb file in ads_common](https://github.com/googleads/google-api-ads-ruby/blob/master/ads_common/lib/ads_common/auth/oauth2_handler.rb). In
particular the `get_token` method on line 89:

```ruby
# Overrides base get_token method to account for the token expiration.
def get_token(credentials = nil, force_refresh = false)
  token = super(credentials)
  token = refresh_token! if !@client.nil? &&
      (force_refresh || @client.expired?)
  return token
end
```

Using `bundle open google-ads-common` we were able to go in and change
`force_refresh` to default to true. Lo and behold, authentication was
working!

Another thing we noticed as we looked through various SOAP output, there
are two values attached to the access token, the creation date, and
the time, in seconds, until it expires. As we looked at the output, we
noticed that it was returning `expires_in` values that were actually
counterintuitively *increasing* rather than decreasing. We would have
expected a key that started at 3600 to, when called a minute later, to
return 3540. Instead it was returning 3660, and climbing up until
hitting 7200 one hour later, at which point the access token would be
expired, but we would not generate a fresh one, and our app would
start blowing up.

Unfortunately, we were unable to tell if the value always worked this
way, or if there was a new breaking change improved in the past few
days.

We were reluctant to monkey-patch the core google libraries and have
to deal with that. So armed with the knowledge that our gems weren't
calculating expiration date correctly, and we wanted them to know that
they needed to generate the access token, we tried modifying our
config files and found a fix that worked without having to patch
google's gems.

## The fix:

Original adwords_api.yml, as generated from `setup_oauth2.rb`:

```yaml
---
:authentication:
  :method: OAuth2
  :oauth2_client_id: REDACTED
  :oauth2_client_secret: REDACTED
  :developer_token: REDACTED
  :client_customer_id: REDACTED
  :user_agent: WebKite_Radius
  :oauth2_token:
    :access_token: REDACTED
    :refresh_token: REDACTED
    :issued_at: 2018-08-23 13:10:13.299601000 -04:00
    :expires_in: 3600
    :id_token: 
:service:
  :environment: PRODUCTION
:connection:
  :enable_gzip: false
:library:
  :log_level: INFO
```

Note the pertinent information is the `:issued_at:` and `:expires_in:`
The fix is to switch `:expires_in:` to 0:

```yaml
---
:authentication:
  :method: OAuth2
  :oauth2_client_id: REDACTED
  :oauth2_client_secret: REDACTED
  :developer_token: REDACTED
  :client_customer_id: REDACTED
  :user_agent: WebKite_Radius
  :oauth2_token:
    :access_token: REDACTED
    :refresh_token: REDACTED
    :issued_at: 2018-08-23 13:10:13.299601000 -04:00
    :expires_in: 0
    :id_token: 
:service:
  :environment: PRODUCTION
:connection:
  :enable_gzip: false
:library:
  :log_level: INFO
```

After that, things worked perfectly!

## Let me know if this helped you out.

This was a really unusual bug for us, and we were surprised that it
wasn't all over StackOverflow or the Google forums since it completely
took our production services down. I'm still curious if we just have
something really odd going on, or if this was a more widespread
problem. So please shoot me an email if you found this information
helpful, or if you can shed any additional light on the sudden change
in our production environment.

Thanks!

-Grant
