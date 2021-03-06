# skydns from scratch

I've decided, rather than bother building a whole chart / double-container pod deployment for this just yet, I'll start by just doing a basic setup using Bitnami's images for etcd and external-dns, and then the stable CoreDNS chart

## setting up etcd

in household-system, obviously

calling this `household-skydns-etcd`

keeping `allowNoneAuthentication` and `auth.rbac.enabled` both `true`, I figure this is harmless

not changing `clusterDomain`, that just seems like a recipe for trouble

setting `persistence.enabled=false` since persistence will be coming from the control plane

leaving everything else unchanged

## notes

```
** Please be patient while the chart is being deployed **

etcd can be accessed via port 2379 on the following DNS name from within your cluster:

    household-skydns-etcd.household-system.svc.cluster.local

To set a key run the following command:

    export POD_NAME=$(kubectl get pods --namespace household-system -l "app.kubernetes.io/name=etcd,app.kubernetes.io/instance=household-skydns-etcd" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- etcdctl put /message Hello

To get a key run the following command:

    export POD_NAME=$(kubectl get pods --namespace household-system -l "app.kubernetes.io/name=etcd,app.kubernetes.io/instance=household-skydns-etcd" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- etcdctl get /message

To connect to your etcd server from outside the cluster execute the following commands:

    kubectl port-forward --namespace household-system svc/household-skydns-etcd 2379:2379 &
    echo "etcd URL: http://127.0.0.1:2379"

 * As rbac is enabled you should add the flag `--user root:$ETCD_ROOT_PASSWORD` to the etcdctl commands. Use the command below to export the password:

    export ETCD_ROOT_PASSWORD=$(kubectl get secret --namespace household-system household-skydns-etcd -o jsonpath="{.data.etcd-root-password}" | base64 --decode)
```

Went to bed after pasting that. after waking up, I was a bit confused about the rbac thing: looks like that's just for interfacing with the pod via kubectl (with a non-cluster-admin user?)

the more I think about it, the more I think including the `rbac` part of this was a mistake, as it shouldn't be interfacing with the cluster API at all.

Or is this for enabling authenticated access, like it needs RBAC to manage the cluster secret? Doesn't sound like it

Anyway, I can mess with removing that later

## setting up external-dns

Using the Bitnami chart, for symmetry and because `stable` is deprecated

Calling this `household-skydns-controller` (because mirroring the last one with `household-skydns-external-dns` would be, just, what, way too confusing)

~~I'm deciding... sure, let's uncomment `crd` from `sources`, let's use this controller for all the DNS records we can, and then we can potentially swap out the coredns bit of the implementation~~ reconsidered, see section about the CRD below

Actually - let's call it `household-dns-controller`, and then we could potentially even swap out the part that communicates via `etcd`

This makes me want to rename the previous component, actually... meh.

Opening https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/coredns.md back up

Setting provider... Oh man, I forgot the SkyDNS/etcd provider is called "coredns", what a mess

setting coredns.etcdEndpoints to "http://household-skydns-etcd.household-system.svc.cluster.local:2379"

I'm gonna give it the annotationFilter "annotations.403.stuartandtiare.com/internal-name". That way, we can set that annotation to give a Service (or whatever) a record in the household name system, while not precluding other external-dns controllers from sharing a record

Let's see, `echo -n annotations.403.stuartandtiare.com/internal-name | wc -c` says that's 48 characters...

let's go `hhk8s.stuartpb.com/internal-name` instead (for "household kubernetes")

setting `triggerLoopOnEvent` to `true` and `policy` to `sync`

Looking into https://github.com/kubernetes-sigs/external-dns/blob/master/docs/proposal/registry.md and setting `registry` to `noop`. since this external-dns solution owns all the records in etcd, keeping an owner-id on them would be meaningless. I can reconsider this if I ever introduce a second controller to this configuration for whatever reason, but I highly doubt that'd happen

## quick thought in the value of that "internal-name" annotation

When I start using controllers like this to expose names, I'll probably use the value to say "which DNS provider should introduce this to the namespace", like `hhk8s.stuartpb.com/exposed-name=cloudflare`

by this logic, the annotation selector for this configuration should be `hhk8s.stuartpb.com/internal-name=coredns`, but, like... I guess I can use that for values when I start assigning them, but I'm not gonna be so fussy? I can fix the annotations by whatever criterion later

## anyway, back to setup

skipping over all the tuning parameters

hmm... if I'm using the CRD, do I want this to be the chart that installs it? hmm

I guess I'll hold off on the CRD thing. I can go back and enable the CRD type later if I want it, and can figure out which chart I want the CRD from

(this could be a cool type of chart: "just the cluster resources, in case you have multiple instances of the same chart installed and don't want any one of them to be more important than the rest")

leaving everything underneath that default:

- service
- serviceAccount
- rbac
- (pod)securityContext
- resources
- the probes
- volume stuff
- metrics

## deploying

it named all the stuff `household-dns-controller-external-dns`. eh whatever, the only name the chart would have let me customize was the serviceaccount

## the last piece of the puzzle

looks like there's only a `stable` chart for coredns, and no README... oof

[here](https://github.com/coredns/coredns/issues/3905) is their migration plan, started five days ago at time of writing. not great

anyway, let's go ahead and use the stable chart for now

oof, their [resources implementation](https://github.com/helm/charts/blob/master/stable/coredns/templates/deployment.yaml#L85) is just "throw the resources object in", so like, fuck you if you don't wanna specify `resources` I guess

almost removed it all, but on the other hand, this *is* DNS - while the external-dns stuff can (probably?) afford to be non-responsive, we definitely don't want our household DNS choking.

let's see, here's what we have for the `cluster-dns` deployment/podspec:

```yaml
          resources:
            limits:
              memory: 170Mi
            requests:
              cpu: 100m
              memory: 70Mi
```

so, like, cluster DNS reserves ~~an entire core...~~ a tenth of a core (100m = 0.1, see below)

okay, looking at the Node in the Dashboard, and it says:

- CPU requests (cores): 525.00m (26.25%)
- CPU limits (cores): 2.45 (122.50%)

so, like, if 525m of CPU is only 26.25%, then we can afford to request 100m?

https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#meaning-of-cpu

ohh, okay, this is in millicore, so `100m` just means "one-tenth of a CPU capacity", so that's fine. an entire core would be `1000m`

and I'm guessing it's allowing some degree of overcommit (hence the "122.50%", and also I'm guessing that's where the ".45" of CPU limit is coming from on a dual-core system)

so yeah, we have 2450m of CPU schedulable, and we've only reserved like a fifth of that (which amounts to just over a quarter of the system's actual capacity)

bottom line, we can afford to reserve another 100m for my household DNS system. I'm fine having, like, a third of the system's resources dedicated to "extremely important shit that can not break"

oh, wait, okay, I get it, CPU limits means the *actual sum of CPU limits*, like "this is the most CPU the pods on this node could potentially use". I'm still a little confused, though, because, like... not all my pods have limits? Do they not count toward that total?

anyway this is just telling me "if all the pods on your node hit their limit, there wouldn't be more room", which is like... okay? it's not like memory limits - if you run out of CPU, you just have to slow down to compensate (which I guess is why the dashboard shows a full circle here as just Orange)

## aside as I browse the node in the dashboard

Wow, one of the kubeapps repo sync pods was using like 100M of RAM for a bit?

looks like that was a momentary sync operation, our RAM overview for the node isn't showing any spiking, pretty low line

## anyway, the coredns resources

Gonna leave these at the default:

```yaml
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

## continuing with the value setup

setting the ServiceType to "LoadBalancer" with a LoadBalancerIP of "192.168.42.53"

leaving the prometheus scrape annotations on, fuck it

## a snag re: RBAC and PSP

uhh...

```yaml
serviceAccount:
  create: false
  # The name of the ServiceAccount to use
  # If not set and create is true, a name is generated using the fullname template
  name:

rbac:
  # If true, create & use RBAC resources
  create: true
  # If true, create and use PodSecurityPolicy
  pspEnable: false
  # The name of the ServiceAccount to use.
  # If not set and create is true, a name is generated using the fullname template
  # name:
```

so, like, the default is to use RBAC, but not create a ServiceAccount? that seems completely broken?

anyway, since this isn't going to be operating in a mode where it needs to monitor cluster resources (that's what the external-dns controller is for), I don't think we actually need any ClusterRole stuff here - going ahead and setting `rbac.create: false`

also setting `isClusterService` to `false` since that's not what this CoreDNS instance is for, we don't need an instance running to provide service for each node

I kind of don't get why the pspEnable is under rbac? I guess they tie the use of PodSecurityPolicy to the ClusterRole? that seems unnecessary, though if you're designing this chart for a world where RBAC is usually required I guess that makes sense

so like, if this pod runs without rbac, does that mean it's going to have to have root permissions and crap, just because it's gonna bind internal to port 53?

again, I am just coming to a great big massive "whatever" - this can be cleaned up at some point in the future when I'm a Kubernetes master and exporting my own Charts for all these services

## okay, here's where we're really going off-road

Pulling the Kubernetes section out and replacing it with an etcd record

reviewing the [etcd plugin documentation](https://coredns.io/plugins/etcd/)

also reviewing the [external-dns coredns docs](https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/coredns.md)

the external-dns doc *does* say to turn RBAC on? but the schema it cites is out of date... I'm just gonna roll the dice with my "we don't need no steenking ClusterRoles" plan

their example lists a "stubzones" directive which is no longer documented, [maybe pulled out](https://github.com/coredns/coredns/issues/256)? anyway, I'm betting this is no longer a thing, so I'm just gonna omit that

[more history re: stubzones](https://gitlab.cncf.ci/coredns/coredns/commit/ebef64280a90f9740870125ec092ccd558fd13f9)

I've decided to make this authoritative for `.internal` names: I could set the authority at `.` and do a fallback (and use names at whatever domain), but I think I want to see what the perf is like for these internal lookups first

anyway, I replace the Kubernetes block with this:

```yaml
  - name: etcd
    parameters: internal
    configBlock: |-
      path /skydns
      endpoint http://household-skydns-etcd.household-system.svc.cluster.local:2379
```

## the core outside-DNS-resolution strategy

The most critical section to replace in this configuration:

```yaml
  - name: forward
    parameters: . /etc/resolv.conf
```

This would cause an infinite loop (which would presumably halt the server, thanks to the `loop` plugin at the end of our configuration), since we're gonna reconfigure the router to offer *this server* over DHCP, which is what resolv.conf would direct to.

(Incidentally, if we feel like tightening the loop a bit by removing a couple levels of indirection, we might want to redesign the cluster-dns configmap to point to the ClusterIP for our household-dns instead of falling back to resolv.conf.)

The question is: where do be break the loop?

Now, if I wanted to get *real* "seamless and unopinionated", I'd come up with some way for the system to make a DHCP request to the ISP *through* the router somehow (DMZ?) and use the DNS recommended there.

However, not only is that outside my wheelhouse in terms of complexity (for all I know, I might even have to reflash my router's firmware to something open-source to support it), it's also not all that useful: who ever said "I want to go out of my way to use my ISP's DNS infrastructure"?

The way I see it, part of setting up this in-house DNS system is so that I can have an endpoint that falls back to the global DNS backbone servers that everybody suggests integrating into your network configuration *anyway*. I can *also* use this to enforce TLS for all our name lookups! (This was a big part of the motivation to set this up, actually.)

I'm planning on stealing the list of fallback DNS straight from [Arch Linux's systemd-resolved package][L109] (as mentioned [on the wiki](https://wiki.archlinux.org/index.php/Systemd-resolved))

[L109]: https://git.archlinux.org/svntogit/packages.git/tree/trunk/PKGBUILD?h=packages/systemd#n109

I *could* set `k3os.dns_nameservers` to these, but that'd mean that the `cluster-dns` system would be the one thing on my network that *doesn't* resolve internal names, which would be a recipe for chaos. (You could argue that using external-dns-supplied internal names for inter-cluster communication is the real chaos, but, like, I think the bigger issue would be needing to treat cluster access as a special case - especially if `.internal` goes cross-cluster at some point!)

That could also be worked around by making it so that the `cluster-dns` server falls back to `household-dns` directly, and then `household-dns` goes to the native resolver, but at that point we're introducing a twistier maze of passages than we need - I'm trying to make as few changes to the upstream configuration as possible. And what if we introduce *another* DNS server? If we wanted *that* server to be internal-name-aware, we'd have to make the same customization!

Admittedly, changing the native resolver's DNS like that would make for a good system if I wanted to make a platform for isolated DNS realms (where I can easily isolate `.internal` away), but that's not the model I'm going for. I want `household-dns` to be the base DNS resolver for this network - any system that joins the network and plays by our DHCP rules gets let in on the .internal name scheme.

as such, I'm gonna proceed by hard-coding our chosen resolvers and making them secure-only:

```yaml
  - name: forward
    parameters: . tls://1.1.1.1 tls://9.9.9.10 tls://8.8.8.8
```

I could add the IPv6 versions, but meh. (Maybe at some point I'll set that up for the configuration of a fallback DNS server? You know, for all those cases where IPv4 goes down but IPv6 is still working)

anyway, no further changes, not enabling autoscaler

## aside: what would I do if I couldn't set up an internal name server?

probably something like [this](2hab2-yzhjv-9c8hq-865y5-tb4mr), which could still be useful for kateskit/hivebug

## fuck

```
Release "household-dns" failed and has been uninstalled: Service "household-dns-coredns" is invalid: spec.ports: Invalid value: []core.ServicePort{core.ServicePort{Name:"udp-53", Protocol:"UDP", Port:53, TargetPort:intstr.IntOrString{Type:0, IntVal:53, StrVal:""}, NodePort:0}, core.ServicePort{Name:"tcp-53", Protocol:"TCP", Port:53, TargetPort:intstr.IntOrString{Type:0, IntVal:53, StrVal:""}, NodePort:0}}: cannot create an external load balancer with mix protocols
```

so hahaha the CoreDNS chart just fucking ships broken, it has values for setting it up as a LoadBalancer service but cannot actually use them

what it is is that we have to create *two separate Services* when using a LoadBalancer and a port that can be either TCP or UDP https://github.com/kubernetes/kubernetes/issues/23880#issuecomment-269054735

OMFG, this isn't even necessary with MetalLB? https://github.com/kubernetes/kubernetes/issues/23880#issuecomment-407709879

[more on the double-service workaround](https://github.com/kubernetes/kubernetes/issues/23880#issuecomment-445879863)

omfg this Kubernetes bureaucracy is mind-numbing https://github.com/kubernetes/enhancements/pull/1438

we've known that this works since *2018* and they *still* can't figure out a way to fix this, infuckingcredible

https://twitter.com/stuartpb/status/1265824067774320641

## anyhoo

Looks like I'm just gonna have to hack it by installing the service as a ClusterIP, then manually splitting it into two LoadBalancer services. On one hand, this is the sort of logic that should ship as part of the chart if this is going to be an issue, but on the other hand there is no reason on Earth it should be an issue. Fuck it whatever

wanting an explanation for this hack tied to the resource gave me the idea for [kublam](nh005-8sp5r-0fay4-1yt9r-fqegw)

## now what

Trying again...

```
<html>

<head><title>502 Bad Gateway</title></head>

<body>

<center><h1>502 Bad Gateway</h1></center>

<hr><center>nginx</center>

</body>

</html>
```

that's the failure message it printed when I tried to install it with the Service as ClusterIP. yes, with the raw HTML, like that.

okay you know what fuck this, this is just not happening on kubeapps. I'm gonna clone the chart, manually unfuck it and deploy it from the command line

[to be continued...](gjz6j-9sfy9-gv90y-46tcf-9yd89)
