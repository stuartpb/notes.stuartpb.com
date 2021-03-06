# The MagicBand Infinity Gauntlet

Okay, so, yeah, [someone else already did this](https://www.thingiverse.com/make:545330), but you've got that yellow MagicBand Icon just sitting there, and it *is* the perfect size, shape, and color for the Mind Stone, and it looks like their thing wasn't worked into the print (and either way, they didn't publish their sources)...

so, I've made the basic version of a model for this, but if it's going to take an eighth of my gold filament (UPDATE: more like a tenth), maybe I should make the model a bit more full-featured (and also maybe prototype it in the brass filament that I care less about)?

I'm basing my work on https://www.thingiverse.com/thing:3205209/comments and still trying to figure out how this assembles

## remaining infinity stone idea

orange bird

- https://disneynewstoday.net/tag/orange-bird/
- http://www.bigfloridacountry.com/orangebird.htm (and http://www.bigfloridacountry.com/wdw/enchantedtikiroom.shtml has more)
- https://disneyparks.disney.go.com/blog/2018/03/new-orange-bird-merchandise-that-will-have-you-thinking-orange-thoughts-this-spring/

## current thinking 2019-06-07

I've got the arm part of the original gauntlet printing, and with it, the last of this original design.

I'm thinking that, as a model, [HappyMoon's version](https://www.thingiverse.com/thing:2888091) was better - the arm is all one piece, the thumb part looks like it actually fits into the rest of the gauntles, the embossed details are less wildly-off-base from the original, etc.

I'm also realizing how much I screwed up by not scaling beforehand

I'm thinking now that my second take on this, if I do one (I'd probably need to order more filament), would use HappyMoon's model as a base, rescale it for my hand, not do any of this dish-based stuff since the MAgicBand cutout would be basically the size of the Mind Stone at hand-appropriate scale (but maybe have an embeddable half-holder like the band's that's scaled down a little to be tighter - this could then swap out for the model-original surface), and incorporate tweaks to the fingers like the Wearable one

my name for this would be "all-attached infinity gauntlet (with magicband icon support)"

also considering, so as for this print to have not been a waste, figuring out a way to articulate the oversized gauntlet kind of like the Marvel Legends one - more on that on [the general hand page][hand]

[hand]: nrj3m-eesg0-m897x-rs36q-n36t6

I might do other tweaks - the fingers do all appear to be joined by one piece (which I believe is where the "knuckles" of this design came from, not noticing that they're all joined if you go a bit further), so I might make something like that (how? no idea) to have as the basis for all the fingers

## notes from immediately after I wrote the notes below

- Need to print new tests of the cutout plug with .2 gap and even just 0 gap (which might be fine for a component this small - my test at an angle found that it did feel simpler).
  - Should see the effect with the lower temperature and a horizontal print.
  - Remember: even testing a 0 gap, there's still .25 around the dish.
- Gotta remember to make a Git commit of all this.
- The split-out nature of the file should be restored to a merged file.
- Should print some 99% scale et al versions of the original MagicBand holder, to see if it might be tighter (either for a new snap-in or for a front-and-back assembly).
  - Hey, this might work as an enclosure for that Tower of Terror holder!

## old notes that led to this current pop-out-dish design

from the first revision, I'm thinking I'll print this part of the gauntlet with the footprint of the holder cut in half, and only the lower part included as part of the model (kind of like how the back of a MagicBand's holder itself is removable), with three holes for wires and/or screws (eg. 2 wires and one screw per side, possibly) from the back within the outline of the holder. This will be able to hold:

- The outer ring for the holder (friction fit into the slot, or screwed in / elastic if that doesn't work out for some reason)
- A plug that fills in the "original" design for the gauntlet from that point on the surface up (as the holder part will only be part of the gauntlet on the inside)
- A "hybrid" design that puts the Mind Stone cover on top of the MagicBand icon, possibly with some kind of coil to detect NFC and a few LEDs to light up
- (in a theoretical redesign for compatibility that I don't feel like doing, or possibly concidence our of dumb luck) The back-of-band holder from an actual MagicBand

ah heck, we might as well make it 4, that way we could potentially use the holes for armature to the fingers. also do SPI or whatever

looking at the design now, I notice that the stone is encircled by an almost perfectly circular ring...

a quick thing I did was move the gauntlet down by .5 (or move this up) and then this cylinder fits the circular carveout almost perfectly:

I might even be so bold as to just target the footprint of the inside and use the design there as the lip itself.

having a nice round cylinder as the base has a few more perks:

- the base can be resized for tolerance
- the entire surface can be redesigned (ie. I can just use a flat plane, rather than intersecting the little bits of the stone holder that remain in the current design)
- mixed-material concepts like a TPU socket won't look as jarring
- more room if I decide I actually want to raise the icon part higher (I can design a whole new second level)

I'm even considering knocking out this entire piece of the gauntlet solid, but... eh... maybe I'll make it like a GIANT version of the original socket, with a smaller hole for the "back of the part" (maybe shaped to assist orientation - I'm thinking an echo of the original oval, but larger so it's enough to fit the MagicBand icon holder), and then, hey, maybe I could have the whole piece holding the icon be one big removable part. that way, I can just snap (ba dum tss) it in, like the orignal holder design, or I can print a multi-layer assembly, or whatever - including more complex circuitry holders