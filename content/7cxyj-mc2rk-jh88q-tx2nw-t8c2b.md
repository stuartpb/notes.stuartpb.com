# 2020-08-10 Kluster Rollout: First steps coming back

Plugged my network switch back into the router. I'd unplugged it when we were encountering network disconnection issues, but I believe that was an issue on our ISP's end that has been resolved

## quick test before rebooting the system

setting `DHCLIENT_FQDN_ENABLED="enabled"` and `DHCLIENT6_FQDN_ENABLED="enabled"` in `/etc/sysconfig/network/dhcp` per [a grep](985hb-x1e5v-cwb2m-vrhr8-c2590)

not sure if I can get it working, because I set the system's hostname... fuck it, I'll mess with it in the next iteration

`curl -L https://download.opensuse.org/tumbleweed/iso/openSUSE-Kubic-DVD-x86_64-Current.iso | dd of=/dev/mmcblk0`
