# A Kubernetes-centric Workspace Dashboard

This could be a project of Wayside:

- a Custom Resource Definition of Workspace, which defines "a Pod having this volume layout"

The view applies for all the Workspaces (or whatever construct this oversees) in a Namespace, kind of like kubeapps

You'd have some other layer where we apply our Workspace definition to a Pod template, for the WorkspacePods

Since you can only have the PersistentVolumeClaim write-attached to one Pod, right? we have a "Main Driver Pod" thing, where you can set the base image or swap it

is this too fancy? Should this just be pods?

Nah, we should make it so the Workspace can be "dual-booted", and, you know, images can be upgraded, things like that

But yeah, maybe it's a construct of images with stackable layers that can be swapped?

## How is this not Kubeapps?

Because, unlike most conventional Kubernetes uses, this treats Pods like *open windows*, or tabs or whatever you want to call a desktop-level process. It puts manual pod invocation right in front of the user: since they're brought up on-demand, and should live as long as possible, we're not targeting any kind of conventional ReplicaSet-type model.

On that note, this might have a "resources used vs. activity" dashboard, where it can *suggest* closing Pods you're not using to free up resources on the node

You might also have a "migrate Pod"

Ooh, what about that "netfork" or whatever project I saved a link to somewhere? Is there a way we could help apps like that migrate, so that there's as little disruption to the user experience as possible?

Like, see, here's the thing: as a development abstraction, all this kind of cluster stuff isn't something we can design around, because the low-level primitives aren't ours to play with. The best we can do is what desktops do for preserving state (short of true "hibernation"): tell programs we're shutting down and they need to sync their state to disk, then startup.

(And, of course, it's worth considering actual VM abstractions, which would allow *actual* memory hibernation)

## Similar projects? / overlap

- https://github.com/podenv/silverkube
- [Kubexplorer idea](k2ecq-hqxgs-ccax1-s1p85-59s4s)

## Making it less Kubernetes-centric

Maybe the "Dashboard" configuration level has a "Kubernetes APIs we have access to" so this can potentially span multiple clusters?

This is also the opportunity to have the driver be broader than "Kubernetes"

PVCs not spanning clusters is just like the flash drive on your desk not spanning to the WAN: synchronization would likely be an ad-hoc component

But this does raise a clear model separation layer aspect: the "Environment", which I guess was one of the senses I used "Way" at one point. Like, an Environment can have multiple StatefulSets, but, like, you can't have a PVC truly hop clusters without being remade (or maybe some fancy host-level abstractions)

maybe these are Lands or Islands or Worlds or Universes

or, hell, maybe we'll just call 'em Clusters. Your local machine is a one-node cluster using the HostPortC9 driver or whatever

## Okay, so

Wait, are Pods mutable? Can you add and remove containers on them? Like, is that how Kubernetes Dashboard does its shell?

Anyway, I'm thinking now that maybe switching Containers within a Pod can be a "switch which view" mechanism

remember, Volumes can't be mounted r/w to multiple pods, but they *can* be to multiple containers *within a pod*
