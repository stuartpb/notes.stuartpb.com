# The Return of Prometheus

Following [the path of the original installation](8qp0x-e05wx-tdav3-hhaf1-3pkcf) and

creating a prometheus namespace

Even though Bitnami's chart has a newer release attached, I'm still going to go with the Stable chart

## this was bad, ignore it

creating a Metrics service for household-dns as described in [the household-dns monitoring upgrade round 1 never got](zntt9-433t9-rpa7r-x6sb8-66kbx) as well as this towards the end of my YAML

```yaml
  additionalServiceMonitors:
  - name: household-dns
    selector:
      matchLabels:
        app.kubernetes.io/instance: household-dns
    namespaceSelector:
      matchNames:
      - household-system
    endpoints:
    - port: http-metrics
      bearerTokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
```

(I realize now that I should just deploy anything I want monitored with a ServiceMonitor resource, hence why most Helm charts have it: see "d'oh" below)

## adding persistence

uncommenting this:

```yaml
    storageSpec:
      volumeClaimTemplate:
        spec:
          storageClassName: openebs-bulk
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 50Gi
```

considered adding the metadata for Ingress, but considering that I'm probably going to bring in auth next, with its own ingress... I'm okay holding off for now

calling this installation "promop" to avoid the "smurf effect" from calling it "prometheus-operator" where those words get prefixed and suffixed over and over

## d'oh

the version from the previously-un-rolled-out patch is missing the `matchLabels` level

luckily, I copied these values over to a local text editor buffer anyway because CodeMirror doesn't let you ctrl+f

after at least two failed syntactic installations:

```
Release "promop" failed and has been uninstalled: failed to install CRD crds/crd-prometheusrules.yaml: etcdserver: request timed out
```

You know, I'm starting to think "why not just insert the ServiceMonitor after the thing's been installed?" And that's what I'm gonna do

in fact, really I should upgrade it with `prometheus.monitor.enabled = true`

## in fact

let's back out the entire "hacked manifests" installation and upload our hacked chart release a different way.

I'm going ahead and deleting the old resources (I accidentally installed them to the wrong namespace anyway - there really ought to be a "do not install to the default namespace" guard)

and then I go into the Service template in my local checkout of all stable charts and just paste in the finished compiled template I want it to look like, fuck it.

I go ahead nd make sure we're all git pulled and then, from my checkout's `stable/coredns` as the CWD: `helm install household-dns . -n household-system`

I realize I forgot to true the metrics scraping, so I upgrade it from the CLI (because Kubeapps doesn't seem to know the config), and here's the output:

```
[stuart@stushiba coredns]$ helm upgrade household-dns . -n household-system
Release "household-dns" has been upgraded. Happy Helming!
NAME: household-dns
LAST DEPLOYED: Mon Jun 15 06:52:46 2020
NAMESPACE: household-system
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
CoreDNS is now running in the cluster.
It can be accessed using the below endpoint
  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        You can watch the status by running 'kubectl get svc -w household-dns-coredns'

    export SERVICE_IP=$(kubectl get svc --namespace household-system household-dns-coredns -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo $SERVICE_IP

It can be tested with the following:

1. Launch a Pod with DNS tools:

kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools

2. Query the DNS server:

/ # host kubernetes
```

I also take a moment to set the Dashboard's name to "Stubernetes" before the next part

## About to check Prometheus

but I noticed the pod can't start because there's not enough CPU.

I'd look at Prometheus, but, well...

so I run `kubectl describe nodes` and look at my reservations:

```
Non-terminated Pods:          (39 in total)
  Namespace                   Name                                                           CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----                                                           ------------  ----------  ---------------  -------------  ---
  household-system            household-dns-announcer-external-dns-68bf958cff-z9cf9          0 (0%)        0 (0%)      0 (0%)           0 (0%)         3h49m
  household-system            household-dns-coredns-74c644766f-m9vqj                         100m (5%)     100m (5%)   128Mi (3%)       128Mi (3%)     21m
  household-system            household-dns-etcd-0                                           0 (0%)        0 (0%)      0 (0%)           0 (0%)         3h55m
  kube-system                 coredns-5d9c4cdbb-cn79q                                        100m (5%)     0 (0%)      70Mi (2%)        170Mi (4%)     3d3h
  kube-system                 coredns-5d9c4cdbb-sw2hv                                        100m (5%)     0 (0%)      70Mi (2%)        170Mi (4%)     3d3h
  kube-system                 etcd-studtop                                                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d10h
  kube-system                 kube-apiserver-studtop                                         250m (12%)    0 (0%)      0 (0%)           0 (0%)         3d10h
  kube-system                 kube-controller-manager-studtop                                200m (10%)    0 (0%)      0 (0%)           0 (0%)         3d10h
  kube-system                 kube-proxy-sbfnj                                               0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d10h
  kube-system                 kube-scheduler-studtop                                         100m (5%)     0 (0%)      0 (0%)           0 (0%)         3d10h
  kube-system                 weave-net-r84s2                                                100m (5%)     0 (0%)      0 (0%)           0 (0%)         3d9h
  kubeapps                    kubeapps-fd5d64c59-9wpm9                                       25m (1%)      250m (12%)  32Mi (0%)        128Mi (3%)     3d8h
  kubeapps                    kubeapps-fd5d64c59-bbklr                                       25m (1%)      250m (12%)  32Mi (0%)        128Mi (3%)     3d8h
  kubeapps                    kubeapps-internal-apprepository-controller-7df45dbc68-29vdf    25m (1%)      250m (12%)  32Mi (0%)        128Mi (3%)     3d8h
  kubeapps                    kubeapps-internal-assetsvc-684f9c8574-6zlzj                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d8h
  kubeapps                    kubeapps-internal-assetsvc-684f9c8574-ndlz4                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d8h
  kubeapps                    kubeapps-internal-dashboard-5b77dcfd5f-b6kd6                   25m (1%)      250m (12%)  32Mi (0%)        128Mi (3%)     3d8h
  kubeapps                    kubeapps-internal-dashboard-5b77dcfd5f-c7jwx                   25m (1%)      250m (12%)  32Mi (0%)        128Mi (3%)     3d8h
  kubeapps                    kubeapps-internal-kubeops-d956f46bf-26rhm                      25m (1%)      250m (12%)  32Mi (0%)        256Mi (7%)     3d8h
  kubeapps                    kubeapps-internal-kubeops-d956f46bf-4djdq                      25m (1%)      250m (12%)  32Mi (0%)        256Mi (7%)     3d8h
  kubeapps                    kubeapps-postgresql-master-0                                   250m (12%)    0 (0%)      256Mi (7%)       0 (0%)         3d8h
  kubeapps                    kubeapps-postgresql-slave-0                                    250m (12%)    0 (0%)      256Mi (7%)       0 (0%)         3d8h
  kubernetes-dashboard        kubernetes-dashboard-67985f44b9-7hfn8                          100m (5%)     2 (100%)    64Mi (1%)        200Mi (5%)     3d1h
  metallb-system              metallb-controller-65f446bf5-m829v                             0 (0%)        0 (0%)      0 (0%)           0 (0%)         3d5h
  metallb-system              metallb-speaker-6m5v4                                          25m (1%)      0 (0%)      25Mi (0%)        0 (0%)         3d5h
  openebs                     cstor-bulk-pool-18z9-788849df97-rwtdm                          0 (0%)        0 (0%)      100Mi (2%)       4Gi (118%)     131m
  openebs                     cstor-work-pool-tda0-5596cd6498-7zrk8                          0 (0%)        0 (0%)      100Mi (2%)       4Gi (118%)     131m
  openebs                     openebs-admission-server-65579ddcfd-n2f7t                      0 (0%)        0 (0%)      0 (0%)           0 (0%)         175m
  openebs                     openebs-apiserver-f7cb6bc49-5tq8t                              0 (0%)        0 (0%)      0 (0%)           0 (0%)         123m
  openebs                     openebs-localpv-provisioner-5847dfb954-vv5hd                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         175m
  openebs                     openebs-ndm-operator-7fdcb6b7f4-jnng7                          0 (0%)        0 (0%)      0 (0%)           0 (0%)         158m
  openebs                     openebs-ndm-zpxlw                                              0 (0%)        0 (0%)      0 (0%)           0 (0%)         158m
  openebs                     openebs-provisioner-7bc5bbdc79-rwwq7                           0 (0%)        0 (0%)      0 (0%)           0 (0%)         175m
  openebs                     openebs-snapshot-operator-84bb75f6b6-zvwcg                     0 (0%)        0 (0%)      0 (0%)           0 (0%)         175m
  prometheus                  alertmanager-promop-prometheus-operator-alertmanager-0         100m (5%)     100m (5%)   225Mi (6%)       25Mi (0%)      55m
  prometheus                  promop-grafana-5876c96b68-7dr8q                                0 (0%)        0 (0%)      0 (0%)           0 (0%)         63m
  prometheus                  promop-kube-state-metrics-57c88799dd-qchr2                     0 (0%)        0 (0%)      0 (0%)           0 (0%)         63m
  prometheus                  promop-prometheus-node-exporter-dqfs5                          0 (0%)        0 (0%)      0 (0%)           0 (0%)         63m
  prometheus                  promop-prometheus-operator-operator-57445fc98c-bzwdq           0 (0%)        0 (0%)      0 (0%)           0 (0%)         63m
```

Uh, hmm, why is kubeapps' backing store taking up *half a core of CPU*? That's absurd. I mean, what are these even used for, repo cache?

I'm gonna go ahead and *remove those*...

From the dashboard, I'm just going ahead and removing the Resource allocation on both of the kubeapps StatefulSets (`kubeapps-postgresql-master` and the other one):

```yaml
          resources:
            requests:
              cpu: 250m
              memory: 256Mi
```

A quarter core and a quarter gig? Eeeyikes

## The waves keep coming

I was still having trouble getting to Prometheus, so I tried rebooting. (I accidentally typed it into the wrong terminal and rebooted my desktop first. Derp.)

Coming up, the system started thrashing again, so I went ahead and suspended each Cronjob manually:

```
kubectl get cronjobs -A
kubectl patch cronjobs apprepo-kubeapps-sync-stable -p '{"spec" : {"suspend" : true }}' -n kubeapps
# ... etc ...
```

Man, the more I look at this the more I'm convinced I've REALLY gotta replace Kubeapps

## hey wait a minute

I don't think the issue's insufficient CPU - there's a persistent volume that's not scheduling. Let me look at OpenEBS.

ok, but you know what? these Prometheus things, because they weren't installed by the name "prometheus-operator", they're all called `promop-prometheus-operator` and it's so much worse

so even though, after shaking and deleting all the OpenEBS stuff I want, this won't go away

## what the feck

open-iscsi isn't installed? grah... I think I might have not done it as a pkg install

> The Transactional Server role has fully automated installation of updates and will reboot between 0330 and 0500 in the morning after an update is installed by default, which can be changed.

hmm, I think the server did reboot somewhere in there...

## after a long trial of chasing bugs around

oh, turns out you DO have to enable the iscsid service, socket activation is not enough

`sudo systemctl enable --now iscsid` and now everything's fine

This had a lot of cascading, long-reaching effects as a result of the traffic pileup

not sure what happened to the snapshot I'd been building - I guess the automatic transactional update overwrote the manual one, hence why it says "reboot to prevent data loss" after making one

but I thought I did reboot? Like, a couple times? Not soon enough?

okay, so, to try this again from the top, I'm gonna delete the prometheus namespace...

and now I'm rebooting again just for good measure. Get the bad boot out.

## argh

I went through a bunch of rounds of trying to get Prometheus to install and failing, and the time I finally got it right I forgot to name the app so Kubeapps deployed it as "yellow-earth"

also the PVC never mounted

anyway, I deleted that app

https://github.com/openebs/openebs/issues/3064

## aside: dismissing a thing

I've installed the Sourcegraph plugin, and it gives this popup message on GitHub that's too wordy for me to read right now but too big to let hang around:

> Sourcegraph has hidden GitHub's native hover tooltips. You can toggle this at any time: to enable the native tooltips run “Code host: prefer non-Sourcegraph hover tooltips” from the command palette or set "codeHost.useNativeTooltips": true in your user settings.

## anyway, this issue

Looking at the list of services on the cluster, `promop-prometheus-operator-kubelet` and `yellow-earth-prometheus-op-kubelet` shouldn't be here - deleting them

continuing to [Trying to get OpenEBS Working](9p2xt-m51z4-abbqy-zxs5g-5x24d)
