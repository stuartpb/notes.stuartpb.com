# rolling out systemd-resolved on stushiba

following [the earlier research on systemd-resolved](tsmr2-xe7sy-8s91x-5945y-64frj)

## undoing what's done

reviewing [2018-09-26 logs](2kaps-t7y41-yb8k2-yr6x0-vfpms)

looks like I might have to deal with the changes to `/etc/nsswitch.conf` [if I switch to systemd-networkd][1], but for now I should be fine

[1]: https://wiki.archlinux.org/index.php/Systemd-networkd#systemd-resolve_not_searching_the_local_domain

per [the recommendation when enabling mDNS](https://wiki.archlinux.org/index.php/Systemd-resolved#mDNS), I'm gonna go ahead and `sudo systemctl disable --now avahi-daemon`

```
Removed /etc/systemd/system/multi-user.target.wants/avahi-daemon.service.
Removed /etc/systemd/system/dbus-org.freedesktop.Avahi.service.
Removed /etc/systemd/system/sockets.target.wants/avahi-daemon.socket.
Warning: Stopping avahi-daemon.service, but it can still be activated by:
  avahi-daemon.socket
```

in light of that message, I also rerun as `sudo systemctl disable --now avahi-daemon.socket` for good measure

doing `yay -S systemd-resolvconf` to replace `openresolv` in case I have any programs that would use that

## switching to systemd-resolved

`sudo systemctl enable --now systemd-resolved.service` to get it running

`sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf` to switch

`resolvctl status`: everything looks good

## enabling mdns in NetworkManager

```bash
sudo bash -c 'echo "[connection]
connection.mdns=1" >/etc/NetworkManager/conf.d/10_resolve-mdns.conf'
```

I almost set this as `connection.mdns-resolve` but [it looks like that wouldn't work](https://gitlab.freedesktop.org/NetworkManager/NetworkManager/-/issues/301)

## testing

reloading this NetworkManager config with `sudo systemctl restart NetworkManager`

testing:

```
[stuart@stushiba ~]$ resolvectl query octopi.local
octopi.local: 2604:4080:1122:9580:3b4:f592:2d16:1260 -- link: enp4s0

-- Information acquired via protocol mDNS/IPv6 in 225.7ms.
-- Data is authenticated: no
```

bam

## setting up broadcast for my home network connection

`nmcli connection edit 'Wired connection 1'`

`nmcli> set connection.mdns 2`

typing `print` shows that it's set

running `save` and `quit`
