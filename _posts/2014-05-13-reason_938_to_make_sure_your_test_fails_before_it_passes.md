---
layout: post
published: true
title: Reason 938 to Make Sure Your Test Fails Before It Passes
category: news
---

Here's a quick example showing why you want to see your test fail
before you see it pass.  This verifies that you're actually testing
what you think you're testing.  This rspec test was passing just fine
before I realised I didn't even test to see if the result was true:

    it "detects normal zip" do
      Geomancer.zip_code_only?("15217").should 
    end

I only noticed it when I wrote the next test and it also passed when
it should have failed.

    it "doesn't detect bad zip" do
      Geomancer.zip_code_only?("123456").should 
    end

(`x.should` is perhaps even more enigmatic than `x.should be`, which is
actually valid and useful rspec syntax.)

I was honestly a little surprised that this didn't fail with some sort
of runtime error, but rspec works in mysterious ways.  I still haven't
decided if this is a feature or a bug, but I think it would probably
be nice if this threw a runtime error.  I can't think of a case where
the above syntax would be useful.