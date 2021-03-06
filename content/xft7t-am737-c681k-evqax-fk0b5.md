# Ops in a Box Distro Structure

continuing from [rethinking k3s](b5a2f-74yvw-g59sd-qbv5w-hfq4j)

Should cross-reference with Kelsey Hightower's "Kubernetes The Hard Way"

see also [hardware packaging concept](bach6-b6ajg-tqajt-er2cr-9wj00)

## namestorming

My current frontrunner for this iteration of the Ops in a Box idea is "KatesKit" - named like Craigslist, but "kates" actually refers to "k8s"

themes I like:

- box / kit
- one (for "your first node of many")
- cluster / node
- seed / hive analogies

other extensions into those ideas:

- tenkit / repurposing kit10.pet
- "Cluster One", or variations on that
  - hydraz.one
- nodeo / nodeo clown
- mynode (mynode.one)
- zhive

## Overview of a Complete System

At the base, we'd have a special NixOS configuration or whatever that sets up everything below properly configured for setup on first run, a la k3os. You'd configure the Nix stuff instead of k3os.yaml and environs.

Unless Nix has its own better and more commonly used alternative, this'd be set up to use systemd. k3os's OpenRC thing is cute and all

We'd clone a Module Tree of modules that combine to form a plumbus that's initiated with `klubster serve` (where `klubster` is the Plushu command that approximates `k3s` here). The files of the configuration (see below) would be written as part of the Nix manifest-or-whatever, in our system: other implementations would need to develop their own conversion/dynamic-specification pipelines as applicable.

## paths of note in k3os's source tree

to figure out what a k3os alternative would need to handle:

https://github.com/rancher/k3os/blob/master/overlay/libexec/k3os/boot

## klubster configuration

The whole server definition is by a tree of flatfiles, not config options (k3s) or YAML (k3os). This way, you don't end up in the "oh, I changed the way I start the server and now I've lost ingress" state, *and* the configuration is easier to query

You know what, though? Maybe we'll have it so all the locations are specified as environment variables, so one can more easily test overriding a single value by overriding its config location (this is more like how everything was in Plushu-that-was)

## on migration

the wacky thing here is, if I get everything right here, it should be possible to seamlessly migrate *away from my k3os system* using this by just dumping my cluster configuration and taking care of whatever with

see, this is the thing I've wanted, this is the dream that k8s lets me realize: being able to *completely switch ops implementations without having to relearn a whole new containerization model*.

## on clusterability

one thought I have is that "I want to be single-node-optimized" should be an opt-in choice, not opt-out

So, like, k3s's weird little on-disk volume provisioner... eh. I don't know, I don't like it, but I guess it is cluster-compatible

but, like, Longhorn isn't much more overhead, and it makes scaling out *way* more flexible. You're less likely to design for bad practices with Longhorn in place

And, like, the default k3s backing store doesn't support multi-master initially...

eh whatever

## OS thoughts

I'd base it on NixOS

## the bits k3s provides outside the control plane

Flannel

apparently the CNI can still run in-cluster somehow, as a DaemonSet? [kube-router](https://github.com/cloudnativelabs/kube-router) has a mode for this

[more comparison of CNIs](https://itnext.io/benchmark-results-of-kubernetes-network-plugins-cni-over-10gbit-s-network-updated-april-2019-4a9886efe9c4)

[Kubedex comparisons](https://kubedex.com/category/comparisons/) are also useful here

I guess the cluster's backing store is generally run outside the cluster control plane? [k8s docs for HA etcd](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)

hmm, looks like everything other than etcd is a k3s feature, per [k3s datastore docs](https://rancher.com/docs/k3s/latest/en/installation/datastore/)

## Bundle for the k3s cluster services suite

a directory containing manifests (and any other associated configuration) for:

- CoreDNS
- Traefik
- ServiceLB from k3s
- the crude k3s dynamic PVC provisioner / Longhorn

I was originally thinking this'd be a Helm chart: can Helm 3 operate without CoreDNS configured? I think so?

Anyway... I think the "directory of Helm charts for this core bundle, which gets installed either by the Nix thing on first run, or something like a maintenance command as needed, or some other middleware that watches the dir or whatever" is a better model than a single Helm chart that stands for the whole collection?

IDK, this might need some work - but the idea is, the collection of backing services would be bootstrapped in a clear way, and would be de-provisionable by the same method you'd use to set up alternatives (Helm).

## above that

The next thing that would be included in the Ops in a Box package, beyond the k3s base but still within what I would consider a "core feature set to be useful", would be to install:

- Dex
- Some authenticating proxy
- Kubernetes Dashboard
- Kubeapps
- Optional: an SSH server on a NodePort/LoadBalancerIP that only hosts k9s as the shell
  - Or maybe it's a straight image with sshd running, where yo can log in and use kubectl/k9s with bash
  - Hmm, could you have this host actual users (maybe even mediated by Dex)? And, like, certain users can sudo (with passwords or some higher authentication) to users for ServiceAccounts with higher cluster access?
- Some kind of "internal external DNS" like what I'm looking to build for Stubernetes, to make this all accessible
- Certificate service, to make the cert available somewhere the browser trusts

## Past that

Above this core, you'd start packaging the rest of the stuff as Helm charts that could be installed via the Kubeapps portal (and the project would maintain its own Helm repositories accordingly).

(How easy is it to break up an applied Helm chart into its constituent charts? I might need an abstraction around Helm charts for "bundles")

See [suite design](mtwmg-gw5d6-m5a4q-ded6h-dkz12)

In a "home network" cluster configuration, I'd have the cluster be discoverable [via mDNS](g8t4j-w7e5t-0g9rc-6h9p6-4y3wn)
