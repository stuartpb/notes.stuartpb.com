# The Shotclock and CI

Remember back when Barfspace auto-committed, and how you've stopped running it in the migration to bagtent because you don't want the repository committing with unreachable posts? Let's fix that.

## This is getting increasingly urgent

Now that I'm not committing this stuff in an online workspace (July 2019), I ought to get this going again, since I'm increasingly likely to lose important content in between these big commits.

Of course, this is really just underscoring the nature of how Git isn't the most sensible synchronization platform for all this, but I'll tackle that problem down the line, as it's less likely to cause a problem than not having the shotclock working correctly is (and the same shotclock constructs can still apply for "push these tree changes to Dat").

Actually, though... would Hyperdrive choke on this? food for thought under the [Distributed Publishing Cloud](dzdxx-09cz0-td9m4-hhwj1-19vrc) home

Anyway, the plan to tackle this is currently laid out at [this sprint](hemw3-h4qam-gcaf8-0t8kt-qc6az)

## Task list

Dating back to the days this was a Quest:

- [x] Write the bagtent-orphans script.
  - You used the bagtent-orphans script to make sure you weren't forgetting any files you'd created before making a commit. Nice job!
- [ ] Make a new version of the shot clock that has a key you can press to conjure a new file, which will have its name printed on the command line, and be added to the commit and all
  - This is currently approximated by bagtent-conjure --open, which runs in a terminal next to the shotclock.
  - There could maybe be a feature to introduce commands to the shotclock?
  - Maybe this could be a module-tree thing?
- [ ] In conjunction with the feature above, have the shot clock pause when it finds a file is orphaned, and only rechecks, like, every seven seconds?
  - [ ] or only manually seems fine to me - probably easier to code, too
    - [ ] "Add a feature to the shotclock script that lets it hold off on running the shotclock when a test command fails." (misplaced quest item from elsewhere)
  - [ ] Should still have a way to invoke conjure while there are orphans, if you need it
  - [ ] Reminder that the conjure button should both echo the name AND pop open an editor tab, following a given command / alias you're expected to have in place and defaulting to `c9 open` or something like that? Is there already a version of `$EDITOR` that means "nonblocking invocation to open a new buffer"?
- [ ] What about the command to open a file by toplink name? Or just a "run a grep on all content files" key.
- [ ] Have a way, if there isn't one, to limit the shot clock's watch scope to just content files.
- [ ] Write a new git-slum-like script that just describes changes in terms of total bytes across files.
- [ ] Hell, just a Markdown editor with a preview pane that lets you open other files by link would be hugely helpful (EDITOR'S NOTE: wrong list)
- [ ] Figure out how to make commit messages that are just like "597+685=1282 changed bytes" (removals + additions)
  - Better: default shotclock message is how long between the first and last change, ie. "5 minutes of work"
  - "24 Seconds of Work: The Barfspace Story"

## quick thing

bagtent-orphans is getting pretty long to check from scratch - changes should be made for the purposes of the shotclock so that the script only checks files that weren't in the previous commit (probably in its calling context, taking filenames to check for on stdin piped from git through sed or something), under the assumption the previous commit was fine

even if the added-in-this-commit check fails, this can still be caught using CI in the builder

## Notes

Kind of continuing from that last thought: I've been thinking about what I do that makes me avoid using the shotclock?

I guess the scenario that came up for me was "I'm trying to find the tab I had open for the page I want to paste this text I cut from this other page", and I was thinking "should we avoid just committing deletions?" but, really, I think having the shot clock available...

okay, the thing I was going to write here was you could just delay it if a non-writing thing was taking too long, but that got me on the track of "what if I juuuust have enough attention span to search, and I don't have time to play Spaceteam with the console three times a minute"

(thought I just had, and had to terrifyingly jump out of that sentence mid-way to transcribe, is that these "what I was saying" deadend artifacts are sometimes because I got distracted mid-thought, but it's also sometimes because I just blanked in the time it took to say "what I was going to say was...", but unspooling that lead-in was simple enough that I was able to type that from muscle memory, hence why that's the tendril artifact that's left like the legs of Ozymandias)

it's funny because these linking phrases, I halfway can't tell when I'm inserting them into something I've already typed and what I haven't. I could look up at my monitor and forget that the next half of the page was something I've already typed

TODO: move above parenthetical and continuation to Nightmares of ADHD raw subpage/section, which should maybe get linked into the Organic Junction

hmm, maybe if it's a deletion was the only thing noticed in the most recent shotclock period, it pauses in "cut mode" where instead, at the end of the clock, it prompts like "if that delete is actually how you're closing out your commit, hit Y..."

You know, I feel like a "pause shotclock so I can do this move without fretting it'll get committed and I'll lose the data" mode would be fine. I think the safest way to do it would probably be to say "pause on cycling the shotclock until you see another change" - I think that's the feature I need to feel safe about not committing *very bad* states (in other words, artifically go to the "I just committed and there are no changes to commit" mode, even though there are pending changes)

This is starting to feel like it's complex enough to merit Node...
