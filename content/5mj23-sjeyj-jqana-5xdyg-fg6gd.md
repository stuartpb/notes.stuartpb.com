# Following the Rebuilding Plan

## Installing Kubeapps

from Stushiba this time:

```
"bitnami" has been added to your repositories
[stuart@stushiba ~]$ kubectl create namespace kubeapps
namespace/kubeapps created
[stuart@stushiba ~]$ helm install kubeapps --namespace kubeapps bitnami/kubeapps --set useHelm3=true
```

I have the brief thought "oh wait, I should have edited the values", but then I remember, anything I want to set this up with, I'll be using it to set up, so

I check and the latest version is since the merge to switch to Postgres

```
NAME: kubeapps
LAST DEPLOYED: Thu Jun 11 22:13:46 2020
NAMESPACE: kubeapps
STATUS: deployed
REVISION: 1
NOTES:
** Please be patient while the chart is being deployed **

Tip:

  Watch the deployment status using the command: kubectl get pods -w --namespace kubeapps

Kubeapps can be accessed via port 80 on the following DNS name from within your cluster:

   kubeapps.kubeapps.svc.cluster.local

To access Kubeapps from outside your K8s cluster, follow the steps below:

1. Get the Kubeapps URL by running these commands:
   echo "Kubeapps URL: http://127.0.0.1:8080"
   kubectl port-forward --namespace kubeapps service/kubeapps 8080:80

2. Open a browser and access Kubeapps using the obtained URL.
[stuart@stushiba ~]$ kubectl port-forward --namespace kubeapps service/kubeapps 8080:80
error: unable to forward port because pod is not running. Current status=Pending
```

## side thought: Helm on Studtop?

Like, K3OS had Helm...

eh ok fine, next time I'm on the shell I'll put it in for the next transaction

(tries later) oh, it's already installed, of course

## oh damn, right, auth access

Rifling through [the old notes](kbryw-6dc19-r18n7-6pg5d-4aqwf)

relevant stuff was on [this early setup log](hznbe-6dznn-cc973-hwsef-rs7mj)

```
studtop:~ # kubectl create serviceaccount kubeapps-operator
serviceaccount/kubeapps-operator created
studtop:~ # kubectl create clusterrolebinding kubeapps-operator --clusterrole=cluster-admin --serviceaccount=default:kubeapps-operator
clusterrolebinding.rbac.authorization.k8s.io/kubeapps-operator created
```

getting the next part from [Quick Access Commands/Links](hdh89-1mqmh-0safa-wcvr6-7ptts)

`kubectl get -n default secret $(kubectl get -n default serviceaccount kubeapps-operator -o jsonpath='{.secrets[].name}') -o go-template='{{.data.token | base64decode}}' && echo`

okay, entering that got me in

looking at http://127.0.0.1:8080/#/ns/kubeapps/apps/kubeapps/upgrade hot me thinking about [Form vs Button design](6mkxa-6mzpk-kt8qa-w4815-v6g4q)

Also thinking about OAuth/OIDC proxies but that's a thing for later

## next up: dashboard

following [the directions their Stable chart's readme directs you to](https://github.com/kubernetes/dashboard/tree/master/aio/deploy/helm-chart/kubernetes-dashboard)

I'm gonna make sure to keep all my repos in the "All Namespaces" space. No need to go make even more sync work

UPDATE: oh I think that's just the "kubeapps" namespace

Doing the UI equivalent of `helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/`

I look at the part for [modifying the synchronization pod][syncpod], and I allllmost go for it, but nah

[syncpod]: https://github.com/kubeapps/kubeapps/blob/master/docs/user/private-app-repository.md#modifying-the-synchronization-job

I'm making a `kube-dashboard` namespace and installing this to it

just gonna call the app "dashboard"

## changes to Kubernetes Dashboard's 2.0.1 Helm values

looking at [the docs for dashboard arguments](https://github.com/kubernetes/dashboard/blob/master/docs/common/dashboard-arguments.md)

I'm gonna try `--enable-skip-login`

Also, what the heck, `--enable-insecure-login`. I can let the browser warn me when I'm gonna be stupid about that, and I can choose to disregard it if I'm doing something fancy like my own local virtual hosting; I don't need the dashboard second-guessing me.

## judging resource spec

```yaml
resources:
  requests:
    cpu: 100m
    memory: 200Mi
  limits:
    cpu: 2
    memory: 200Mi
```

PFF WHAT NO. I drop the resource request, that's banuts. 200Mi limit only. If the dashboard requires *200 megs* to run it, I'm not interested.

Thinking a little... I'm OK giving it, like, 64Mi requested. If it can't deal with being shrunk to that in a crisis, too freakin' bad, I'll use kubectl.

## picking rbac policy

```yaml
rbac:
  # Specifies whether namespaced RBAC resources (Role, Rolebinding) should be created
  create: true

  # Specifies whether cluster-wide RBAC resources (ClusterRole, ClusterRolebinding) to access metrics should be created
  # Independent from rbac.create parameter.
  clusterRoleMetrics: true

  # Start in ReadOnly mode.
  # Only dashboard-related Secrets and ConfigMaps will still be available for writing.
  #
  # The basic idea of the clusterReadOnlyRole
  # is not to hide all the secrets and sensitive data but more
  # to avoid accidental changes in the cluster outside the standard CI/CD.
  #
  # It is NOT RECOMMENDED to use this version in production.
  # Instead you should review the role and remove all potentially sensitive parts such as
  # access to persistentvolumes, pods/log etc.
  #
  # Independent from rbac.create parameter.
  clusterReadOnlyRole: false
```

So, if I understand this correctly, this'll make it so the Dashboard, if I skip login, will have read-only access to the cluster through a ClusterRole given access, and will have write access to the kube-dashboard namespace via the Role.

sounds cool let's try it: setting this to `true`

## thought I wrote

> I think I'll probably dial this back to not be able to review secrets after I install this Helm chart (persistentvolumes are fine, it's not like it can read the contents)

revisiting this note to build the stubernetes-system manifest for this, having been able to experience it in production, I recognize that it's saying it can read (and write!) secrets *in the dashboard's namespace* by default, since the "skip login" mode just uses the dashboard's own ServiceAccount credentials (which I believe this option sets up as a "read-only mode").

still worth reviewing later, but not as big of an issue as this made it sound like

## original programming

in fact, I'm kind of considering [whether the default Role should even be created](https://github.com/kubernetes/dashboard/blob/master/aio/deploy/helm-chart/kubernetes-dashboard/templates/role.yaml)

but, like, surely not including this role would cause problems, right?

huh, you can choose not to create a service account? but it'll still make all the docs that reference one? wild

leaving all the rest of the stuff defaulted

## notes from the dashboard install

```
*********************************************************************************
*** PLEASE BE PATIENT: kubernetes-dashboard may take a few minutes to install ***
*********************************************************************************

Get the Kubernetes Dashboard URL by running:
  export POD_NAME=$(kubectl get pods -n kube-dashboard -l "app.kubernetes.io/name=kubernetes-dashboard,app.kubernetes.io/instance=dashboard" -o jsonpath="{.items[0].metadata.name}")
  echo https://127.0.0.1:8443/
  kubectl -n kube-dashboard port-forward $POD_NAME 8443:8443
```

## now, how to access this

https://jtway.co/generate-kubernetes-client-certificates-64782f7a58d5

https://kubernetes.io/docs/setup/best-practices/certificates/

soo... thinking about this, I'll figure out the "client certificate" thing later - right now, I just want to bring back MetalLB

I create a "metallb" namespace in Kubeapps

Since the Stable chart of MetalLB is outdated and deprecated, I'm gonna go with the Bitnami chart [that was just recently added](https://github.com/bitnami/charts/pull/2068), as advertised in [a MetalLB question asking about this](https://github.com/metallb/metallb/issues/523)

Looks like MetalLB would be interested in handling this chart [if it could be worked into the site's Netlify config](https://github.com/metallb/metallb/issues/460)

## a thought

I wonder if it'd be cool to set up a "bitnami charts, but the defaults use stock images"

maybe call it the "stockstream"

## Anyway, configuring

Able to pull my `configInline` from my `stubernetes-setup/resources`

```yaml
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

Gonna enable `prometheus.serviceMonitor`

## picking the images

reviewing https://hub.docker.com/u/metallb/

Setting the images to metallb/{speaker,controller} and the tags to v0.9.3, respectively

## dedicating little a resources

```yaml
    requests:
       memory: 25Mi
       cpu: 25m
```

## failure

```
Release "metallb" failed and has been uninstalled: unable to build kubernetes objects from release manifest: unable to recognize "": no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1"
```

So I guess this needs to carry the ServiceMonitor CRD in its chart... eh whatever, I can still add the promop first. Might be worth suggesting this to bitnami though

OK, so I guess I'll enable the Prometheus stuff later. After doing it all again:

```
MetalLB is now running in the cluster

LoadBalancer Services in your cluster are now available on the IPs you
defined in MetalLB's configuration. To see IP assignments,

    kubectl get services -o wide --all-namespaces | grep --color=never -E 'LoadBalancer|NAMESPACE'

should be executed.

To see the currently configured configuration for metallb run

    kubectl get configmaps --namespace metallb-system metallb -o yaml

in your preferred shell.
```

## okay, now let's expose the Dashboard

you know what? everything being called `dashboard-kubernetes-dashboard` here is really stupid, I'm gonna reinstall it and call it `kubernetes-dashboard`

In the service spec, I'm gonna set the type to `LoadBalancer` and `192.168.42.64` as the loadBalancerIP

Also, hey, metricsScraper wasn't enabled in the earlier version! This chart has an option to install metrics-server if your cluster doesn't have it yet - and, wouldn't you know it, we don't! Apparently it's not something kubeadm does?

## exposing the Dashboard

for some reason the LoadBalancer gave it `192.168.32.0` even though it should have been given a fixed address?

oh, [the chart doesn't recognize the value](https://github.com/kubernetes/dashboard/blob/master/aio/deploy/helm-chart/kubernetes-dashboard/templates/service.yaml)

hmm, so, we can't get the selfsigned certificate [it generates by default?](https://github.com/kubernetes/dashboard/blob/master/aio/deploy/helm-chart/kubernetes-dashboard/templates/deployment.yaml)

## oh, right, I remember why I used kubernetes-dashboard as the namespace

all the docs assume that's where you've installed it

Okay, let's try this again, one last time...

why is the service getting 192.168.32.0? `kubectl get service kubernetes-dashboard -n kubernetes-dashboard -o yaml`

oh, [the chart doesn't recognize the value](https://github.com/kubernetes/dashboard/blob/master/aio/deploy/helm-chart/kubernetes-dashboard/templates/service.yaml)

still can't connect to 192.168.42.64, and the kubectl proxy 503s with `no endpoints available for service \"https:kubernetes-dashboard:\"`

## hmm

tracking this down: https://stackoverflow.com/questions/52893111/no-endpoints-available-for-service-kubernetes-dashboard

`kubectl describe pods -A -l=k8s-app=kube-dns` shows events like this:

`Failed to create pod sandbox: rpc error: code = Unknown desc = failed to create pod network sandbox k8s_coredns-5d9c4cdbb-pdp54_kube-system_75ff22fa-2465-41b1-83f8-97ebe394746f_0(5a1f27436c69b3c56e75de32383e33cba5d503bf7da380206360c324f9166d0f): unable to allocate IP address: Post http://127.0.0.1:6784/ip/5a1f27436c69b3c56e75de32383e33cba5d503bf7da380206360c324f9166d0f: dial tcp 127.0.0.1:6784: connect: connection refused`

I'm not sure why that happened, but... `kubectl delete pods -A -l=k8s-app=kube-dns`

## GROOOAN

```
studtop:~ # kubectl describe services -n kubernetes-dashboard
Name:                     kubernetes-dashboard
Namespace:                kubernetes-dashboard
Labels:                   app.kubernetes.io/component=kubernetes-dashboard
                          app.kubernetes.io/instance=kubernetes-dashboard
                          app.kubernetes.io/managed-by=Helm
                          app.kubernetes.io/name=kubernetes-dashboard
                          app.kubernetes.io/version=2.0.1
                          helm.sh/chart=kubernetes-dashboard-2.0.1
                          kubernetes.io/cluster-service=true
Annotations:              meta.helm.sh/release-name: kubernetes-dashboard
                          meta.helm.sh/release-namespace: kubernetes-dashboard
Selector:                 app.kubernetes.io/component=kubernetes-dashboard,app.kubernetes.io/instance=kubernetes-dashboard,app.kubernetes.io/name=kubernetes-dashboard
Type:                     LoadBalancer
IP:                       10.107.158.84
IP:                       192.168.42.64
LoadBalancer Ingress:     192.168.42.64
Port:                     https  443/TCP
TargetPort:               https/TCP
NodePort:                 https  31665/TCP
Endpoints:                10.32.0.29:8443
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Normal   IPAllocated                   41m                metallb-controller         Assigned IP "192.168.32.0"
  Normal   nodeAssigned                  36m (x3 over 41m)  metallb-speaker            announcing from node "studtop"
  Normal   IPAllocated                   36m                metallb-controller         Assigned IP "192.168.42.64"
  Normal   LoadbalancerIP                36m                service-controller         -> 192.168.42.64
  Normal   nodeAssigned                  23m (x2 over 26m)  metallb-speaker            announcing from node "studtop"
  Warning  FailedToUpdateEndpointSlices  14m (x6 over 14m)  endpoint-slice-controller  Error updating Endpoint Slices for Service kubernetes-dashboard/kubernetes-dashboard: node "studtop" not found


Name:              kubernetes-dashboard-metrics-server
Namespace:         kubernetes-dashboard
Labels:            app=metrics-server
                   app.kubernetes.io/managed-by=Helm
                   chart=metrics-server-2.11.1
                   heritage=Helm
                   release=kubernetes-dashboard
Annotations:       meta.helm.sh/release-name: kubernetes-dashboard
                   meta.helm.sh/release-namespace: kubernetes-dashboard
Selector:          app=metrics-server,release=kubernetes-dashboard
Type:              ClusterIP
IP:                10.99.2.215
Port:              <unset>  443/TCP
TargetPort:        https/TCP
Endpoints:         10.32.0.21:8443
Session Affinity:  None
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Warning  FailedToUpdateEndpointSlices  14m (x6 over 14m)  endpoint-slice-controller  Error updating Endpoint Slices for Service kubernetes-dashboard/kubernetes-dashboard-metrics-server: node "studtop" not found
```

okay, that's it: [Abortive Attempt at Using systemd-resolved](vwth9-jcpk5-8fa3d-kke73-0cwtq)

## trying a different tack

The service issue seems to have resolved itself(?), so let's forget about the proxy for a moment and go straight for a direct connection to the API again:

https://192.168.0.23:6443/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

Okay, so after copying the client key and certificate parts out from my kubeconfig and de-base-64ing them, running `openssl pkcs12 -export -clcerts -inkey stubernetes.key -in stubernetes.crt -out stubernetes.p12 -name "stubernetes-admin-client"` per https://jtway.co/generate-kubernetes-client-certificates-64782f7a58d5, importing it into Firefox, going to the page in Firefox and revoking the "security exception", then approving the "this site wants to identify you" window...

and I'm back to where I started.

```json
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {

  },
  "status": "Failure",
  "message": "no endpoints available for service \"https:kubernetes-dashboard:\"",
  "reason": "ServiceUnavailable",
  "code": 503
}
```

unfuckin'believable

trying to write it as `https://192.168.0.23:6443/api/v1/namespaces/kubernetes-dashboard/services/kubernetes-dashboard:https/proxy/#/login` still comes back "Client sent an HTTP request to an HTTPS server."

# it dawns on me

Anyway, you know what? Maybe it's considering the pod unhealthy because the metrics-server really is going on the fritz

But, like, that's a whole different pod? It doesn't seem to impact the dashboard's health at all? By all appearances the dashboard pod is ready

anyway fuck it; metrics are better handled by Prometheus/Grafana anyway

## upgraded with the metrics stuff disabled

and, oh hey wouldn't you know it, there the service is.

maybe I'll let Prometheus handle it all and the Dashboard / scaling can derive from that. Or I just cave and introduce metrics-server into kube-system properly when it's time to look at proper inter-node scaling, since it shouldn't be that much overhead, but also not worth that much overhead when we're already including Prometheus

## possible metrics roadmap

Would it maybe make sense to have every node run metrics-server and, what, node-scraper or whatever the Prometheus collector daemon is, and then have the "master control" node

the Skip button does nothing? and uploading my Kubeconfig says `Internal error (500): Not enough data to create auth info structure.`

anyway, time to sleep, I can figure this out in the morning

## what's next

After thinking on it for a good while...

I think we've witness what we were looking for enough with the dashboard re: making sure MetalLB works

at this point I think the more important things are setting up Prometheus/Grafana/Loki (to monitor health, the thing you really want a good UI for - you can do the document stuff from kubectl)

and setting up the Household DNS

- When considering how I'd want to do exposed-ingress, I decided [Fuck It, We're Going Multi-Node](n22cj-by17q-c18bz-29e33-rw6am)
  - Also thinking about, you know, adding Raspberry Pis, a dedicated master-control node, the Torrent Bulk Master which may overlap master control and Prometheus Influx
  - I'm thinking I'll probably experiement with this trying to run a Talos cluster on Packet

If and when I want to return to bringing up the Dashboard interface, I can see [Introducing a User Construct: Consider the Dashboard](0jak2-74hwh-jvax7-1ntwt-kfm8g)

somewhere around here, as I was figuring out how to provision service for a mixed cluster, it dawned on me [How Helm and Operators Work Orthogonally](0f0ss-0c1b4-vdbqg-dr1j8-bcyg9)

anyway, what's *next* is pinned t othe Rebuild Plan right now: Longhorn and Household DNS, then seeing if oauth2-proxy/Dex/Keycloak(ugh) is straightforward enough, and using that for the Dashboard and Grafana if so (and just creating a new cluster-admin if not - still want to figure out how all this can work, but essentially, the things I'd want to be read-only on the Dashboard are things I can make visible like that via Grafana)
