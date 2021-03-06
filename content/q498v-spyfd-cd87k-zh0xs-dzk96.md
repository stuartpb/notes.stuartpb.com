# new revision

installing stuticia from the install media I had flashed for reinstalling stumez

gonna rebuild the cluster with stuticia joined and stunster... not joined. I've decided stunster is gonna be my guinea pig for local Ska Linux development, and actually just bare-metal experimentation in general, so these two machines can be the dedicated x64 nodes and there's no half-powered node jeopardizing overall cluster health like what I think happened with stunster and Ceph spinup

that frees up stunster's 128 GB MicroSD, now that I think of it - I can swap it for a 64GB for experimentation, and use the 128gb for an onboard Linux installation on sturoo

## stuticia setup

following from [the previous systemd-networkd rollout](g3fcm-gdc4s-r4a1e-0c4f9-w26xp)

after installing from media, I add my SSH keys from GitHub, run a `transactional-update` and reboot. I run the command to switch away from wicked after it's back:

`transactional-update run bash -x <(curl https://raw.githubusercontent.com/stuartpb/stubernetes-setup/master/steps/kubic/setup-systemd-networkd.sh)`

after rebooting, names resolve, so it looks like I've finally got this one right

## cluster teardown, again

kick things off with a good old `kubeadm reset`

I then run this on all the storage nodes (I'm sure these are the drives - checked to make sure nothing'd changed):

```bash
ls /dev/mapper/ceph-* | xargs -I% -- dmsetup remove %
sgdisk --zap-all /dev/sda
sgdisk --zap-all /dev/sdb
dd if=/dev/zero of=/dev/sda bs=1M count=100 oflag=direct,dsync
dd if=/dev/zero of=/dev/sdb bs=1M count=100 oflag=direct,dsync
```

then I run `rm -r /var/lib/rook` on every node (it's not there on the server, but it's on stumez)

I then do a reboot for good measure

## rapid rebuild

ohh, this is why the nodes were ready after I rebuilt: lingering Weave rules, because I don't bother clearing them since Weave remakes them anyway, and is the first thing I set up. stuticia is the only one that's not ready right after deploying.

I go ahead and helm install the core, then I roll out the same base services as [last time](cert1-2b4mb-82afh-8p82v-dgq6n)

got kind of tripped up for a bit because I forgot to push the hornhorse issuer

now I've got a bum certificate clogging up the works again...

`kubectl delete certificate kubernetes-dashboard-hornhorse-cert -n kubernetes-dashboard` like got me unstuck [last time](v4ctw-32v1c-wj8n5-0xkns-5v8rk)

still stuck. I try deleting the secret (of the same name) *and* the cert

hmm... looks like there aren't errors in the pod... but there's no route to host?

```
[stuart@stushiba stubernetes-system]$ kubectl get pods -n internal-ingress
NAME                                       READY   STATUS    RESTARTS   AGE
internal-contour-contour-766d9ccf9-6hh4r   1/1     Running   0          24m
internal-contour-contour-766d9ccf9-tq5qv   1/1     Running   0          24m
```

well, that doesn't look right. where are the envoys?

```
[stuart@stushiba stubernetes-system]$ kubectl get daemonsets -n internal-ingress
NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                                           AGE
internal-contour-envoy   0         0         0       0            0           kubernetes.io/arch=amd64,st8s.testtrack4.com/zone=403   26m
```

oh HERP DERP RIGHT. I forgot to run the labeling step:

```
kubectl label nodes --all st8s.testtrack4.com/zone=403
kubectl label nodes stuby stuphire stumethyst st8s.testtrack4.com/storage-group=storberry
```

okay, looks like everything's working now.

## deep breath

I deploy the rook-ceph release and wait for all the discover pods to come online

I push the cluster, and everything comes online okay.

I push the throwaway pool.... then I rethink it...

when I opened up the dashboard earlier, the system seems to have read the SSDs as hdds...

You know what? What the hell, let's just use the SSDs as the db devices for the HDDs by specifying size selectors in the drive group, and then I'll take the drive selectors off the storage classes. Path of least resistance

## d'oh

changing the cluster looks like it doesn't go well, and then I screw up pulling the cluster out for a smooth teardown. fuck it, rebuilding the cluster again, and this time keeping [a checklist of the steps](g8gn4-qg9nj-4995p-ck3eh-wz1d8)

## okay so

looks like rook-ceph deployed OK? but I still don't see prometheus coming online

deploying the ingress for the rook-ceph dashboard and `kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo` to get the password (per https://rook.io/docs/rook/v1.4/ceph-dashboard.html)

it looks like it's hung up on certs... whatever, I'm going to bed

## woke up

Grafana deployed, but, wouldn't you know it, nothing works, because the core part of Prometheus didn't deploy, because the PVs weren't created, because...

https://github.com/rook/rook/issues/6364

like right as I got a response in that issue, I decided my Helm Operator was irreparably stuck as well, and to hell with it, I'm just gonna try rebuilding the cluster again, see if that fixes this. I don't feel like it will

## first hitch

Ran into some trouble with the cert not appearing, which I traced back to [this bug](https://github.com/jetstack/cert-manager/issues/2741#issuecomment-625620741), stemming from what I discovered while reviewing [Let's encrypt's rate limits](https://letsencrypt.org/docs/rate-limits/). I'm just gonna go ahead and switch back over to the staging server for all the ingresses until I can successfully deploy an app without having to tear down my entire cluster

makes me think about wanting [Git Standing Patch Ignore](2v96d-2q8g1-vm9b9-dhp53-s13ma), which also intersects with the [kublam.moe](nh005-8sp5r-0fay4-1yt9r-fqegw) idea

## oh shit

so I redid Rook, and while I was waiting for that to repro, I did `kubectl version` (because yeah, what else could it be but weirdness there), and [yeah](https://bugzilla.opensuse.org/show_bug.cgi?id=1177289)

I also did `kubeadm config images pull --image-repository=k8s.gcr.io` on the worker nodes but I don't think that really mattered

anyway, this was enough to turn me off of ever using the kubic registry at any opportunity ever again

need to figure out how to make this a consistent kubic-setup-step (even after this bug is fixed, since they've now proven their packaging can't be trusted to preserve basic functionality)

## anyway

went through a few more rounds and have ended up in a spot where it looks like there's a PVC that's lingering and keeping Prometheus from starting? but Rook went OK?

okay, you know what, here's what I'm gonna do, rather than tear the whole cluster down and try again: I'm gonna delete the Prometheus chart, watch everything disappear, ensure the PVC is gone, delete it if not, and *then* apply the release again

had to manually remove the podsecuritypolicy docs, maybe because of CRDs?

## anyway

had to do a little more jiggling over another major version mismatch

there's still a failed job for the admission-create webhook job, but from what I can tell of [what it does](https://github.com/helm/charts/pull/14543/files), that shouldn't matter until we start trying to write our own PrometheusRules which I don't expect to do until the next time I push a Helm revision, which should jiggle that job anyway

anyway, Grafana's working now, woop woop

... fuck it, Helm Operator constantly recreates the thing anyway, let's kill the job and watch it get recreated

... oh fuck it's Failed, maybe because of the job problem. Fuck it let's do the old delete-and-recreate

