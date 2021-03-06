# printer upgrade

want to install new firmware

printed new selector, blade holder, and PTFE front (since the track in the old one had been gouged to hell by the blade coming loose in the lousy old holder)

rather than using the IP any more, I want to access the printer by mDNS, so I'm [rolling out systemd-resolved](hs8v7-1s5bg-8y8nn-a0e32-gvh8c)

once that's done, I go to `octopi.local`

updating the Prusa thumbnailer

installing the Firmware Updater plugin, it prompts me to reboot

## more setup after rebooting

following the directions from https://github.com/OctoPrint/OctoPrint-FirmwareUpdater

`ssh pi@octopi.local`

```bash
sudo apt-get update
sudo apt-get install avrdude
```

## configuring the plugin

clicking the wrench in the plugin dialog under OctoPrint Settings

using specs described at https://forum.prusaprinters.org/forum/original-prusa-i3-mk2-s-others-archive/octoprint-firmware-upgrades/#post-91319 - confirming against https://shop.prusa3d.com/en/mk3mk3s/925-einsy-rambo-mk3s.html

setting board to ATmega2560, path to avrdude at `/usr/bin/avrdude`, and an AVR programmer type of `wiring`

## flashing the firmware

I download the firmware from prusa3d.com/drivers and upload it to this interface to flash

## oof

Timeout communicating with printer... right, this shit. Trying to do this over the onboard serial is a bad idea, considering that the firmware version it currently has is prone to issues on that interface? I'm gonna go ahead and flash these from Stuzzy via USB. Let me just go ahead and find a USB A/B cable

## done

Found one right next to the label printer I still haven't set up. Plugged the printer into Stuzzy, flashed 3.9.0 (and waited for verify), unplugged, grabbed a MicroUSB cable, plugged that into the MMU2 and flashed 1.0.6

okay, so, I'm gonna just leave it like this overnight and see if it isn't dead with too many timeouts in the morning. if it is, I'm gonna try reflashing OctoPi (because this is just a slow piece of junk at this point), and if *that* doesn't work I'm gonna swap this Zero out for a Raspberry Pi A

actually... let me cold boot this first, getting weird loops with the MMU2 like I was earlier
