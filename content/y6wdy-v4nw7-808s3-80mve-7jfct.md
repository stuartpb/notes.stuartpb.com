# Reiterate Yourself

You should strive to pick the right thing, the first time, *inasmuch as you know what you're doing*.

To the degree that you know you *don't* know what you're doing, you should pick something [close to your use case](0a1et-fyyjb-x693v-2b8vq-00n0w), something that's loose (like a collection of random notes) and will allow a lot of flexibility, but not a lot of structure.

If you start developing a more elaborate use case that could work on more structured data, *that's* when you should start designing the next phase / schema / iteration, and figuring out what the migration path from one to the next should be.

That way, you'll be forced to revisit your existing work to see the discrepancies, the commonalities and redundancies, and won't be tempted to keep legacy cruft around from a poorly-specced earlier iteration.

## An example of this in practice

When Tiare and I were putting our guest list together, I had us do it as a series of Trello cards that just listed groups of people (mostly couples) by summaries like "Alice and Bob" or "The Exampletons", and a parenthesized "(2)" after their names.

All we really knew and were prepared to discuss at that point was a ballpark figure of how many people would be attending our wedding, so that's what I focused on: I ran a map on a `querySelectorAll` for the card titles to sum up our anticipated guest count.

Six months later, when we were getting ready to send out our Save the Date emails, we talked about what things we'd need from our guests, and laid out the columns / schema for a spreadsheet, where we actually listed out each known guest's full name (including honorific) individually.
