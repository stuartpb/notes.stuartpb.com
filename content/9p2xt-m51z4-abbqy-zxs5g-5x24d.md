# Trying to get OpenEBS Working

Continuing from [The Return of Prometheus](adfw5-f3v1p-wraf8-zkarh-k9vwq) and [the issue it spawned](https://github.com/openebs/openebs/issues/3064)

Consolidating the 4 storage definitions `awk 'BEGINFILE {print "---"}{print}' openebs-* > openebs-storage.yaml`

also adding namespace metadata to the `StoragePoolClaim`s

okay, so I tried deleting the storage docs, but it's stuck deleting cstor-bulk-pool

what if I delete the prometheus pod? Will that open the window to delete the storageclass?

okay, I deleted the object and re-added it

[oh my god](https://github.com/openebs/openebs/issues/3064#issuecomment-644500493)

making name changes and doing the upgrade

okay, you know what, changing the name of everything in an upgrade is a bad idea

okay, this is a clusterfuck. Deleting prometheus namespace and recreating then reinstalling the chart with the new values

## what now

> Release "prometheus-operator" failed and has been uninstalled: failed to create resource: Internal error occurred: failed calling webhook "prometheusrulemutate.monitoring.coreos.com": Post https://prometheus-operator-operator.prometheus.svc:443/admission-prometheusrules/mutate?timeout=30s: service "prometheus-operator-operator" not found

per https://github.com/helm/charts/issues/19928#issuecomment-596961503 and replies:

```
kubectl delete validatingwebhookconfigurations.admissionregistration.k8s.io prometheus-operator-admission
kubectl delete MutatingWebhookConfiguration prometheus-operator-admission
```

trying to install it now fails with 502 Bad Gateway errors - looking at the Namespace some more, there are still a few resources lingering, like a PVC, that don't seem to have been collected. Deleting them. Also looking at cluster-wide resources

fuck it, I'm keeping the values YAML on disk anyway, why bother trying to do this through kubeapps?

okay, now the only thing here is a "default-token" Secret, which I guess is intrinsic to each namespace

```
[stuart@stushiba stubernetes-setup]$ helm install -n prometheus prometheus-operator stable/prometheus-operator -f values/prometheus-operator.yaml
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
manifest_sorter.go:192: info: skipping unknown hook: "crd-install"
NAME: prometheus-operator
LAST DEPLOYED: Tue Jun 16 01:20:04 2020
NAMESPACE: prometheus
STATUS: deployed
REVISION: 1
NOTES:
The Prometheus Operator has been installed. Check its status by running:
  kubectl --namespace prometheus get pods -l "release=prometheus-operator"

Visit https://github.com/coreos/prometheus-operator for instructions on how
to create & configure Alertmanager and Prometheus instances using the Operator.
```

And the pod *still* won't come online

`transactional-update pkg install e2fsprogs`

## back

Okay, it looks like it's up... but the container for my API server is missing?

`journalctl -bu kubelet`

Hmm... flipping through, it looks like a freaking meteor hit my logs at 2020-06-16T09:30:00Z

Uploading the forensics via `journalctl -b -u kubelet --until="2020-06-16 09:31:00" | curl -F 'f:1=<-' ix.io` - you don't need the spew of errors after 9:30, just a little bit of carnage to convey how bad it is

## a little diversion

Hmm. No output. Looks like the upload failed.

```
studtop:~ # journalctl -b -u kubelet --until="2020-06-16 09:31:00" | curl -F 'file=<-' https://0x0.st/
Segmentation fault
```

Hmm.

```
studtop:~ # journalctl -b -u kubelet --until="2020-06-16 09:31:00" | wc -c
2373502
```

So yeah, GNOME terminal helpfully tells me that's about 20 MiB. Too big for GridFS, but hardly what I'd think of as a curl-crasher. Weird.

```
studtop:~ # mktemp
/tmp/tmp.F2hEUkDb7q
studtop:~ # journalctl -b -u kubelet --until="2020-06-16 09:31:00" > /tmp/tmp.F2hEUkDb7q
studtop:~ # less /tmp/tmp.F2hEUkDb7q
studtop:~ # curl -F 'f:1=@/tmp/tmp.F2hEUkDb7q' ix.io
studtop:~ # curl -F 'file=@/tmp/tmp.F2hEUkDb7q' https://0x0.st/
https://0x0.st/iWfE.F2hEUkD
```

finally

Wow, this server was started about 20 minutes before the incident, and the error logs that are cut at 1 minute after are 30% that last minute

Okay, I can review that later if it becomes relevant, until then... I'm gonna restart kubelet and see if that fixes this?

`systemctl restart kubelet`

nope, it died again.

[the logs of the failed pod](https://gist.github.com/stuartpb/4d4a0a0351ef4a538d4221bd156de88f) don't seem to tell me much

## screw it

dimension's fucked, Morty get in the spaceship we're out

```
curl -L https://download.opensuse.org/tumbleweed/iso/openSUSE-Kubic-DVD-x86_64-Current.iso | dd of=/dev/mmcblk0
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   374  100   374    0     0    544      0 --:--:-- --:--:-- --:--:--   543
100 1514M  100 1514M    0     0  7991k      0  0:03:13  0:03:13 --:--:-- 7098k
3100672+0 records in
3100672+0 records out
1587544064 bytes (1.6 GB, 1.5 GiB) copied, 207.206 s, 7.7 MB/s
```

now I'm just gonna reboot, open the lid, install this image over the current system (once again, there's nothing precious to lose, all work is on stushiba or these notes), and then see where this goes.

also, since I'm on the verge of a real 2-node system, I might change the name of this to "studlid", if I choose to go that route

## thing I'm thinking of doing differently next time

- not setting a hostname
  - if the installer calls it "studtop", I'll change name to "studlid", but otherwise I'm gonna go ahead
