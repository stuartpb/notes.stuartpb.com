# August 2019 stushiba AUR cleanup

Wanting to update my Minecraft launcher, I've decided to `yay -Syu`

```
:: Searching AUR for updates...
 -> Missing AUR Packages:  gtk-xfce-engine
 -> Orphaned AUR Packages:  lib3ds  scribes
 -> Out Of Date AUR Packages:  minecraft-launcher  xi-core-git
:: 11 Packages to upgrade.
11  aur/brother-ql700               1.1.4r0-2       -> 3.1.5r0-2
10  aur/docker-machine-driver-kvm2  1.2.0-1         -> 1.3.1-1
 9  aur/earlyoom                    1.2-2           -> 1.3-1
 8  aur/google-chrome               74.0.3729.157-1 -> 76.0.3809.100-1
 7  aur/minecraft-launcher          2.1.3676-1      -> 2.1.5965-1
 6  aur/pencil2d                    0.6.2-1         -> 0.6.4-1
 5  aur/scite                       4.1.5-1         -> 4.2.0-1
 4  aur/signal                      1.19.0-1        -> 1.26.2-1
 3  aur/tea                         47.0.1-3        -> 47.1.0-4
 2  aur/ttf-merriweather            1:2.003-1       -> 1:2.005-1
 1  aur/yay                         9.2.0-1         -> 9.2.1-1
```

Holding back:

- scite: I mostly installed this because, several years back, I knew it from Lua for Windows. I don't think it was AUR at the time
- tea: Another text editor I installed way the hell back when also not an AUR package, IIRC.
- signal: as I said [a few days ago for stuzzy](c3q4x-zwgbz-4xax0-npy3h-6hhw9), fuck Signal Desktop

I'm also going to get rid of `scribes` (another text editor I no longer care for) and `gtk-xfce-engine` (no idea what happened with this or what it did).

Keeping `lib3ds` because it's a dependency for Meshlab (which I do sometimes use).

Installying these updates got `go`, which I'm keeping because, you know, I ought to get around to doing some Go stuff.

Running `yay -R scribes scite signal tea gtk-xfce-engine`
