# How I'm Editing These Notes As of Mid-October 2019

I'm mostly editing these notes on my desktop at home, and using Google Keep when I'm away from it.

On my desktop ([stushiba](c2txf-hdscr-t99xp-4jrjj-beh02)), I have a `workspaces` directory in my home directory (divided up kind of like how I logically divided up my Cloud9 "workspaces") - in there, I have a `notes` subdirectory I've opened VS Code to in the past, so that's usually what it opens when I click the icon (which I have pinned to my GNOME dock or whatever it's called).

In the `notes` subdirectory, I've checked out my `bagtent` and `git-shotclock` repos as well as `notes.stuartpb.com`. The first thing I do when opening the editor fresh is `cd notes.stuartpb.com`, then `. bin/setup-shell` to set up my environment variables so the bagtent utilities will be in my PATH, as well as the EDITOR and GIT_EDITOR.

When I'm typing and I want to sprout off to a new page, I run `bagtent-conjure --open` to open a new editor tab, then copy the UUID from the terminal.
