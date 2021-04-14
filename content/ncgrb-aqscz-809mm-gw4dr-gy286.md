# bringing up unusualstudio.net

picking https://dedipath.com/ssd-vps because they have a $3.50/mo package (on OpenVZ) that meets the minimum requirements listed at https://github.com/cdr/code-server/blob/v3.8.1/docs/guide.md#requirements exactly (1GB RAM, 2 cores), all of which is perfect for my needs

found via https://lowendbox.com/best-cheap-vps-hosting-updated-2020/

initializing with Ubuntu 20.04 because that's the newest listed and, while I'm no big fan of Ubuntu, I also figure, screw it, the point of this box is to have a working box I can do anything on, and Ubuntu's got a how-to for everybody, hooray for inertia

auto-generated a password for this, replaced with what looking at the generated password reminded me of

Started HTML5 serial console

`useradd -m -G sudo stuart` to make a sudoer account for me per https://askubuntu.com/questions/43317/what-is-the-difference-between-the-sudo-and-admin-group

`passwd stuart` to make sure I can enter passwords to sudo things

`cd /home/stuart; su stuart; mkdir .ssh`

echo public key on stuflon to authorized_keys

cancel out of HTML5 session

sshing in as stuart

doing `sudo -i`

`apt-get update`

`apt-get upgrade`

installing package maintainer's version of sshd_config (permits root login, as the one installed already appears to)
I'll end up disabling that later by disabling root's password anyway

```
--- /etc/systemd/resolved.conf  2021-02-10 21:35:40.123632311 +0000
+++ /etc/systemd/resolved.conf.dpkg-new 2021-01-06 20:47:39.000000000 +0000
@@ -19,7 +19,6 @@
 #MulticastDNS=no
 #DNSSEC=no
 #DNSOverTLS=no
-#Cache=yes
+#Cache=no-negative
 #DNSStubListener=yes
 #ReadEtcHosts=yes
-DNS=8.8.8.8 8.8.4.4
```

heck whatever, let's keep the existing one

`apt-get install nano curl`

`curl -fsSL https://code-server.dev/install.sh | sh`

`systemctl enable --now code-server@stuart`

`hostnamectl set-hostname unusualstudio.net` replaces default linux.e0a8ca.com

now reading https://caddyserver.com/docs/install#debian-ubuntu-raspbian

```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo apt-key add -
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee -a /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

nano to edit the Caddyfile accordingly to the code-server suggestion

`systemctl enable --now caddy.service`

looks like the machine shipped with apache2 enabled, so

`systemctl disable --now apache2`

edited the config file; used usermod to change my user shell to bash because I needed tab completion
