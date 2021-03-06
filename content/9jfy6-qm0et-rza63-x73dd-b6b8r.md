# USB WS2812 Strips

Is it possible to use a DigiSpark to just expose a USB character device that takes in the, what, 16-bit BGR color strings used by WS2812s, and blits that when you write/sync?

I think the problem here might actually be that the digispark board isn't capable of driving WS2812s?

looking at [TinyGo's feature support](https://tinygo.org/microcontrollers/digispark/)

no, it seems to be possible, [this hack did it](https://www.instructables.com/id/USB-NeoPixel-Deco-Lights-via-Digispark-ATtiny85/)

oof, though... the 512-byte RAM limit

## What'd be interesting

What if you made essentially one of those LED lightup USB cables, and used the on-board flash to let the user write their own LED state, and you could switch the strip to "power mode" which reconnects the host end from the client end to the controller (for reprogramming the animation)

you could also maybe switch the lights on/off

## animation format?

The idea of using this as a client running a standalone routine makes me wonder: what would be a good format?

so obviously, each frame needs to describe the entire buffer to write

the straightforward one is just "bitmaps", but considering the small amount of RAM, can we do better?

(i'm not sure, actually - it's entirely possible that the WS2812 timing stuff is not capable of generating values procedurally)

(though could it be rewritten to be so?)

you could have transition functions both for colors and time interval, but I do think you'd have to have a whole frame animate at once - let's consider the idea that you can't have multiple simultaneous timer values, and you just have to pack overlapping variables into regions that describe the trajectory of that variable within that region

pure linear transition would be another simple-enough thing, but, like, what are our other transition-function options? you'd look at CSS and SVG gradients and animation and SVG path definition for compact definition formats...

oh snap, if you overlay them this starts to turn into a JPEG function thing

anyway the idea is that this would allow for smooth "flow animations"

also "repeat this pattern X times" seems normal

you know what might inform this design adequately? image formats

## What you could use this for

was thinking of this for various "smart applications" like [HiveBug's face](ech2q-n3zmw-ppar5-g5q1k-8p8cy)

could also maybe extend it to handle the epaper display as well?
