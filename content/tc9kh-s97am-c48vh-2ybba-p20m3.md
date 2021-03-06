# Rethinking the CNI

I originally went with Weave because that's what the Kubic Wiki suggested.

However, I've been having problems that look like the CNI malfunctioning, and it seems like there are a couple CNI solutions that are a little lighter weight.

## Reviewed docs

- https://www.reddit.com/r/kubernetes/comments/dy6rgu/networking_calico_vs_weave_vs_flannel/
- https://www.reddit.com/r/kubernetes/comments/9n5677/is_kuberouter_the_way_forward_in_kubernetes/
- https://itnext.io/benchmark-results-of-kubernetes-network-plugins-cni-over-10gbit-s-network-updated-august-2020-6e1b757b9e49
- https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/ (long ago)
- https://caylent.com/understanding-kubernetes-interfaces-cri-cni-csi (mostly by accident because it mentions Kubic in an unrelated paragraph)

## Conclusions

kube-router does seem like it'd be rock-solid and reliable out of simplicity (its [landing page](https://www.kube-router.io/) and [intro](https://www.kube-router.io/docs/introduction/) certainly make it seem that way), but it also seems like there are a lot of switches to fiddle with - [just the kubeadm docs](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kubeadm.md) list two ways to deploy it, and neither one matches [the "quickest way to deploy kube-router"](https://www.kube-router.io/docs/user-guide/#running-as-daemonset) exactly. Looking at the source tree lists [*twelve different configurations*](https://github.com/cloudnativelabs/kube-router/tree/master/daemonset), and even within the differences I *understand* it's not exactly clear which option ought to be used (from what I hear, kube-proxy used to be worse, but works the way kube-router does now). I did confirm that it's [ARM-compatible](https://hub.docker.com/r/cloudnativelabs/kube-router/tags), though.

Calico really seems like the true successor to Flannel - but it does need me to recreate the cluster to assign a pod CIDR (which Weave, of course, didn't need). No big deal, since I'm going to have to rerun it anyway. The [Tigera Operator](https://docs.projectcalico.org/getting-started/kubernetes/quickstart) might make sense here, or at least, as a future component of the core system (keeping the CNI updated with a dedicated operator alongside the gotk for the rest of the system).

[Cilium](https://cilium.io/) is also listed as supported in [Kubic's docs](https://en.opensuse.org/Kubic:kubeadm), and does sound cool (BFP instead of iptables! Touts IPv6 support!), and [is as straightforward to install as Calico](https://docs.cilium.io/en/v1.8/gettingstarted/k8s-install-default/)... [Calico's iptables have been described as a problem](https://mobilabsolutions.com/2019/01/why-we-switched-to-cilium/)...

Oof, cilium doesn't support arm64. That's a dealbreaker. Maybe some day, [if Cilium gets its arm64 shit straightened out](https://github.com/cilium/cilium/issues/9898) - which may even happen by the 1.9 release?

[Network interface rethink](8fw52-4x7mt-4r8vm-jx527-4hxm3)
