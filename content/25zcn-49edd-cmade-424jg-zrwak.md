# MicroKube

A component for a CRI-O system (like openSUSE MicroOS) that presents a Kubernetes API, but does not handle any kind of node selection (everything is applied to the host as if it were the only node)

A kind of alternative to Minikube, though really it could fit into Minikube as a component

The thing is, MicroKube can also be used as a kind of "alternative metadistro" package format, where you apply a Helm chart via MicroKube to describe the structure of all

Actually, dang, isn't this a feature of podman already?

## other things I kind of want to call this

lokubus

## Handling Deployments, StatefulSets, DaemonSets etc.

We might have an "alternative Deployment Handler" that turns these kinds of documents into systemd units

In fact, damn, you could make an alternative-to-kubelet Kubernetes that does that anyway, really, I mean systemd does allow for API composition

also, this can totally by known as "ukube" and the internal binary be "microkubelet"

hey, this totally could replace *parts of* kubelet, neat

## I guesss the base idea

Most of the things kubelet would use to run are either not included (because they're only needed for inter-node coordination) or implemented more simply

So, like, actually, is this an alternative to kubeadm? like, I think I was confusing "kubelet" and "kube-api-server", not getting that k3s is basically a weird alternative to Kubelet that also does some of the work of kubeadm on every startup (which is a pain in the ass and I wish it didn't)

so, like, I think kubeadm connects directly to the container runtime and starts pods, and then "kubelet" - like, on Docker, kubelet is the thing that makes sure the containers of a component like kube-api-server are "podified" correctly

I guess I kind of need to get a little clearer on that

but yeah, like, I guess that's the goal here: to see if there's a way to make, like, I guess a Globerlay version of kubeadm / kubelet, where maybe it's a Helm-based version of one of those and the other one is probably a set of shell scripts - and one of these could also be swapped out for the "node that doesn't need to worry about any other nodes" subset (ie. for the "every node is its own little cluster" model)

## bad idea

microkube can provide a similar interface to "kubectl", and then expose that on the shell

nah... that's a different feature. I think that's basically what podman has. what I'm describing here turns the node into one that you can use *actual* kubectl with, and thus actual Helm and all compatible tools (such as kubeapps)

you just have to treat each node as its own "cluster"
