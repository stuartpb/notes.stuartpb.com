# Stubernetes System, Now on Appropriate Hardware

Revisiting my notes from the last time I got this close:

- [Initial Stubernetes setup](qcptj-gdnfj-g2a7q-29gbv-66s09)
  - a reminder to set annotations
- [The next wave](b13jy-1nwxy-gj9z2-1jmw5-qawr2)
  - starting by rolling out metallb
- [A Screeching Halt](394x5-5xy60-7q9ry-9d4n4-cx1re)

## adding annotations

`kubectl annotate pods --all st8s.testtrack4.com/zone=403`

## once that's done

`cd stubernetes-system/helmreleases`

`kubectl create -f metallb.yaml`

after waiting a little while:

```
[stuart@stushiba ~]$ kubectl get pods -A
NAMESPACE            NAME                                               READY   STATUS    RESTARTS   AGE
kube-system          coredns-76679c6978-gp4wv                           1/1     Running   0          81m
kube-system          coredns-76679c6978-sgsdn                           1/1     Running   0          81m
kube-system          etcd-sturl.403.testtrack4.com                      1/1     Running   0          81m
kube-system          kube-apiserver-sturl.403.testtrack4.com            1/1     Running   0          81m
kube-system          kube-controller-manager-sturl.403.testtrack4.com   1/1     Running   1          81m
kube-system          kube-proxy-9g667                                   1/1     Running   0          25m
kube-system          kube-proxy-g9w46                                   1/1     Running   0          26m
kube-system          kube-proxy-trgjp                                   1/1     Running   0          25m
kube-system          kube-proxy-x7hnb                                   1/1     Running   0          81m
kube-system          kube-scheduler-sturl.403.testtrack4.com            1/1     Running   1          81m
kube-system          weave-net-842gh                                    2/2     Running   0          29m
kube-system          weave-net-n96tr                                    2/2     Running   1          25m
kube-system          weave-net-ngvm9                                    2/2     Running   0          25m
kube-system          weave-net-qjf77                                    2/2     Running   0          26m
stubernetes-system   stubernetes-core-helm-operator-8757df5dc-xbv54     1/1     Running   0          29m
```

hmm. no metal stuff... here are the logs for the helm operator

```
W0914 03:54:23.828907       6 client_config.go:543] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
ts=2020-09-14T03:54:23.833778213Z caller=operator.go:82 component=operator info="setting up event handlers"
ts=2020-09-14T03:54:23.834021961Z caller=operator.go:100 component=operator info="event handlers set up"
ts=2020-09-14T03:54:23.83413991Z caller=main.go:287 component=helm-operator info="waiting for informer caches to sync"
ts=2020-09-14T03:54:23.934647746Z caller=main.go:292 component=helm-operator info="informer caches synced"
ts=2020-09-14T03:54:23.935150409Z caller=git.go:104 component=gitchartsync info="starting sync of git chart sources"
ts=2020-09-14T03:54:23.935290535Z caller=operator.go:112 component=operator info="starting operator"
ts=2020-09-14T03:54:23.935373144Z caller=operator.go:114 component=operator info="starting workers"
ts=2020-09-14T03:54:23.936202859Z caller=server.go:42 component=daemonhttp info="starting HTTP server on :3030"
ts=2020-09-14T03:54:24.299968697Z caller=checkpoint.go:24 component=checkpoint msg="up to date" latest=0.10.1
ts=2020-09-14T04:12:16.963808443Z caller=release.go:75 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="starting sync run"
ts=2020-09-14T04:12:22.244462925Z caller=release.go:275 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="running installation" phase=install
ts=2020-09-14T04:12:26.638877603Z caller=release.go:278 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 error="installation failed: unable to build kubernetes objects from release manifest: unable to recognize \"\": no matches for kind \"ServiceMonitor\" in version \"monitoring.coreos.com/v1\"" phase=install
ts=2020-09-14T04:12:26.651064023Z caller=release.go:330 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 warning="uninstall failed: uninstall: Release not loaded: metallb: release: not found" phase=uninstall
ts=2020-09-14T04:12:26.701674389Z caller=release.go:75 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="starting sync run"
ts=2020-09-14T04:12:26.744709853Z caller=release.go:275 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="running installation" phase=install
ts=2020-09-14T04:12:30.54664433Z caller=release.go:278 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 error="installation failed: unable to build kubernetes objects from release manifest: unable to recognize \"\": no matches for kind \"ServiceMonitor\" in version \"monitoring.coreos.com/v1\"" phase=install
ts=2020-09-14T04:12:30.571722829Z caller=release.go:330 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 warning="uninstall failed: uninstall: Release not loaded: metallb: release: not found" phase=uninstall
ts=2020-09-14T04:15:23.89921207Z caller=release.go:75 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="starting sync run"
ts=2020-09-14T04:15:23.922266971Z caller=release.go:275 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="running installation" phase=install
ts=2020-09-14T04:15:28.658814437Z caller=release.go:278 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 error="installation failed: unable to build kubernetes objects from release manifest: unable to recognize \"\": no matches for kind \"ServiceMonitor\" in version \"monitoring.coreos.com/v1\"" phase=install
ts=2020-09-14T04:15:28.680512789Z caller=release.go:330 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 warning="uninstall failed: uninstall: Release not loaded: metallb: release: not found" phase=uninstall
ts=2020-09-14T04:18:23.899126018Z caller=release.go:75 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="starting sync run"
ts=2020-09-14T04:18:23.925815156Z caller=release.go:275 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="running installation" phase=install
ts=2020-09-14T04:18:28.631462783Z caller=release.go:278 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 error="installation failed: unable to build kubernetes objects from release manifest: unable to recognize \"\": no matches for kind \"ServiceMonitor\" in version \"monitoring.coreos.com/v1\"" phase=install
ts=2020-09-14T04:18:28.698806132Z caller=release.go:330 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 warning="uninstall failed: uninstall: Release not loaded: metallb: release: not found" phase=uninstall
ts=2020-09-14T04:21:23.898548415Z caller=release.go:75 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="starting sync run"
ts=2020-09-14T04:21:23.922211567Z caller=release.go:275 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 info="running installation" phase=install
ts=2020-09-14T04:21:28.661911107Z caller=release.go:278 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 error="installation failed: unable to build kubernetes objects from release manifest: unable to recognize \"\": no matches for kind \"ServiceMonitor\" in version \"monitoring.coreos.com/v1\"" phase=install
ts=2020-09-14T04:21:28.685787718Z caller=release.go:330 component=release release=metallb targetNamespace=metallb-system resource=stubernetes-system:helmrelease/metallb helmVersion=v3 warning="uninstall failed: uninstall: Release not loaded: metallb: release: not found" phase=uninstall
```

oh, derp, right. you really do have to set up prometheus first

`kubectl delete -f metallb.yaml` for good measure

now doing `kubectl create -f prometheus-operator.yaml` followed by `kubectl create -f prometheus-operator.yaml`

```
[stuart@stushiba ~]$ kubectl get pods -A
NAMESPACE            NAME                                                     READY   STATUS                  RESTARTS   AGE
kube-system          coredns-76679c6978-gp4wv                                 1/1     Running                 0          92m
kube-system          coredns-76679c6978-sgsdn                                 1/1     Running                 0          92m
kube-system          etcd-sturl.403.testtrack4.com                            1/1     Running                 0          92m
kube-system          kube-apiserver-sturl.403.testtrack4.com                  1/1     Running                 0          92m
kube-system          kube-controller-manager-sturl.403.testtrack4.com         1/1     Running                 1          92m
kube-system          kube-proxy-9g667                                         1/1     Running                 0          36m
kube-system          kube-proxy-g9w46                                         1/1     Running                 0          36m
kube-system          kube-proxy-trgjp                                         1/1     Running                 0          35m
kube-system          kube-proxy-x7hnb                                         1/1     Running                 0          92m
kube-system          kube-scheduler-sturl.403.testtrack4.com                  1/1     Running                 1          92m
kube-system          weave-net-842gh                                          2/2     Running                 0          39m
kube-system          weave-net-n96tr                                          2/2     Running                 1          36m
kube-system          weave-net-ngvm9                                          2/2     Running                 0          35m
kube-system          weave-net-qjf77                                          2/2     Running                 0          36m
metallb-system       metallb-controller-5f4bd54964-qbtnt                      1/1     Running                 0          2m31s
metallb-system       metallb-speaker-mx8vx                                    1/1     Running                 0          2m31s
metallb-system       metallb-speaker-qprn6                                    1/1     Running                 0          2m31s
metallb-system       metallb-speaker-tmvtb                                    1/1     Running                 0          2m31s
metallb-system       metallb-speaker-tsmtj                                    1/1     Running                 0          2m30s
prometheus           alertmanager-pop-alertmanager-0                          1/2     CrashLoopBackOff        5          3m30s
prometheus           pop-operator-5446d698d8-dg6tx                            2/2     Running                 0          3m57s
prometheus           prometheus-operator-grafana-55984ffbc7-nd4qc             0/2     Init:CrashLoopBackOff   5          3m57s
prometheus           prometheus-operator-kube-state-metrics-bd8f49464-79skp   0/1     CrashLoopBackOff        5          3m57s
prometheus           prometheus-operator-prometheus-node-exporter-562sz       1/1     Running                 0          3m57s
prometheus           prometheus-operator-prometheus-node-exporter-7hxvl       1/1     Running                 0          3m57s
prometheus           prometheus-operator-prometheus-node-exporter-hlv7t       1/1     Running                 0          3m57s
prometheus           prometheus-operator-prometheus-node-exporter-kzthz       1/1     Running                 0          3m57s
prometheus           prometheus-pop-prometheus-0                              3/3     Running                 1          3m18s
stubernetes-system   stubernetes-core-helm-operator-8757df5dc-xbv54           1/1     Running                 0          39m
```

okay... what's wrong with prometheus?

```
[stuart@stushiba ~]$ kubectl logs prometheus-operator-grafana-55984ffbc7-nd4qc -n prometheus
error: a container name must be specified for pod prometheus-operator-grafana-55984ffbc7-nd4qc, choose one of: [grafana-sc-dashboard grafana] or one of the init containers: [grafana-sc-datasources]
[stuart@stushiba ~]$ kubectl logs prometheus-operator-grafana-55984ffbc7-nd4qc -n prometheus grafana
Error from server (BadRequest): container "grafana" in pod "prometheus-operator-grafana-55984ffbc7-nd4qc" is waiting to start: PodInitializing
[stuart@stushiba ~]$ kubectl logs prometheus-operator-grafana-55984ffbc7-nd4qc -n prometheus grafana-sc-datasources
standard_init_linux.go:219: exec user process caused: exec format error
```

aaargh

## revisiting the prometheus operator

updating stuff to `prometheus-community/kube-prometheus-stack` per [deprecation notice][old] pointing to https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

[old]: https://github.com/helm/charts/tree/master/stable/prometheus-operator

doing `kubectl delete helmrelease prometheus-operator -n stubernetes-system` to remove the old deployment

updating the location and version accordingly in the spec

also adding a bit to `spec.values.grafana` so that'll get thrown on the latop too - this can be finessed better later

```
      nodeSelector:
        kubernetes.io/arch: amd64
```

## repeatedly slipping on a banana peel

```
[stuart@stushiba helmreleases]$ kubectl delete helmrelease prometheus-operator -n stubernetes-system
helmrelease.helm.fluxcd.io "prometheus-operator" deleted
[stuart@stushiba helmreleases]$ kubectl create -f kube-prometheus-stack.yaml
helmrelease.helm.fluxcd.io/kube-prometheus-stack created
Error from server (AlreadyExists): error when creating "kube-prometheus-stack.yaml": namespaces "prometheus" already exists
[stuart@stushiba helmreleases]$ kubectl delete ns prometheus
namespace "prometheus" deleted
[stuart@stushiba helmreleases]$ kubectl create -f kube-prometheus-stack.yaml
namespace/prometheus created
Error from server (AlreadyExists): error when creating "kube-prometheus-stack.yaml": helmreleases.helm.fluxcd.io "kube-prometheus-stack" already exists
[stuart@stushiba helmreleases]$ kubectl delete -f kube-prometheus-stack.yaml
namespace "prometheus" deleted
helmrelease.helm.fluxcd.io "kube-prometheus-stack" deleted
[stuart@stushiba helmreleases]$ kubectl create -f kube-prometheus-stack.yaml
namespace/prometheus created
helmrelease.helm.fluxcd.io/kube-prometheus-stack created
```

## still incompatible

```
[stuart@stushiba ~]$ kubectl get pods -A
NAMESPACE            NAME                                                        READY   STATUS             RESTARTS   AGE
kube-system          coredns-76679c6978-gp4wv                                    1/1     Running            0          139m
kube-system          coredns-76679c6978-sgsdn                                    1/1     Running            0          139m
kube-system          etcd-sturl.403.testtrack4.com                               1/1     Running            0          139m
kube-system          kube-apiserver-sturl.403.testtrack4.com                     1/1     Running            0          139m
kube-system          kube-controller-manager-sturl.403.testtrack4.com            1/1     Running            4          139m
kube-system          kube-proxy-9g667                                            1/1     Running            0          83m
kube-system          kube-proxy-g9w46                                            1/1     Running            0          83m
kube-system          kube-proxy-trgjp                                            1/1     Running            0          82m
kube-system          kube-proxy-x7hnb                                            1/1     Running            0          139m
kube-system          kube-scheduler-sturl.403.testtrack4.com                     1/1     Running            4          139m
kube-system          weave-net-842gh                                             2/2     Running            0          86m
kube-system          weave-net-n96tr                                             2/2     Running            1          83m
kube-system          weave-net-ngvm9                                             2/2     Running            0          82m
kube-system          weave-net-qjf77                                             2/2     Running            0          83m
metallb-system       metallb-controller-5f4bd54964-qbtnt                         1/1     Running            0          49m
metallb-system       metallb-speaker-mx8vx                                       1/1     Running            2          49m
metallb-system       metallb-speaker-qprn6                                       1/1     Running            0          49m
metallb-system       metallb-speaker-tmvtb                                       1/1     Running            0          49m
metallb-system       metallb-speaker-tsmtj                                       1/1     Running            0          49m
prometheus           kps-operator-58cc96d744-cl4m2                               0/2     CrashLoopBackOff   10         6m6s
prometheus           kube-prometheus-stack-grafana-5fc689d8f9-jtsmf              2/2     Running            0          6m6s
prometheus           kube-prometheus-stack-kube-state-metrics-5cf575d8f8-mlftk   1/1     Running            0          6m6s
prometheus           kube-prometheus-stack-prometheus-node-exporter-8pxwb        1/1     Running            0          6m6s
prometheus           kube-prometheus-stack-prometheus-node-exporter-k7jrx        1/1     Running            0          6m6s
prometheus           kube-prometheus-stack-prometheus-node-exporter-nc68k        1/1     Running            0          6m6s
prometheus           kube-prometheus-stack-prometheus-node-exporter-v85hc        1/1     Running            0          6m6s
stubernetes-system   stubernetes-core-helm-operator-8757df5dc-xbv54              1/1     Running            0          86m

stubernetes-system   stubernetes-core-helm-operator-8757df5dc-xbv54              1/1     Running            0          86m
[stuart@stushiba ~]$ kubectl logs -n prometheus  kps-operator-58cc96d744-cl4m2
error: a container name must be specified for pod kps-operator-58cc96d744-cl4m2, choose one of: [kube-prometheus-stack tls-proxy]
[stuart@stushiba ~]$ kubectl logs -n prometheus  kps-operator-58cc96d744-cl4m2 kube-prometheus-stack
standard_init_linux.go:219: exec user process caused: exec format error
```

okay, diving into https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/templates/prometheus-operator/deployment.yaml... adding this

```
    prometheusOperator:
      nodeSelector:
        kubernetes.io/arch: amd64
```

## prometheus coming up

```
[stuart@stushiba ~]$ kubectl get pods -A
NAMESPACE            NAME                                                        READY   STATUS              RESTARTS   AGE
kube-system          coredns-76679c6978-gp4wv                                    1/1     Running             0          157m
kube-system          coredns-76679c6978-sgsdn                                    1/1     Running             0          157m
kube-system          etcd-sturl.403.testtrack4.com                               1/1     Running             1          157m
kube-system          kube-apiserver-sturl.403.testtrack4.com                     1/1     Running             1          157m
kube-system          kube-controller-manager-sturl.403.testtrack4.com            1/1     Running             5          157m
kube-system          kube-proxy-9g667                                            1/1     Running             0          100m
kube-system          kube-proxy-g9w46                                            1/1     Running             0          101m
kube-system          kube-proxy-trgjp                                            1/1     Running             0          100m
kube-system          kube-proxy-x7hnb                                            1/1     Running             0          157m
kube-system          kube-scheduler-sturl.403.testtrack4.com                     1/1     Running             5          157m
kube-system          weave-net-842gh                                             2/2     Running             0          104m
kube-system          weave-net-n96tr                                             2/2     Running             1          100m
kube-system          weave-net-ngvm9                                             2/2     Running             0          100m
kube-system          weave-net-qjf77                                             2/2     Running             0          101m
metallb-system       metallb-controller-5f4bd54964-qbtnt                         1/1     Running             0          67m
metallb-system       metallb-speaker-mx8vx                                       1/1     Running             5          67m
metallb-system       metallb-speaker-qprn6                                       1/1     Running             0          67m
metallb-system       metallb-speaker-tmvtb                                       1/1     Running             0          67m
metallb-system       metallb-speaker-tsmtj                                       1/1     Running             0          67m
prometheus           alertmanager-kps-alertmanager-0                             2/2     Running             0          39s
prometheus           kps-operator-66f58c67d-sb28p                                2/2     Running             5          6m21s
prometheus           kube-prometheus-stack-grafana-5fc689d8f9-jtsmf              2/2     Running             0          23m
prometheus           kube-prometheus-stack-kube-state-metrics-5cf575d8f8-mlftk   1/1     Running             0          23m
prometheus           kube-prometheus-stack-prometheus-node-exporter-8pxwb        1/1     Running             0          23m
prometheus           kube-prometheus-stack-prometheus-node-exporter-k7jrx        1/1     Running             0          23m
prometheus           kube-prometheus-stack-prometheus-node-exporter-nc68k        1/1     Running             0          23m
prometheus           kube-prometheus-stack-prometheus-node-exporter-v85hc        1/1     Running             1          23m
prometheus           prometheus-kps-prometheus-0                                 0/3     ContainerCreating   0          28s
stubernetes-system   stubernetes-core-helm-operator-8757df5dc-xbv54              1/1     Running             0          104m
```

as this sets up...

let's just do it and be legends

```
[stuart@stushiba helmreleases]$ kubectl create -f 403-contour.yaml
helmrelease.helm.fluxcd.io/403-contour created
[stuart@stushiba helmreleases]$ kubectl create -f 403-internal-dns.yaml
helmrelease.helm.fluxcd.io/household-dns created
[stuart@stushiba helmreleases]$ kubectl create -f kubernetes-dashboard.yaml
namespace/kubernetes-dashboard created
helmrelease.helm.fluxcd.io/kubernetes-dashboard created
[stuart@stushiba helmreleases]$ kubectl create -f rook-ceph.yaml
namespace/rook-ceph created
helmrelease.helm.fluxcd.io/rook-ceph created
```

... oh right, should have done this first

```
[stuart@stushiba helmreleases]$ kubectl create -f ../namespaces/403-system.yaml
namespace/403-system created
```

## hmm

```
ts=2020-09-14T05:37:28.959909872Z caller=helm.go:69 component=helm version=v3 info="creating 15 resource(s)" targetNamespace=403-system release=403-internal-dns
ts=2020-09-14T05:37:29.129670725Z caller=release.go:278 component=release release=403-internal-dns targetNamespace=403-system resource=stubernetes-system:helmrelease/household-dns helmVersion=v3 error="installation failed: Service \"403-internal-dns-coredns-metrics\" is invalid: metadata.name: Invalid value: \"403-internal-dns-coredns-metrics\": a DNS-1035 label must consist of lower case alphanumeric characters or '-', start with an alphabetic character, and end with an alphanumeric character (e.g. 'my-name',  or 'abc-123', regex used for validation is '[a-z]([-a-z0-9]*[a-z0-9])?')" phase=install
ts=2020-09-14T05:37:29.184213882Z caller=helm.go:69 component=helm version=v3 info="uninstall: Deleting 403-internal-dns" targetNamespace=403-system release=403-internal-dns
```

well, looks like "403-system" wasn't the perfect replacement for "household-system" that I thought it was... I'll go ahead and roll those bits back

## going back

before I try rolling household out again, I note that the rook-ceph release seems to have failed because of... oh right, that dumb templating bug.

I go ahead and update the release version and re-apply it... that works.

## retrying the household components

After doing `kubectl create ns household-system`

```
[stuart@stushiba helmreleases]$ kubectl create -f household-contour.yaml
helmrelease.helm.fluxcd.io/household-contour created
[stuart@stushiba helmreleases]$ kubectl create -f household-internal-dns.yaml
helmrelease.helm.fluxcd.io/household-internal-dns created
```

## more stuff that doesn't work on arm

```
[stuart@stushiba ~]$ kubectl logs -n household-system household-internal-dns-etcd-0
standard_init_linux.go:219: exec user process caused: exec format error
[stuart@stushiba ~]$ kubectl logs -n household-system household-internal-dns-external-dns-7df57b5c7-9tn4d
standard_init_linux.go:219: exec user process caused: exec format error
[stuart@stushiba ~]$ kubectl logs -n household-system household-contour-contour-certgen-dw5wm
standard_init_linux.go:219: exec user process caused: exec format error
```

groooan

## tackling Contour first

Looks like bitnami just put out a new version

`kubectl apply -f https://raw.githubusercontent.com/projectcontour/contour/release-1.8/examples/contour/01-crds.yaml` first per documentation

okay, no, 2.0.0 doesn't fix it. [standing issue in Contour's repo to get ARM images](https://github.com/projectcontour/contour/issues/2868)

looks like adding an amd64 node selector got Contour to work. Now I'm just gonna roll out those node selectors for external-dns and etcd

## cluster looks groovy

```
[stuart@stushiba ~]$ kubectl get pods -A
NAMESPACE              NAME                                                        READY   STATUS    RESTARTS   AGE
household-system       household-contour-contour-bc54469b7-9pklj                   1/1     Running   0          35m
household-system       household-contour-contour-bc54469b7-jz7g5                   1/1     Running   0          35m
household-system       household-internal-dns-coredns-6bf695cb9c-j92nx             1/1     Running   0          79m
household-system       household-internal-dns-etcd-0                               1/1     Running   0          4m37s
household-system       household-internal-dns-external-dns-6598768487-9rbls        1/1     Running   0          4m47s
kube-system            coredns-76679c6978-gp4wv                                    1/1     Running   0          4h25m
kube-system            coredns-76679c6978-sgsdn                                    1/1     Running   0          4h25m
kube-system            etcd-sturl.403.testtrack4.com                               1/1     Running   1          4h25m
kube-system            kube-apiserver-sturl.403.testtrack4.com                     1/1     Running   2          4h25m
kube-system            kube-controller-manager-sturl.403.testtrack4.com            1/1     Running   6          4h25m
kube-system            kube-proxy-9g667                                            1/1     Running   0          3h29m
kube-system            kube-proxy-g9w46                                            1/1     Running   0          3h30m
kube-system            kube-proxy-trgjp                                            1/1     Running   0          3h29m
kube-system            kube-proxy-x7hnb                                            1/1     Running   0          4h25m
kube-system            kube-scheduler-sturl.403.testtrack4.com                     1/1     Running   6          4h25m
kube-system            weave-net-842gh                                             2/2     Running   0          3h33m
kube-system            weave-net-n96tr                                             2/2     Running   1          3h29m
kube-system            weave-net-ngvm9                                             2/2     Running   0          3h29m
kube-system            weave-net-qjf77                                             2/2     Running   0          3h30m
kubernetes-dashboard   kubernetes-dashboard-598d95dbfb-6qx4b                       1/1     Running   0          107m
metallb-system         metallb-controller-5f4bd54964-qbtnt                         1/1     Running   0          176m
metallb-system         metallb-speaker-mx8vx                                       1/1     Running   9          176m
metallb-system         metallb-speaker-qprn6                                       1/1     Running   0          176m
metallb-system         metallb-speaker-tmvtb                                       1/1     Running   0          176m
metallb-system         metallb-speaker-tsmtj                                       1/1     Running   0          176m
prometheus             alertmanager-kps-alertmanager-0                             2/2     Running   0          109m
prometheus             kps-operator-66f58c67d-sb28p                                2/2     Running   5          115m
prometheus             kube-prometheus-stack-grafana-5fc689d8f9-jtsmf              2/2     Running   0          132m
prometheus             kube-prometheus-stack-kube-state-metrics-5cf575d8f8-mlftk   1/1     Running   0          132m
prometheus             kube-prometheus-stack-prometheus-node-exporter-8pxwb        1/1     Running   0          132m
prometheus             kube-prometheus-stack-prometheus-node-exporter-k7jrx        1/1     Running   0          132m
prometheus             kube-prometheus-stack-prometheus-node-exporter-nc68k        1/1     Running   0          132m
prometheus             kube-prometheus-stack-prometheus-node-exporter-v85hc        1/1     Running   3          132m
prometheus             prometheus-kps-prometheus-0                                 3/3     Running   0          109m
rook-ceph              rook-ceph-operator-756d5d87f9-85wsk                         1/1     Running   2          93m
rook-ceph              rook-discover-bc7nv                                         1/1     Running   0          89m
rook-ceph              rook-discover-qzsrz                                         1/1     Running   0          89m
rook-ceph              rook-discover-wl5r8                                         1/1     Running   0          89m
stubernetes-system     stubernetes-core-helm-operator-8757df5dc-xbv54              1/1     Running   0          3h33m
```

## next steps

- [Resetting kube-prometheus-stack](epffv-6ph18-t58fg-c611h-4mwwb)
- [Getting the Ceph storage up and running](j79ha-6y5fn-88ads-07rpk-99bwd)
