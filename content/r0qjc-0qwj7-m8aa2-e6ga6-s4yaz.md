# updating again

running `yay -Syu` to get Inkscape 1.0 (amidst other updates)

What the heck, let's upgrade all our AUR deps

## Raleway

Looking at https://github.com/impallari/Raleway and seeing it hasn't been updated in years:

looking at https://github.com/impallari/Raleway/network it seems https://github.com/mikedug/Raleway is the most-maintained fork, building off of https://github.com/TypeNetwork/Raleway

anyway, going with `otf-raleway` for now, since that's at least a stable release

(Double-checked [previous notes](5023e-abv8a-tdaxa-ajjwz-aavg0) to confirm this font didn't have a known issue)

## Package issues

had to skip these due to a source hash mismatch:

- ttf-chunk
- f2c
  - checking: this was installed for `levmar` which is an optdep for meshlab

removing ttf-caladea because it now conflicts with ttf-google-fonts-git

install kept choking on ttf-fanwood vs otf-fanwood (they're in conflict, and league-fonts has switched from having one as a dependency to the other), so I'm uninstalling and reinstalling league-fonts (same thing would have likely happened with junction and raleway which I was also prompted to uninstall the ttf versions of)

had to do this with pebble-sdk too (chokes thinking pebble-tool-git is in conflict with it when it's now a dep)

## afterward

(note that I reran `yay -Syu` a bunch of times in the process of dealing with all this)

removing ttf-chunk since it's redundant to otf-chunk which is in league-fonts now

had to cleanBuild f2c, not sure what happened there but the clean build fixed it
