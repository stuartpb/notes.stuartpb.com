# got this DNS all set up, let's see how she handles

```
[stuart@stushiba resources]$ dig google.com @192.168.42.53

; <<>> DiG 9.16.3 <<>> google.com @192.168.42.53
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 26826
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; PAD: (399 bytes)
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		30	IN	A	216.58.193.78

;; Query time: 449 msec
;; SERVER: 192.168.42.53#53(192.168.42.53)
;; WHEN: Thu May 28 16:24:29 PDT 2020
;; MSG SIZE  rcvd: 468
```

versus

```
[stuart@stushiba resources]$ dig google.com

; <<>> DiG 9.16.3 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 38440
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 4, ADDITIONAL: 9

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		86	IN	A	172.217.3.174

;; AUTHORITY SECTION:
google.com.		38758	IN	NS	ns2.google.com.
google.com.		38758	IN	NS	ns4.google.com.
google.com.		38758	IN	NS	ns1.google.com.
google.com.		38758	IN	NS	ns3.google.com.

;; ADDITIONAL SECTION:
ns4.google.com.		290504	IN	A	216.239.38.10
ns4.google.com.		238697	IN	AAAA	2001:4860:4802:38::a
ns1.google.com.		258316	IN	A	216.239.32.10
ns1.google.com.		262029	IN	AAAA	2001:4860:4802:32::a
ns3.google.com.		290504	IN	A	216.239.36.10
ns3.google.com.		57280	IN	AAAA	2001:4860:4802:36::a
ns2.google.com.		290137	IN	A	216.239.34.10
ns2.google.com.		343450	IN	AAAA	2001:4860:4802:34::a

;; Query time: 0 msec
;; SERVER: 192.168.0.1#53(192.168.0.1)
;; WHEN: Thu May 28 16:24:40 PDT 2020
;; MSG SIZE  rcvd: 303
```

:thinking_face:

oh wait

```
[stuart@stushiba resources]$ dig google.com @1.1.1.1

; <<>> DiG 9.16.3 <<>> google.com @1.1.1.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17557
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1452
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		182	IN	A	216.58.193.78

;; Query time: 3 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu May 28 16:28:53 PDT 2020
;; MSG SIZE  rcvd: 65
```

ah.

okay, so, like, I didn't realize the router already implemented its own DNS proxy?

```
# dhcpcd -T
dhcpcd-9.0.2 starting
DUID 00:01:00:01:26:63:08:fc:fc:aa:14:c9:b7:b0
enp4s0: IAID 14:c9:b7:b0
dhcp6_openudp: Address already in use
ps_inet_listenin6: Address already in use
ps_root_recvmsg: Address already in use
enp4s0: soliciting a DHCP lease
enp4s0: offered 192.168.0.101 from 192.168.0.1
interface=enp4s0
pid=204117
protocol=dhcp
reason=TEST
ifcarrier=up
ifflags=4163
ifmtu=1500
ifwireless=0
new_broadcast_address=192.168.255.255
new_dhcp_lease_time=4294967295
new_dhcp_message_type=2
new_dhcp_server_identifier=192.168.0.1
new_domain_name_servers='192.168.0.1 192.168.0.1' <<< check 'em
new_ip_address=192.168.0.101
new_network_number=192.168.0.0
new_routers=192.168.0.1
new_subnet_cidr=16
new_subnet_mask=255.255.0.0
ps_dostop: Connection refused
dhcpcd exited
```

wild

logging into the admin console and seeing what my ISP provides, this does match:

```
[stuart@stushiba resources]$ dig google.com @208.76.152.1

; <<>> DiG 9.16.3 <<>> google.com @208.76.152.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54821
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 4, ADDITIONAL: 9

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		222	IN	A	172.217.3.174

;; AUTHORITY SECTION:
google.com.		38294	IN	NS	ns1.google.com.
google.com.		38294	IN	NS	ns2.google.com.
google.com.		38294	IN	NS	ns4.google.com.
google.com.		38294	IN	NS	ns3.google.com.

;; ADDITIONAL SECTION:
ns4.google.com.		290040	IN	A	216.239.38.10
ns4.google.com.		238233	IN	AAAA	2001:4860:4802:38::a
ns1.google.com.		257852	IN	A	216.239.32.10
ns1.google.com.		261565	IN	AAAA	2001:4860:4802:32::a
ns3.google.com.		290040	IN	A	216.239.36.10
ns3.google.com.		56816	IN	AAAA	2001:4860:4802:36::a
ns2.google.com.		289673	IN	A	216.239.34.10
ns2.google.com.		342986	IN	AAAA	2001:4860:4802:34::a

;; Query time: 0 msec
;; SERVER: 208.76.152.1#53(208.76.152.1)
;; WHEN: Thu May 28 16:32:24 PDT 2020
;; MSG SIZE  rcvd: 303
```

Also, like... I'm wondering if I should maybe change my caching rules to be longer than 30 seconds? or maybe the issue is just that the DNS server not getting used enough means it has to do the TLS handshake

## preparing to change the nameserver

(not going to do it until I've got at least one internal domain I can test)

Huh looking at my dashboard, the DHCP pool appear to be configured as...192.168.0.2 to 192.168.255.254?! and it's distributing random addresses throughout that range!!

that is [not what it's supposed to be](2ehz6-qdw71-178bs-sgst1-n5req)!!

fixed. luckily it doesn't show it as having any active leases in 192.168.42.0/24 or any of the other numbering-plan-reserved spaces, so we can just let this issue sort itself out

how did this even happen, though? did an update do this?

anyway. hmm... there's a space to configure DNS resolution under "Internet" (where there's a radio to switch between "use the ones advertised by DHCP" and "use ones I set manually"), and a space to configure it under "DHCP" (where it's a blank field that's listed as "optional").

if I had to guess, I'd bet that setting it under "Internet" means the router is going to continue functioning as a DNS proxy (ie. advertising 192.168.0.1 as the DNS server), but using the configured IP. And, honestly? I think I'm gonna go that way: this way, if I break the laptop, I can just toggle the radio on the router and everyone's DNS will go back to normal without having to refresh their DHCP config

## testing an internal name

goint to the Kubernetes Dashboard for Kubeapps

(side note: wow, Kubeapps uses Mongo and, now that I look a little more closely, it is a *fuckboi* with my ram)

anyway, editing the Service to change the type to LoadBalancer and add two Annotations:

```yaml
  annotations:
    external-dns.alpha.kubernetes.io/hostname: kubeapps.internal
    hhk8s.stuartpb.com/internal-name: coredns
```

oh hurr durr, I never changed the ConfigMap in the metallb configuration somehow? So it was autoassigned 192.168.32.0

Went back and changed that...

did I install this with kubeapps?

okay what the FUCK, it was REVERTED

oh FUCK ME, it's reassigning from that fucking "manifests" folder isn't it. That's what all this "rio.cattle.io" shit is

## time to undo yet another mistaken trust in k3s

saving the `metallb.yaml` and `metallb-config.yaml` manifests from the server to my local "resources" folder (correcting the IP range in the config one)

running `sudo rm /var/lib/rancher/k3s/server/manifests/metal*.yaml`

okay, the stuff is still here... I almost want to try deleting the whole thing and remaking it from the files, just to ensure the grubby little rio annotations aren't anywhere near it any more?

let's try this:

```
kubectl annotate -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/id-
kubectl annotate -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/applied-
kubectl annotate -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/owner-gvk-
kubectl annotate -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/owner-name-
kubectl annotate -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/owner-namespace-
kubectl label -f metallb.yaml -f metallb-config.yaml objectset.rio.cattle.io/hash-
```

okay looks like that did it

jiggling the kubeapps service type to `NodePort` and back to `LoadBalancer` to get it to reallocate an IP in the new block

derp, didn't work. `kubectl -n metallb-system rollout restart deployment/controller` to reload the changed configmap

and now, reloading, it's assigned it `192.168.42.0`... hmm. I guess I've been thinking "oh, if I pick a manual IP address, it'll need to be in the range I've allocated in metallb", but now, like... I don't even think it's going to be careful with its allocations?

do I not still have the internal ingress running? that was `192.168.42.0` before (which is why I thought, y'know, the config change was working)

oh right, I manually assigned it `192.168.42.80`

Okay, time to rework the numbering plan... I think I'll still have just one /24(ish) allocated to dynamic provisioning, but it'll be 192.168.32.0-192.168.32.250, with potential room to expand up to 42 if need be

ohhhhh, maybe that's what fucked my DHCP range: changing the subnet mask must have automatically expanded it to fit (hence why it ranged at the /16 level). grr

good to know: if you change the subnet mask, make sure you revise the DHCP range after

okay, I'm renaming this pool `household-internal-dynamic` (to contrast with the internal-static range on 42 and a potential exposed-dynamic, which might go on 63?), and changing it to 192.168.32.0-192.168.32.250

(I'm reserving the top 5 addresses of each /24 in case I ever need to provision some kind of each-range authority. also, you know, potential broadcast addresses in a different network topology)

deleting the extra metallb ReplicaSets this restarting has created

hmm, switching back and forth on NodePort vs LoadBalancer isn't reclaiming the IP (maybe because it's not "in the pool" any more?)

What if I set a manual IP that's in that range, delete it, *then* shake the service type?

setting the `loadBalancerIP` to 192.168.42.69 isn't shaking the allocation of 192.168.42.0

oh, herp derp, I didn't change the type back to `LoadBalancer`

okay, so... I set the annotation for the address pool... still nothing

ohohohohoh, this is because I have an Ingress, and *that's* on 42.0, isn't it

...no, there's no ingress...

...but maybe there should be? Would that rekajigger this?

ok, so, like, let me just delete the NodePort and see if that lets me reset this to a ClusterIP

the ingress loadbalancerip is still there (and still resolving). at this point I'm debating just deleting kubeapps altogether and maybe replacing it with nothing, this MongoDB shit is just disgusting

```
[stuart@stushiba resources]$ helm uninstall --dry-run --debug -n kubeapps kubeapps
release "kubeapps" uninstalled
```

uh... okay you know what, fuck it, I'm doing this. a full delete and reinstall

creating this as "kubeapps.yaml" in a new "values" folder for stubernetes-setup:

```yaml
useHelm3: true
frontend:
  service:
    type: LoadBalancer
    annotations:
      external-dns.alpha.kubernetes.io/hostname: kubeapps.internal
      hhk8s.stuartpb.com/internal-name: coredns
mongodb:
  enabled: false
postgresql:
  enabled: true
```

oh nice, reviewing the modern values it looks like they use postgres instead of mongo now. I mean, I could have done a proper upgrade, but this works too (EDIT: oh, hey, the readme says that's not supported, so yeah we'd have had to do this anyway)

https://github.com/kubeapps/kubeapps/blob/master/docs/user/using-an-OIDC-provider.md is real neat but I'll need to figure it out later

```
[stuart@stushiba values]$ helm install -f kubeapps.yaml kubeapps -n kubeapps bitnami/kubeapps
```

uhh, looking at it I'm seeing both Tiller *and* Mongo? WTF?

running `helm install -f kubeapps.yaml kubeapps -n kubeapps bitnami/kubeapps --dry-run --debug`, and, yeah, it's just, like... completely ignoring the `-f kubeapps.yaml`???

oh herp de derp I didn't save the file

oh, and the change to upgrade to postgres was [this month](https://github.com/kubeapps/kubeapps/pull/1730), it's not worked into the chart yet. Looks like I'll have to switch it manually, so I'll go ahead and add that above

the `kubeapps` service is *still* not getting a LoadBalancerIP. Fuck it, we're not depending on the server for DNS yet: `sudo reboot`

this is an *amazing* argument in favor of letting the router handle DNS: we can set the ISP DNS (or 1.1.1.1) as a fallback when it's restarting

## still not working

fuck it. deleting the `metallb-system` namespace, deleting the `kubeapps` namespace, and then we'll try applying them back

the kubeapps server is STILL not getting an IP. Last ditch effort - pasting this in from the metallb install instructions:

```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.3/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

NOW it works. Fucking finally

## uhh

but now I can't find the DNS server I set up earlier...

oh, right, also - apparently, you *do* need to make the pool you use available to metallb, so I added the pool for static addresses with `auto-assign: false`, re-applied the configmap, and deleted the pods so they'd reload the configmap

reloading the dashboard, and it starts in the default namespace - oh *son of a bitch*, this is where the nameserver went. Didn't I specify a namespace when doing the Helm install?

anyway, doing `kubectl delete -f resources/household-dns.yaml` and `kubectl apply -f resources/household-dns.yaml`

Agh, they're still not coming back. Fuck it, deleting the metallb-system namespace and trying again

And they're still not coming back. Fuck it - deleting everything, then rebooting the node and trying to reinstall.

I'm also ratcheting the household-ingress back to a ClusterIP Service - I'll add the LoadBalancer stuff back once MetalLB is back.

ugh, still not working. Okay, I'm manually setting the DNS services to ClusterIP, then re-applying the config

## Okay, fuck's sake

let's take a look at the logs

If I delete the controller, here are the logs for the new one:

```
{"branch":"HEAD","caller":"main.go:142","commit":"v0.9.3","msg":"MetalLB controller starting version 0.9.3 (commit v0.9.3, branch HEAD)","ts":"2020-05-29T07:05:41.967948051Z","version":"0.9.3"}
{"caller":"k8s.go:387","configmap":"metallb-system/config","error":"could not parse config: yaml: line 2: did not find expected '-' indicator","event":"configStale","msg":"config (re)load failed, config marked stale","ts":"2020-05-29T07:05:42.474694772Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/longhorn-frontend","ts":"2020-05-29T07:05:42.474901602Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/longhorn-frontend","ts":"2020-05-29T07:05:42.475014291Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/longhorn-frontend","ts":"2020-05-29T07:05:42.475108253Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/csi-provisioner","ts":"2020-05-29T07:05:42.475255636Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/csi-provisioner","ts":"2020-05-29T07:05:42.475376332Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/csi-provisioner","ts":"2020-05-29T07:05:42.475483115Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/csi-resizer","ts":"2020-05-29T07:05:42.475609442Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/csi-resizer","ts":"2020-05-29T07:05:42.475707929Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/csi-resizer","ts":"2020-05-29T07:05:42.475804015Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/compatible-csi-attacher","ts":"2020-05-29T07:05:42.475909237Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/compatible-csi-attacher","ts":"2020-05-29T07:05:42.476013343Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/compatible-csi-attacher","ts":"2020-05-29T07:05:42.476072851Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-internal-assetsvc","ts":"2020-05-29T07:05:42.476136584Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-internal-assetsvc","ts":"2020-05-29T07:05:42.476185696Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-internal-assetsvc","ts":"2020-05-29T07:05:42.476226488Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-dns-coredns-udp","ts":"2020-05-29T07:05:42.476272155Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-dns-coredns-udp","ts":"2020-05-29T07:05:42.476310498Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-dns-coredns-udp","ts":"2020-05-29T07:05:42.476356788Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-dns-coredns-tcp","ts":"2020-05-29T07:05:42.476397209Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-dns-coredns-tcp","ts":"2020-05-29T07:05:42.476436296Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-dns-coredns-tcp","ts":"2020-05-29T07:05:42.476484712Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubernetes-dashboard/dashboard-metrics-scraper","ts":"2020-05-29T07:05:42.476548841Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubernetes-dashboard/dashboard-metrics-scraper","ts":"2020-05-29T07:05:42.476588349Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubernetes-dashboard/dashboard-metrics-scraper","ts":"2020-05-29T07:05:42.476628973Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kube-system/kube-dns","ts":"2020-05-29T07:05:42.476668469Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kube-system/kube-dns","ts":"2020-05-29T07:05:42.476706116Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kube-system/kube-dns","ts":"2020-05-29T07:05:42.476762023Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-skydns-etcd-headless","ts":"2020-05-29T07:05:42.476812611Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-skydns-etcd-headless","ts":"2020-05-29T07:05:42.47685045Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-skydns-etcd-headless","ts":"2020-05-29T07:05:42.476884832Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-dns-controller-external-dns","ts":"2020-05-29T07:05:42.476944003Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-dns-controller-external-dns","ts":"2020-05-29T07:05:42.476981915Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-dns-controller-external-dns","ts":"2020-05-29T07:05:42.477015552Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-postgresql","ts":"2020-05-29T07:05:42.47706105Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-postgresql","ts":"2020-05-29T07:05:42.477105792Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-postgresql","ts":"2020-05-29T07:05:42.477145816Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-internal-dashboard","ts":"2020-05-29T07:05:42.4771856Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-internal-dashboard","ts":"2020-05-29T07:05:42.477234351Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-internal-dashboard","ts":"2020-05-29T07:05:42.477274183Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/longhorn-backend","ts":"2020-05-29T07:05:42.477314303Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/longhorn-backend","ts":"2020-05-29T07:05:42.477372119Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/longhorn-backend","ts":"2020-05-29T07:05:42.47740668Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kube-system/metrics-server","ts":"2020-05-29T07:05:42.477451062Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kube-system/metrics-server","ts":"2020-05-29T07:05:42.477489189Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kube-system/metrics-server","ts":"2020-05-29T07:05:42.477522875Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"longhorn-system/csi-attacher","ts":"2020-05-29T07:05:42.477567113Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"longhorn-system/csi-attacher","ts":"2020-05-29T07:05:42.47760584Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"longhorn-system/csi-attacher","ts":"2020-05-29T07:05:42.477645Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-postgresql-read","ts":"2020-05-29T07:05:42.477689381Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-postgresql-read","ts":"2020-05-29T07:05:42.477721338Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-postgresql-read","ts":"2020-05-29T07:05:42.477772239Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-internal-kubeops","ts":"2020-05-29T07:05:42.477838961Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-internal-kubeops","ts":"2020-05-29T07:05:42.566706104Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-internal-kubeops","ts":"2020-05-29T07:05:42.567191854Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"default/kubernetes","ts":"2020-05-29T07:05:42.567544651Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"default/kubernetes","ts":"2020-05-29T07:05:42.567766152Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"default/kubernetes","ts":"2020-05-29T07:05:42.568016176Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-skydns-etcd","ts":"2020-05-29T07:05:42.56834171Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-skydns-etcd","ts":"2020-05-29T07:05:42.568557568Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-skydns-etcd","ts":"2020-05-29T07:05:42.568776536Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps-postgresql-headless","ts":"2020-05-29T07:05:42.568980053Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps-postgresql-headless","ts":"2020-05-29T07:05:42.569174459Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps-postgresql-headless","ts":"2020-05-29T07:05:42.569384639Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubeapps/kubeapps","ts":"2020-05-29T07:05:42.569593919Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubeapps/kubeapps","ts":"2020-05-29T07:05:42.569793031Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubeapps/kubeapps","ts":"2020-05-29T07:05:42.570295708Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"household-system/household-ingress-traefik","ts":"2020-05-29T07:05:42.570617868Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"household-system/household-ingress-traefik","ts":"2020-05-29T07:05:42.570900005Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"household-system/household-ingress-traefik","ts":"2020-05-29T07:05:42.570959513Z"}
{"caller":"main.go:49","event":"startUpdate","msg":"start of service update","service":"kubernetes-dashboard/kubernetes-dashboard","ts":"2020-05-29T07:05:42.571000522Z"}
{"caller":"main.go:62","event":"noConfig","msg":"not processing, still waiting for config","service":"kubernetes-dashboard/kubernetes-dashboard","ts":"2020-05-29T07:05:42.57127237Z"}
{"caller":"main.go:63","event":"endUpdate","msg":"end of service update","service":"kubernetes-dashboard/kubernetes-dashboard","ts":"2020-05-29T07:05:42.571329213Z"}
{"caller":"main.go:126","event":"stateSynced","msg":"controller synced, can allocate IPs now","ts":"2020-05-29T07:05:42.571615828Z"}
```

## AYFKM

... wait a fucking second ...

FUCKING GEDIT. My ConfigMap YAML looked like this:

```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
     - name: household-internal-static
      protocol: layer2
      addresses:
      - 192.168.42.0 - 192.168.42.250
      auto-assign: false
    - name: household-internal-dynamic
      protocol: layer2
      addresses:
      - 192.168.32.0 - 192.168.32.250
```

When it should have looked like this:

```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: household-internal-static
      protocol: layer2
      addresses:
      - 192.168.42.0 - 192.168.42.250
      auto-assign: false
    - name: household-internal-dynamic
      protocol: layer2
      addresses:
      - 192.168.32.0 - 192.168.32.250
```

Notice the difference? It's **one fucking space character too many** in the first address pool. MetalLB does not clearly raise an issue when this happens.

oh wait it did, that's literally the second line of the logs, fucking lol

still though, it didn't do [the thing it should have done here, which is die](https://github.com/metallb/metallb/issues/608)

## WELL FAN DAMN TASTIC THEN

Now we're back to where we were this afternoon: the service is all set up and resolving external DNS (but not internal)

What's the fucking lesson here? **READ YOUR FUCKING POD LOGS**

## On that note

OK, so a dig on kubeapps.internal is returning 0 results. So... let's look at the external-dns controller!

It's full of messages like these:

```
{"level":"warn","ts":"2020-05-29T07:17:20.623Z","caller":"clientv3/retry_interceptor.go:61","msg":"retrying of unary invoker failed","target":"endpoint://client-90a5b2b6-c46e-4890-9dd1-062cc8f91cdb/household-skydns-etcd.household-system.svc.cluster.local:2379","attempt":0,"error":"rpc error: code = InvalidArgument desc = etcdserver: user name is empty"}
time="2020-05-29T07:17:20Z" level=error msg="etcdserver: user name is empty"
```

...huh. Looking at https://github.com/bitnami/charts/issues/1454

So, like... yeah, it looks like this is coming from having rbac enabled, even though we have...

[oh haha rbac overrides the value of allowNoneAuthentication](https://github.com/bitnami/charts/blob/master/bitnami/etcd/templates/statefulset.yaml#L137)

Or, like, wait what? That ternary makes it so that rbac overrides an allowNoneAuthentication of *false*.

[What Bitnami bullshit *is* this?](https://www.google.com/search?q="ALLOW_NONE_AUTHENTICATION")

Man, good thing their bitnami-bot creates a thousand bullshit commits that adjust the readme so I can't find the commit that removed the "security" heading this chart appears to reference in the readme of https://github.com/bitnami/bitnami-docker-etcd !!

[even blame explains nothing](https://github.com/bitnami/bitnami-docker-etcd/blame/master/README.md)

ugh. anyway, apparently it means [allowing the system to not create a root user](https://github.com/bitnami/bitnami-docker-etcd/blob/master/3/debian-10/rootfs/entrypoint.sh#L33)

and enabling rbac [always creates that root password anyway](https://github.com/bitnami/charts/blob/master/bitnami/etcd/templates/statefulset.yaml#L139)

and Bitnami considers "having a root password" to mean ["enable authentication"](https://github.com/bitnami/bitnami-docker-etcd/blob/328c900aa253bdaae5a6f3120bf62f9d0129ca41/3/debian-10/rootfs/entrypoint.sh#L34), which, of course, [(from what I can make of the docs)](https://etcd.io/docs/v3.4.0/op-guide/authentication/) means "don't allow none authentication" :upside_down_face:

anyway, ugh, fine, whatever, all these "don't do this in a production environment" warnings... I guess I'll go ahead and learn about Secrets and shit tomorrow so I can

or, like, wait no what, [etcd didn't have authentication at all until 2.1??](https://etcd.io/docs/v2/authentication/)

yeah you know what, yeah fuck it I'm just gonna disable rbac and see if I can make this work tonight before I go to bed. maybe I'll fuck with securing the private line between the two later

## it remains to be said, though

having a separate variable for "allow none authentication" (which is, by the way, *the stupidest non-phrase I've ever heard*) that just means "don't fail if I set this other variable to false" is stupid, and the way that the chart *always enables it in a situation where it would be meaningless* is just... a *terrible* sign of several people not knowing what they were doing. the fact that Bitnami appears to export their Docker builds in a GitHub repository format that erases all contributor identities is just another horrifying glimpse into what might be terribly wrong with this stack

## anyway

after running an upgrade to a version of this chart with `auth.rebac.enabled` set to `false`:

```
[stuart@stushiba stubernetes-setup]$ dig kubeapps.internal @192.168.42.53

; <<>> DiG 9.16.3 <<>> kubeapps.internal @192.168.42.53
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8205
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 977190c2ad5d25af (echoed)
;; QUESTION SECTION:
;kubeapps.internal.		IN	A

;; ANSWER SECTION:
kubeapps.internal.	30	IN	A	192.168.32.0

;; Query time: 66 msec
;; SERVER: 192.168.42.53#53(192.168.42.53)
;; WHEN: Fri May 29 01:32:59 PDT 2020
;; MSG SIZE  rcvd: 91
```

:sunglasses:

## trying to set this up in the router...

setting the first DNS server local in the Internet settings is rejected with "DNS server IP address and LAN IP address cannot be in the same subnet." boo

searching this message turns up some results about DNS rebinding protection? which is, yeah, what I figured, and it sounds like consumer firmware most likely doesn't have a way to disable this

anyway, I'm resolving this by just setting 192.168.42.53 as the primary in the DHCP settings (with 1.1.1.1 as the secondary). Means I can't use the router as a stable proxy, but oh well

Oh wait, actually, I might as well use 192.168.0.1 as the secondary DHCP recommendation? since I'm not changing the Internet-settings side of things, that brings us back to the "use ISP DNS as a fallback" configuration that I (sort of) described wanting before! neat!

anyway, this is a perfect time to go to bed, and let all the devices on the network refresh their DHCP leases over the next two hours, so that everything will be configured to recognize internal names in the morning
