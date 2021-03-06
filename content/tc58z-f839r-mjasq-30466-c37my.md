# HTML Email Notes

> Between desktop, mobile, and Outlook, HTML email is a three-headed dog from hell.
>
> &mdash; Ted Goas, [on the naming of his Cerebus project](http://www.tedgoas.com/blog/cerberus-responsive-email-templates/)

## What makes HTML Email different from HTML pages?

The main thing is that stylesheets have historically been mangled by email clients (likely going back to the Hotmail days when nobody wanted to load a whole iframe for messages, but also didn't want selectors potentially affecting the outer UI), so **all styles have to be inlined**.

There's also the whole thing where **remote resources like images don't load by default**, because it's a form of information leakage to retrieve them

Outlook up through 2013 uses Microsoft Word to render, which can't handle proper CSS positioning, so if you want to support this old crap you need to abandon all modern best practices and write horrendous HTML for a world where Internet Explorer 6 just came out

There are probably other weird things, too - all the historic weirdness around email is still a concern, for instance (meaning you *need* a plaintext fallback)

## The other big difference

[You still need to work with font stacks.](tg7vs-ameww-82a8m-g1zg4-zsrwe)

## Some good primers around this

NOTE: This would probably make sense as something to incorporate into [Email and Phone Numbers](5kphh-cm8ce-2h89n-qebh1-8yjg7)

- [Lee Munroe's Smashing Magazine article from January 2017](https://www.smashingmagazine.com/2017/01/introduction-building-sending-html-email-for-web-developers/) is a great all-rounder
- [Mailtrap's writeup on embedding images](https://blog.mailtrap.io/embedding-images-in-html-email-have-the-rules-changed/)
  - their [general HTML email article](https://blog.mailtrap.io/building-html-email-template/) before this is like a lighter-weight, less-useful version of Lee's article
    - It's also newer, but that doesn't make much of a difference here
  - [Sendgrid also has an article on image embedding](https://sendgrid.com/blog/embedding-images-emails-facts/)
    - more developer-focused, but less informative about the tradeoffs

## Thought on image embedding

Here's a crazy idea that will almost certainly never work: what about srcset and/or picture elements?

hmm https://litmus.com/community/discussions/6152-srcset-and-sizes-in-email

https://stackoverflow.com/questions/31991667/responsive-images-in-an-email

> It's too bad there isn't a resource like CanIUse for html email...

oh wow, nice that someone linked to that CampaignMonitor page, that's super useful

So really it looks like the only UAs that don't support style elements in head are like AOL Webmail - maybe this line can be relaxed?
Selector support is also pretty good

Hey, maybe this advice should be rewritten!

## Other (primary) resources

- [CampaignMonitor's support matrix](https://www.campaignmonitor.com/css/)
- [Email Client Market Share](https://emailclientmarketshare.com/) is a useful report by Litmus for informing your "can I just not support that" decisions
- [Really Good Emails](https://www.reallygoodemails.com/) has a bunch of real-world samples that you can gank techniques from, or rip off wholesale

## Comparisons

- [Tools for working with email](9thyw-4x56x-r8801-3v32c-vcswj)
  - [editor app comparison](afmc0-ac0k4-25a9e-4fepz-s0we4)
- [template comparison](bpd4d-b33bm-mwaw1-t20ss-91gwv)
- [Pipelines and methodologies](d97v9-82tzb-yb8mg-yspc6-m9x8w)
