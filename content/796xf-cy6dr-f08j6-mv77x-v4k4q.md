# Printing NeckFX Pearls

After trying to build plates as [individual pieces](samgd-1g07x-mc94y-997wg-ypkcj)

## anyway

I think the humidity is making my transparent PLA really-expandy.

I was trying to print a plate of 12 halves: I kept getting the first layer gumming up on itself.

First I tried tightening the idler, which made it happen a little later (one or two would print): then I tried raising my live Z, which only kind of worked at absurdly high offsets like -0.4 where the plastic was practically coming off anyway.

I also tried upping my mesh leveling to 5 reads per point: this did nothing

bumping the temperature up to 220C for the first layer solved the problem

## End of 2019 Post-MMU2/Textured-Plate/Move Redux

I'm having issues getting the pearls to print nicely again. I printed a shroud and dehydrated the PLA filament (before I even started on this, as it had gotten so brittle I was wary of sending it through the MMU unit), but I'm seeing symptoms of the earlier issues, only worse.

I was experimenting with some new techniques for printing the pearls (thinking that my new textured build plate could maybe allow for some techniques I couldn't pull off with the smooth one, like a proper raft layer), but they kept coming out wonky: I figured I might need to give the layers more time to cool, so I decided to try a 12-up plate.

I went back and grabbed the 3mf for the plate that worked before, tweaking settings that'd work for this printer (and setting the first layer temp back to 215).

Printing at 215 for the first layer worked (nice thing about the textured build plate), but a couple pieces pieces came loose around 3mm, and of the pieces that weren't loose, the edges were rough and puckered, lifting more the farter you got from the center.

Tried with the smooth build sheet to see if that would (somehow) fix it, and ran into the same gumming problems as before, even printing the first layer at 230C. I noticed that the filament was rolling on the silicone sock I have now, so I tried taking that off, but, of course, that only made the problem significantly worse

## anyway

turns out the gumming problem was more directly related to the bed temperature. bumping to 70C fixed it. also, lowering live Z helps

After watching a print progress, I could tell the puckering issue stems from cooling trouble - the print cools less behind the nozzle. removing my E3D sock lessened the problem.

incidentally, this explains why only one side of the pearls looks "deformed", and why you get busted results if the tabs aren't printed toward the back - if you print the other way, the channel gets deformed
