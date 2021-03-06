# On userdata in Understanding Lua

## Notes from OneNote

> Cardboard boxes with "DO NOT OPEN" signs?
>
> One idea I had was to just let anything else be "userdata": photos, files, plasticine cows, sockets, models, kitchen sinks...

## Comments from Trello: "Userdata can be absolutely anything else"

> Pipes. Pink flamingos. File handles. Toy trucks. Ogres. Paintings.

---

> Maybe have userdata use a distinctly different art style (photos?) to emphasize its other-ness

---

> Now, there's only one type left. That might sound restrictive, although within Lua you'll find you can actually represent pretty much any concept using the primitives I've just described. However, there are things that exist outside of Lua, and that is what the last type represents. Userdata - everything that falls outside of these primitives.

## Comments from GitHub Issue

*(the following paragraphs were a series of comments on the issue on 2018-02-13, following the previous content)*

I'm going to use that last comment as the line in the script.

As for how userdata is represented... I like the "any kind of object, in a completely different artstyle" idea, actually. It makes it a little bit clearer that userdata is "alien", and can be interfaced with via metamethods as described in [issue #6][] (which will use the native artstyle).

[issue #6]: zd6hj-kp8n9-rc812-1v6gm-y6ft0

In *Dashseat*, however, they'd need to have a uniform appearance, so... maybe the "cardboard box that contains some Thing" idea is called for (or maybe that can just be how userdata is in Dashseat, and this comic can break from that).

(Or maybe userdata can have a metamethod/property that describes its appearance in Dashseat! It's not out of the question. We could just use cardboard boxes by default.)

Ooh, did I consider the idea that userdata is *flat representations of whatever thing it's supposed to be*? So, like it's a Polaroid photo of a socket, or a painting of a pipe.

... nah that kind of makes it sound like they're imagedata

Ooh, actually, I had this note in the OneNote notebook on a page called "Non-Analogous Elements":

> Stuff I have that just assists visually that doesn't stand for anything in Lua.
>
> Make this visible stylistically, eg. have solid outlines for all of the Lua stuff and just the infill for non-Lua stuff?

I think that's a better idea than using a different art style for userdata - though, you *could* arguably have both (ie. things with/without outlines are in-engine concepts, the opposite are without, and photos... have an outline, or don't, representing that they are an in-engine concept?)
