# Switching Stunster to systemd-networkd

```
stunster:~ # transactional-update pkg install systemd-network
Checking for newer version.
transactional-update 2.24.1 started
Options: pkg install systemd-network
Separate /var detected.
/etc on overlayfs detected.
Syncing /etc of oldest snapshot /.snapshots/13/snapshot as base into new snapshot /.snapshots/25/snapshot
Calling zypper install
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following 4 NEW packages are going to be installed:
  libgnutls30 libhogweed6 libnettle8 systemd-network

4 new packages to install.
Overall download size: 2.1 MiB. Already cached: 0 B. After the operation, additional 4.2 MiB will be used.
Continue? [y/n/v/...? shows all options] (y):

Checking for file conflicts: [......done]
Warning: 4 packages had to be excluded from file conflicts check because they are not yet downloaded.

    Note: Checking for file conflicts requires not installed packages to be downloaded in advance in
    order to access their file lists. See option '--download-in-advance / --dry-run --download-only'
    in the zypper manual page for details.

Retrieving package libnettle8-3.6-1.5.x86_64 (1/4), 146.9 KiB (323.1 KiB unpacked)
Retrieving: libnettle8-3.6-1.5.x86_64.rpm [.done (3.8 KiB/s)]
(1/4) Installing: libnettle8-3.6-1.5.x86_64 [.........done]
Retrieving package libhogweed6-3.6-1.5.x86_64 (2/4), 229.3 KiB (351.5 KiB unpacked)
Retrieving: libhogweed6-3.6-1.5.x86_64.rpm [.done (10.4 KiB/s)]
(2/4) Installing: libhogweed6-3.6-1.5.x86_64 [.........done]
Retrieving package libgnutls30-3.6.15-1.1.x86_64 (3/4), 860.1 KiB (  1.8 MiB unpacked)
Retrieving: libgnutls30-3.6.15-1.1.x86_64.rpm [done]
(3/4) Installing: libgnutls30-3.6.15-1.1.x86_64 [............done]
Retrieving package systemd-network-246.4-1.1.x86_64 (4/4), 952.0 KiB (  1.7 MiB unpacked)
Retrieving: systemd-network-246.4-1.1.x86_64.rpm [done]
(4/4) Installing: systemd-network-246.4-1.1.x86_64 [............done]
Additional rpm output:
Running in chroot, ignoring command 'daemon-reload'
Running in chroot, ignoring command 'daemon-reload'
Running in chroot, ignoring command 'daemon-reload'


Trying to rebuild kdump initrd

Please reboot your machine to activate the changes and avoid data loss.
New default snapshot is #25 (/.snapshots/25/snapshot).
transactional-update finished
stunster:~ # transactional-update shell --continue
Checking for newer version.
transactional-update 2.24.1 started
Options: shell --continue
Separate /var detected.
/etc on overlayfs detected.
Syncing /etc of oldest snapshot /.snapshots/13/snapshot as base into new snapshot /.snapshots/26/snapshot
Opening chroot in snapshot 26, continue with 'exit'
transactional update # systemctl disable wicked.service
Removed /etc/systemd/system/multi-user.target.wants/wicked.service.
Removed /etc/systemd/system/network.service.
Removed /etc/systemd/system/network-online.target.wants/wicked.service.
Removed /etc/systemd/system/dbus-org.opensuse.Network.Nanny.service.
Removed /etc/systemd/system/dbus-org.opensuse.Network.AUTO4.service.
Removed /etc/systemd/system/dbus-org.opensuse.Network.DHCP4.service.
Removed /etc/systemd/system/dbus-org.opensuse.Network.DHCP6.service.
```

At this point I hop over to https://jlk.fjfi.cvut.cz/arch/manpages/man/systemd.network.5 and oh dip I just noticed this (emphasis mine):

> SendHostname=
> When true (the default), the machine's hostname will be sent to the DHCP server. Note that the machine's hostname must consist only of 7-bit ASCII lower-case characters and **no spaces or dots**, and be formatted as a valid DNS domain name. **Otherwise, the hostname is not sent** even if this is set to true.

So I guess I'll need to manually set the hostname in this file to ensure this doesn't break the local DNS?

## here's where I get the main idea

Honestly, I'm thinking, if I can get this to work, I might just tear down the cluster, reset all the hostnames to be just the short names, and let search domains be the discovery mechanism - I think that's the smoothest experience, all things considered. It all depends on the systemd-resolved integration, though (to whatever extent kubelet needs to resolve cluster names)

though I also feel like I dug deep into hostnamectl at one point and felt like this ought to get culled down to its short name somewhere in networkd's guts? ah, whatever, that's not what the docs say happens

I go ahead and do this in the transactional shell:

```
echo '[Match]
Name=en*

[Network]
DHCP=yes
DNSOverTLS=opportunistic

[DHCPv4]
Hostname=stunster
UseHostname=no
# used for interface priority
RouteMetric=10

[DHCPv6]
RouteMetric=10
' > /etc/systemd/network/80-dhcp.network
```

then I exit and reboot.

```
transactional update # exit
exit

Please reboot your machine to activate the changes and avoid data loss.
New default snapshot is #26 (/.snapshots/26/snapshot).
transactional-update finished
stunster:~ # reboot
Connection to stunster.403.testtrack4.com closed by remote host.
Connection to stunster.403.testtrack4.com closed.
[stuart@stushiba ~]$ ssh root@stunster.403.testtrack4.com
Last login: Fri Sep 25 08:25:58 2020 from fd5a:b141:6779::101
stunster:~ # hostnamectl
   Static hostname: stunster.403.testtrack4.com
Transient hostname: stunster
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: 7c4b8429cd2442488f60fe75b6102b68
           Boot ID: 8b6f86a445094f2ebfe3d3e89892d9aa
  Operating System: openSUSE MicroOS
       CPE OS Name: cpe:/o:opensuse:microos:20200923
            Kernel: Linux 5.8.10-1-default
      Architecture: x86-64
stunster:~ # hostnamectl set-hostname $(cat /etc/hostname)
stunster:~ # reboot
stunster:~ # Connection to stunster.403.testtrack4.com closed by remote host.
Connection to stunster.403.testtrack4.com closed.
[stuart@stushiba ~]$ ssh root@stunster.403.testtrack4.com
Last login: Fri Sep 25 12:17:57 2020 from fd5a:b141:6779::101
stunster:~ # hostnamectl
   Static hostname: stunster.403.testtrack4.com
Transient hostname: stunster
         Icon name: computer-laptop
           Chassis: laptop
        Machine ID: 7c4b8429cd2442488f60fe75b6102b68
           Boot ID: 7c97c89fcfed4a238bd97d91fe6bc456
  Operating System: openSUSE MicroOS
       CPE OS Name: cpe:/o:opensuse:microos:20200923
            Kernel: Linux 5.8.10-1-default
      Architecture: x86-64
```

gahhhh. well now at least I know it's not coming from Wicked

## update

name resolution broke after doing this switch - I forgot the first two lines of /etc/resolv.conf under Wicked:

```
### /etc/resolv.conf is a symlink to /var/run/netconfig/resolv.conf
### autogenerated by netconfig!
```

and, since /var/run is ephemeral under kubic...

fixed with `ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf`

I'm putting all this under a script in stubernetes-setup: should be executable with something like `transactional-update run bash -x <(curl https://raw.githubusercontent.com/stuartpb/stubernetes-setup/master/setup-systemd-networkd.sh)`

Anyway, I've started rolling back the cluster by doing `kubeadm reset` on stunster

rollin out [403 Network Setup for October](cwt92-9k4sd-es85j-9swb0-2f8jj)

## wait, name resolution worked?

when I tried this on sturl, I had a couple issues:

- the `Name=en*` thing doesn't appear to work, due to different device name handling on the Pi I guess? (the example came from the man page)
  - I'd found some issue describing how `Type=ether` would sometimes not match ethernet devices, but that issue seems to have been patched
  - Anyway, switched by switching to `Type=ether`
- it seems to have some issue with DNSSEC failures?
  - allow-downgrade should make this okay, and, like, it was on stunster...
  - but I guess I rolled out the search domain after that? a properly configured search domain might have caused the validation failures?
  - but, you know what, it's overhead we don't need if we're trying opportunistic DNS over TLS anyway, and our in-house resolver is TLS to trusted IPs
    - also, one less thing to worry about as I explore internal DNS stuff
  - I've modified the script to add `sed -i '/#? *DNSSEC=/{s/^#//;s/=.*$/=no/}' /etc/systemd/resolved.conf`,
    - which I run on stunster

also, why was systemd-resolved enabled on stunster? oh I guess I forgot to mention that I enabled that after forgetting?

the new command to run this is `transactional-update run bash -x <(curl https://raw.githubusercontent.com/stuartpb/stubernetes-setup/master/steps/kubic/setup-systemd-networkd.sh)`

## a brief aside to wipe everything

Since there's still some Ceph artifacts in the system, I ensure `/var/lib/rook` is deleted on all nodes, then do this on the storage trio:

I do an `lsblk` to make sure the storage devices are /dev/sda and /dev/sdb on all nodes (they are, though which is which varies), then run this to wipe them:

```bash
sgdisk --zap-all /dev/sda
sgdisk --zap-all /dev/sdb
dd if=/dev/zero of=/dev/sda bs=1M count=100 oflag=direct,dsync
dd if=/dev/zero of=/dev/sdb bs=1M count=100 oflag=direct,dsync

ls /dev/mapper/ceph-* | xargs -I% -- dmsetup remove %
```

I use `dd` for wiping the metadata from both disks because `blkdiscard` doesn't appear to work through these USB enclosures

reminder that this is from https://github.com/rook/rook/blob/master/Documentation/ceph-teardown.md

I go ahead into the [2020-10-03 rebuild](cert1-2b4mb-82afh-8p82v-dgq6n)
