# dockitall

## current status

(as of 2020-01-24) Functionally, the dock's pretty sound at the moment: I took care of all the ideas I wanted to implement, and all the worst narrow-case hacks (like only supporting completely round cables) have been taken care of. Implementing the proper corner radius implementation takes care of my biggest peeves with the old design: circles can't wreck bounding boxes with non-mod-4 facet counts (letting most of the model's detailcome from `$fs` and `$fa`), and single corners can take more radius than half their side.

The "open back channel" is still not re-implemented:- that's something I should consider revisiting, since there's still currently vestigial code supporting it (and it's not a bad design, if you have an unwieldy cable that can't curve tightly, or if there's a Y offset, or if your cable just won't fit well using the hourglass hole).

On top of general renaming / cleanup, the next (and final before merging to master) feature I want to take care of is using the "curved points" function to define the front face, the side walls (now called "cheeks"), and the back (which will be much taller).

The back wall will probably be just a rectangle, though

If the curved-points function did get overhauled to take arbitrary angles, it'd work well for a clean redefinition of the hourglass hole that wouldn't need a `$fn=24` hack (though it'd still need a high resolution, the facets'd be correct without having to do any hulls). That'd require *so* much more *math*, though...

## stuff to clean up

- Honestly, the rectangle definition functions have gross names
- `_cr` should be "corner_radius" if not "bezel" or "fillet"

## past status

earlier notes from the 0.2 refactor [here](px77n-rwns2-m29b8-ehj5a-zwvr0)

## build notes

[all the head-against-the-wall issues I hit with the dual-extrusion](kyv1t-6bfjr-4raz6-pqmj4-yzf0q)
