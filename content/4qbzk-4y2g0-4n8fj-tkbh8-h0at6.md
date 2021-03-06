# Random UUID Usability in Bagtent

A thought I'm having right now is that Bagtent's random-id syntax makes it like a natural fit for Markdown's natural structure, as there's no need for redundant page names or custom Wiki syntax: every symbol is used (though the section below muses if it's maybe a little wasteful).

- [Alternatives to UUIDs in Bagtent](c4g5h-7tqnh-8j8gd-eaqvs-sxh8j)

The bit below also muses about alternatives, but just in terms of how there could maybe be better, shorter identifiers

## randomness musing

This is kind of a quick throw-in page to muse: how could I calculate the odds that two items together on a page would just happen to have the same three characters? Is it relative to the number of pages linked on the page, divided by two? Or does the number of overall files in the repo somehow affect the odds? (that seems like a Gambler's Fallacy)

[this page](gtaf6-82afe-0jaf7-23v3j-bjs8y) had the UUID that brought this to mind, you can probably find the other node by looking at the tree listing

Anyway, the use of UUIDs in Bagtent versus some higher-entropy random ID schema should really be examined, and how even a system using a different random ID schema would still fall under much the same tooling

I think there's this thought I have that, if you take 16 random bytes, there's a higher chance that sequence will appear in some kind of block of data than there is that it'll appear specifically formatted in hex with the hyphens that break up a UUID. I'm not 100% sure on this? but I feel like there's a birthday-failure-case if you take, say, 32GB of random data, that you've still got a reasonably high chance of hitting a 16-byte sequence in the series - whereas the ASCII of the UUID

the above stuff could probably be split off into a general Lean Note on Random UUIDs

## to katamari

- [Crockford Base32 for UUID v4, too](4p973-96srh-4e86t-ny0x8-1qf84)
