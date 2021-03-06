# 2020-10-03 rebuild

realized I didn't disable DNSSEC correctly due to a bum regex (`?` is one of those needs-to-be-escaped-to-work-right dumb sedisms). fixed

the old jam: run `kubeadm init`, copy out of `/etc/kubernetes/admin.conf`, run join command on all nodes.

due to the DNS misconfiguration, the NTP server names didn't resolve, so I had to reboot a couple nodes - probably could have figured out another way to fix this but fuck it, reboots are cheap when it's not-yet-configured

## node labeling

```
kubectl label nodes --all st8s.testtrack4.com/zone=403
kubectl label nodes stuby stuphire stumethyst st8s.testtrack4.com/storage-group=storberry
```

weird observation: even before I push the core chart, the only nodes that aren't ready are stumez and sturl, the ones I only recently flashed, even though I just transactional-updated the rest, right?

idk probably just a coincidence. anyway they go ready after Weave is running as well

## Prometheus CRDs

this time I want to install metal and the ingress before I do prometheus, etc

I open https://gitpod.io/#https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack/crds and run `awk 'FNR==1 && NR!=1 {print "---"}{print}' *.yaml > prometheus.yaml` to combine all CRDs into one bundle, which I copy-paste into a file in my stubernetes-system repo

I then `kubectl apply -f` both of the files in there

next I start pushing some releases:

```
[stuart@stushiba helmreleases]$ kubectl apply -f cert-manager.yaml
namespace/cert-manager created
helmrelease.helm.fluxcd.io/cert-manager created
[stuart@stushiba helmreleases]$ kubectl apply -f metallb.yaml
namespace/metallb-system created
helmrelease.helm.fluxcd.io/metallb created
[stuart@stushiba helmreleases]$ kubectl apply -f internal-ingress.yaml
namespace/internal-ingress created
helmrelease.helm.fluxcd.io/internal-contour created
[stuart@stushiba helmreleases]$ kubectl apply -f hornhorse-external-dns.yaml
namespace/hornhorse created
helmrelease.helm.fluxcd.io/hornhorse-external-dns created
```

it's at this point that I re-introduce my CloudFlare secret from my local `stubernetes-secrets` directory on stushiba. you can imagine what's in it: the same token, in two different namespaces (one for external dns and one for cert-manager)

I also remember to go into issuers and apply hornhorse-issuer.yaml

after a bit, I open a new tab to kubernetes-dashboard.horn.horse and smile: everything's live, just as planned.

that'll make debugging the next step less mind-erasing

## installing rook-ceph

just to be cool I'm going to wait until all the pies go green before I try rolling out the cluster

The rook-discover pod on stumethyst takes forever to start: I see a lot of errors like `Failed to create pod sandbox: rpc error: code = Unknown desc = failed to create pod network sandbox k8s_rook-discover-s8qdt_rook-ceph_2346b696-025f-4370-b068-91d74c6e8be1_0(d0d152ff2e6b8b62a1c91afefd92858186febbba39fd90aa8c6615eb2eecd42d): unable to set hairpin mode to true for bridge side of veth vethwepld0d152f: operation not supported` while waiting

watching... hmm, the mon on stunster keeps dying, and it looks like that's crashing the stuphire initialization pod?

it looks like keeping stunster in the cluster might actually be *hurting* reliability. boo

anyway, I'm gonna go to bed and see if this eventually fixes itself

## 2020-10-03 P3:30

booyah, all pies green across all namespaces

osd 5 still seems to be going into a crash loop regularly?

gah, fuck... I'm gonna do another teardown.

[new page](q498v-spyfd-cd87k-zh0xs-dzk96)
