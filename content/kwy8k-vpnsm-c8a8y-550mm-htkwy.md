# Changing the Username on my PocketCHIP

This is just a plan right now:
- edit the autologin user line in /etc/lightdm/lightdm.conf
- do the username change
  - username command: `#TODO`
  - change / move home directory: `#TODO`
  - I believe I found these on StackOverflow on my phone?

looking at how some of the files in `/etc/skel` have `/home/chip` in them... I don't feel like I want to risk making waves with my system's stability.

## back and forth on having my own user

I am going to make a new user for myself with a password, though, and I'll keep my good work under that -

nah, who am I kidding? I'll just treat this under my same "revoke keys from devices via GitHub" rule. It's not like I'm encrypting this at rest, there's no truly sensitive data on it other than revocable keys.

## the final forth: sharing is caring

oh, right, it's not about convenience - it's about privacy, if I'd like to share this device like my Switch. the Chip account is like the "community account", a shared anonymous space where you're not expected to make waves. Like its own little local BBS.

and my personal user is my personal private space, like a room with a lock. Hence why keeping chip's autologin

anyway, that's why I wouldn't change the password for Chip... though, as I said, I might remove it from the wheel group, so I could be sure that people can't mess with the system's sensitive hardware settings. (This is the true meaning of root in the modern age: a parental lock.)

Actually... on that thought, I might make chip password-protected, so that I could log *out* as Chip to the LightDM login window to put the device into Guest-Only mode in a "less trusted community" setting (where I might also turn off autologin).

So, in this formulation, `/etc/skel` pointing to assets under `/home/chip` actually does make sense

## aside about LightDM's guest/temporary users

Ooh, do guest users not get added to the `users` group? That would be tight

## Continues

[Expanding Horizons](4b9cv-hse5y-yra54-5pfww-pzgcg)
