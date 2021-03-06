# Steam-Like Internal Redistribution Service Model/Idea

I'm looking at the Bundle for Racial Justice on itch.io, and like "wow, it sucks this isn't Steam", but like, I'm looking at it like "it totally *could* be

you could hook its metadata service up to (you could use some kind of double-hash for discovery vs. proof of ownership)

so, the technical model would be that the daemon is authorized to your account via API or you give it your password or whatever. and maybe it reads from a collection.

but it downloads the game over the HTTP link from itch.io (who don't seem to provide torrent links like Humble?)

and then, you know, your local "gamemaster" node is the one who keeps the game "on the shelf" for you, you don't have to have it mounted directly on your local machine, it can be paged out to your cheap large-bulk-storage node, which distributes with other nodes over BitTorrent

the logic is, this is fine, because you got the hash through your legitimate copy of the file. It's gotta be okay to ask the cloud "hey, give me this tree I've already read and identified"

Similarly, this "package source hash" is the identifier for "repack" or "reencode" or "patch" services - they operate (in what I think constitutes a secure fashion) requiring you're acquainted with the hash of the processed file

So, like, if I want a service that gives me the latest version of Crazy Taxi With The Ads Put Back In, Which Is Almost Certainly Legal For an End User to Do, I have to know the upstream hash for the "verified licensed" sourced

of course, the user can always set themselves as the "verified licenser" of whatever source, but, you know, honor system

and, you know, it's like Helm, you can add whatever repositories

## ok so yeah

is Kodi like this? IS there a system that does a Popcorn-Time-level good aggregation of movies, TV, games and more (OH GOD RETAIL FLASHBACKS)

but seriously, something that has Netflix-level usability for movies, and Steam-level usability for games

using a Steam model for movies (but with Streaming, which, damn, I haven't seen a *game* support in forever, people got used to downloads as physical media transportation vanished really)

but also, there's a Web frontend for streaming movies to a Chromebook

and there could also be a web frontend to something like Steam Link or Steam Play or whatever (I think Steam Play is Proton?), that forwards media and your peripherals

This would prefer a dedicated node... fuck, do they still sell Steam Machines? That'd be perfect, since this is

One thing I think would be really cool - and this could, actually, be facilitated with a little Windows Kubernetes cluster (though, ugh, I'd hate to have to share a Cluster with it - it'd have to have `taint: windows`, which would hopefully keep Daemons away) - would be to have two "Gaming Minisets", with one running AMD and Linux, the other running Intel + nVidia + Windows (for maximum hardware diversity)

you could even have the Windows node run Docker (I think it kind of has to, actually)

## getting into that cluster control plane idea specifically

so, like, you'd have a DaemonSet or something handle connection to the onboard "Daemon" (that would be this whatever-torrent-and-itch-thing, or Steam), that would act as a "Controller" on objects that... I suppose, considering that this cluster wouldn't have much else going on, custom resources accessible over the cluster API? or, you know, you really reserve that for "hypervisor" stuff

## one thing you'd want for games

A model of how they persist data would be cool, though yeah, is there a cheap way to swing "just put a btrfs overlay over the game directory image mount and then store that as a Longhorn volume to move around or something like that"? (another reason I'm mixed-cluster-skeptic here)

## on the "Link" modeling

In this dual-node configuration, you could have it so each one is hooked up to a KVM and/or each other. So video encoding doesn't have to take any resources on the machine running the game, which can be either system. (or a system streaming a little minesweeper could keep to itself while the other system goes to its max)

You might also do something with, as I said, KVM, and Vive, and even multiple-cards-one-machine-different-profiles? Like, one hypervisor could still boot the Windows/Linux setup, and it might even be easier to facilitate the cross-machine access

and of course you'd be freet o run Linux on both

and needless to say this also ties into stuff with Twitch et al streaming and video conferencing and other services you might want to provision-on-an-appropriately-specced-node

## anyway, the end goal here

this makes it smooth to game on a Chromebook - even when your machine is halfway across the country (well, as smooth as that can go)

but it should also be smoother-than-Sling to just straight-up install the game locally and try Proton if you've got even ChromeOS Penguin

or even try in-browser emulation, for the retro games - I think Emscripten or whatever is cool for this?

I think my model/idea for the game service side of this was built around the idea of "say I have a torrent that contains the entire game library for a given system"

(also, if you have this, you might want to also have some kind of at-rest encryption)

but yeah, you can also have a torrent that's just one game. and maybe it's a PC game, or something like Wolfenstein or ZACH-LIKE or DOOM or (to pick a totally different example) Hypnospace Outlaw (which, right now, would ship with "Prefer Windows, If Linux Fall Back to Older Working Version until Bug X is Patched")

This also identifies a junction with [kublam](nh005-8sp5r-0fay4-1yt9r-fqegw)

## sort of unrelated thought, for temporary keys

is there an off-board clock chip that can remember a secret until a given time, at which point it guarantees it will be erased? and it can have the time reset on the given system, but the secret can only be extended by opening a new temporary secret

Like, you could wire this up via the GPIO headers to the Raspberry Pi that controls the cluster - maybe even mount it into the filesystem, somehow? under /proc or /sys? or /dev?

or I guess this could be a cluster-wide service. anyway you'd use it for sessions, and I guess maybe certificates somehow?

could you even do this on a FlashAir as the ntp-following expiry daemon?

I guess Redis is kind of a software version of this
