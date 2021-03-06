# Bagtent Publisher

(a lot of the goals of this are already accomplished by the [Netlify CMS](peejv-w0xm5-ry9kg-z2x2s-bxtw5))

This is a specific tool aimed at making website building as easy as buying a pack of Post-It notes and a marker.

That last line is a great vision statement, especially attainable if you attach something like the IPFS quests after this one so that providing hosting doesn't have to be a single party's concern.

It overlaps a bit with the goals and philosophy of [penciltape](3gda5-8dfws-cb869-jmc9z-sg26j), and might make sense as a layer / paradigm around its use (like, the default layout has a "content" directory, and there's a button to create a new page by UUID, and penciltape can have use for all these other UI affordances, like maybe there's a way to make filenames second-class relative to the first line and subsequent content, like how GMail presents email subjects / bodies).

- [ ] Build something that will let people just start publishing markdown to a URL as a website, without having to give pages names or anything, and just relying on web UI affordances to the same ends as the Bagtent scripts (ie. search, maybe some kind of keystroke / menu option for "Spawn...")
  - [ ] Is there a layer we can drop on top of GitHub Pages?
    - [ ] I've been thinking there are a couple other projects I could accomplish with an overlay over a common codebase for this, like hubstrap
    - [ ] "Local buffers that autocommit under certain JS condition tests and then immediately push" would be a cool Wayside configuration that could effectively work like this
      - [ ] and if I write all the bagtent CLI in Node, which I probably won't do, I could run this in the browser
      - [ ] Well, a mix of JS and shell scripts might work
        - what's that JS-based Bash clone thing that one guy made who made Vorpal or whatever, I wanna say Cash?
  - [ ] Would this make sense for NeoCities?
- [ ] Hell, even just a Markdown editor with a bookmarks pad (I know I wrote this one somewhere) and a preview mode that can edit after following a link would be gangbusters
  - [ ] I mean, hell, even just an editor with a parser that lets me, say, ctrl+click a linked word in Markdown to parse and follow the link
  - it's a shame that the blocking element from just building this on the web platform would have to be the Git angle
  - working out even a basic interface to that with something like just a local thing to dispatch Git commands in a container... wouldn't work on chromebooks unless they support container platform, so that's out
  - [ ] do up a Lean Notes page about the state of doing Git stuff in JS

[Editor / interface project ideas](aamhr-wg63b-cf806-5kx1h-n3g6n) collects similar ideas
