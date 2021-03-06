# On the idea of the notes workspace introducing "self-hosting notes"

This came up as I was trying to phrase the link to [my inaugural notes-workspace notes](2ncae-q5n8d-9da26-enghc-n8ydb) on the cluster-changelog root

Having this be on a solution I'm actually proud of, that I have faith in, is making it so I can, for the first time, document the system I use to write these notes in the faith that it'll be a reproducible artifact.

Oh shit, that's totally what this is: this Kubernetes system is the first time I've had a meaningful "system", on my own hardware, **that is defined in the abstract** - something where I can publish *my entire end-to-end setup* if I felt like it - as in, *the entire system*, as it's all defined by declarative documents.

Of course, there's still the data that's in PersistentVolumes, but that's an *abstract format* that is *cleanly separated from the definition* - I can hook that drive with all the PV data to *completely different hardware* (ie. it could be a different *processor architecture*) and it would work.

I don't need to worry about hardware bitrot the way, like, *every system I've built in the past* did.

## Anyway, where I meant to go with this

beyond the "why"

I can write *my whole process* now, like "I type bagtent-conjure and then open a file". Like, now that it has a *dedicated system* for it, one that doesn't live on any one node (or, at least, doesn't have to - this is one side of the idea for setting everything up in Flux, so the setup is truly backed up properly, rather than just trying to retrace my steps)

like, I can observe "hahaha, I'm instinctively tossing my cursor to the corner to switch out of notes context, even though it's a pinned tab and this is ChromeOS"

Now that it's a "cloud installation" that isn't tied to any one installation (even though it's all on *my* hardware, it's still an ephemeral "cloud" unit)

## Future-forward thinking

Like, not only all that stuff, but also, like, the level at which I set this up was pretty high-level - I don't feel like there's going to be a radical shift in most of the stuff I described any time soon, unlike, say, distro tastes. Ubuntu can gradually suck over time to the point that you can need to shift to a different distro *just because Ubuntu was no longer maintained*. See, now, if that happens with a Kubernetes distro? I jump distros, and *the entire setup comes with me*. And each of my applications is in a pipeline where they choose their *own* distro.

## more stuff I can do in faith now that I have this setup

- Build on it, period!
  - All my old solutions were totally janky, put together more in triage than anything else, because I didn't want to get too attached to a mortal system's dev environment.
- Autocommit! Like, now that I can have an actual h2g *always-on daemon* for my notes, I can think about reinstating the
  - I might even just implement this with an always-running process on `screen`, and I make it so the workspace connects to that screen on startup?
    - I think there might be an extension that makes terminals work like this
    - I'm starting to think this is probably how Cloud9 did it; didn't they actually use tmux?
  - Might also consider auto-saving!
    - I feel like... I'm not quite ready for that.
      - Maybe auto-saving-and-committing-to-a-dev-branch
        - Oh man, I would *love* to have editor views tied to Git branches
          - [continues](ycgks-z5ppw-8jawd-gsy2k-6wpm5)
- Rely on editor settings for this folder!
  - remember: .vscode/settings.json
- Rely on extensions - and develop my own!
- Not have to stop writing notes when I go to bed!
  - Honestly, this one's likely to become be the game changer
  - Looks like it miiight kick off previous instances when you switch to a new browser?
    - but that's, like, fine, considering it saves all the work (which it does because it's VS Code)
    - Oh wait, that means I'm gonna have to do setup-shell a lot...
      - unless I set up a profile that sets up the shell by default! I'm okay relying on customizations like that now!

## More on that thought struggling to be born here

The thing that makes the Cloud so enticing, that this paradigm-of-personal-clusters captures, is that *it's always updating*. Not just the software, we've had auto-updating software forever - the *hardware*. Your average user has had to abandon their whole desktop setup every time they've switched computers, and try to recreate it from memory (while adapting to whatever shit's changed in the new version), and that's become less of a pain in the ass *because so much happens behind a web browser now*.

With this, changing the backing hardware *does not impact the hardware you hold to use it*, and *does not change the interface of that environment*. Since the defined structure is *purely* technical (all the UI-touching bits are part of the browser environment), you don't need to worry about learning a new "back is a gesture instead of a button now" just because your old computer wasn't fast enough to maintain all the stuff you wanted. (And again, clusterability.)

## Thinking about the owner-service idea again

(this is for Ops in a Box again)

Now that I've done it myself, I'm thinking you'd have something like an Ingress controller that sets up a sidecar that forwards through a tunnel to... you know, that app I saw earlier did have hostname switching, how is it at that?

(Can multiple connections go through this tunnel?)

And it'd also run an external-dns that would connect to a skydns instance (dedicated, like Cloudflare's [Bob-and-Lola][] model, except this is which node you connect to to set records through) to set the backing domain info

[Bob-and-Lola]: https://blog.cloudflare.com/whats-the-story-behind-the-names-of-cloudflares-name-servers/

the reason for skydns is because etcd is HTTP which means it can be segmented per-end-user (especially if we use etcd's rbac). not sure if there are other endpoints that can work like that (not sure about the DDNS RFC)

you'd also use whatever record thing external-dns has (iirc) to set up, you know, MX records and stuff like that that you might want. TXT records.

this would all have a dedicated dashboard

you'd have the same model for domain ownership as Cloudflare I think does: you make us the authority for the DNS

- Hey, wait a tic, can I just use ExternalDNS to set Cloudflare records that point at my apartment?
  - Can I set it up so only Cloudflare is authorized to make a connection set up through DDNS?

Hmm, I wonder how much of this I could prototype on my free Packet credit...

## another note for the "Hardware SKU" consideration for this ops-in-a-box thing

yubikey. there should be a configurable level of "how secure do you want/need this to be"

## why vs code specifically?

Here, unlike Theia or C9 (not sure which is worse), I know I'm building on a platform that people *use*. Now that I have *this specific working fully-open pipeline*, I can rely on planning "how can I add this editor feature to make my life better" and not have to worry so much about how I might implement it in other editing environments (since I don't have to worry about needing another environment, since it's in-browser and safe, as described above)
