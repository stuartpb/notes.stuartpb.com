# setting up stukrates

## operation take 2

I took everything in Downloads and zipped it up, then transferred it to Stushiba via https://file.pizza for archiving

The closest thing to a Tabalanche export is in there - the tabs are missing when I check it now

repeating [the procedure I followed last time I opened one of these machines](fh5p2-pg37g-jg9fx-rej49-spq4t)

Lost one of the screws for the heat shield, but that works out as the instructions say not to reinsert #5 anyway

was down to only half of the 2.5x7 screws, so replaced them all with black screws from a kit I bought on Amazon

stashed my one profile from Pull Tool

following https://mrchromebox.tech/#devmode now

hitting ctrl+alt+f2 for a root shell

installing full ROM firmware; saving original as stock-firmware-SWANKY-20200228.rom via USB stick

hitting r to reboot brings me to coreboot

flashing k30s 0.9.0 to the flash drive I used for the fw backup (1 GB storage)

dumped to shell: https://mrchromebox.tech/#faq explains that hitting esc brings me to an intelligible menu

## booted into the k3os live

- logging in as rancher
- doing `lsblk` to confirm the disk I want wiped is `mmcblk0`
- doing `sudo k3os install` and choosing install to disk, to mmcblk0
- n default for cloud-init file
- authorizing stuartpb via github
- configuring wifi for both channels because heck why not, also setting up for my hotspot in case of emergency
- "run as server or agent" seems like a master-node selection thing? anyway, default 1, server
  - https://rancher.com/docs/k3s/latest/en/architecture/ explains
- hitting enter on token or cluster secret, assuming it'll generate one
- everything installed

## once installed

okay, looks like I can't log in as rancher at console anymore

logging into my router for DHCP clients, adding a new reservation (192.168.0.35) for stukrates, then logging into the current IP via ssh from stushiba

`sudo cat /var/lib/rancher/k3s/server/node-token` outputs something, so looks like that's working

`sudo bash -c "echo 'hostname: stukrates' >> /var/lib/rancher/k3os/config.yaml"` as an attempt to set hostname

`sudo reboot`

oh wow, this changed DHCP reservations easily

committing notes, switching to stuzzy and going to bed
