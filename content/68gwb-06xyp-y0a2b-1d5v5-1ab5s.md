# MicroSD ring

https://www.thingiverse.com/thing:4138303

this is an idea I had a while back (the MicroSD iterations of sturling used one I made with I think a bit of coat hanger and some Sugru), but only just now got around to looking into 3D printing

so I typed "microsd ring" into yeggi, and sure enough, someone had uploaded almost the exact design I would have initially described, just a few months ago, to Thingiverse

## take on that model

ti's a little tighter on the USB port than it needs to be

it's also a little big on the finger

and the bottom layer printed weird with support

## next steps

parametric version. some features I'm thinking:

- things that should be parameters:
  - port dimensions
    - like, this same basic design could hold a design with a [Tomu](fawec-tqrtg-469sx-k9wrj-hbt8j)
    - but yeah, also, adjustments for tightness
  - bevel radius
    - multi-dimensional and per-component would be cool
      - multi-dimensional because, like, the sides of the opening on that model from Thingiverse are lower than the front/back
    - ie. this is implemented by minkowski summing with the hull of 8 transformed spheres for the box, and 2 cylinders summed with spheres for the ring
    - also note that we subtract the bevel dimensions before summing, of course
  - ring size
    - the width of the band
    - diameter
    - maybe allow a "degrees to remove", like with the watchband design
    - also, allow that cut to be diagonal / otherwise overlapping in some fashion
  - position offset for box relative to ring
    - printing the top of the slot level with the ring isn't a bad idea
    - like, it's centered in the original design, which makes it off-center once the MSD reader is inserted
      - also, this centering means you have to add suooprts for clearance
    - I had the same problem with my Sugru version
- hull between ring and casing piece
  - another way to fix the "few layers of support material" problem
- maybe fancy shell designs that obscure / hide the reader
  - like jewels or a skull or something like that. A shield / face
